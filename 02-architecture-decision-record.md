# Architecture Decision Record

## ADR-001 — Default Container Platform for Payment Services

**Status:** Proposed

**Decision:** Amazon ECS with AWS Fargate as the default container runtime

**Alternatives considered:** Amazon EKS, Amazon EC2, AWS Lambda

**Decision scope:** Long-running containerized payment application services

---

## 1. Context

The Tier 1 payment platform requires a runtime for long-running application services such as payment initiation, payment validation, payment processing, and status or enquiry APIs.

The platform must support:

- 99.99% availability for defined critical payment journeys;
- deployment across multiple Availability Zones;
- strong security and auditability;
- progressive and controlled application deployment;
- cost transparency;
- application portability;
- integration with AWS networking, identity, observability, and security capabilities; and
- progressive migration from existing workloads.

The assessment permits Amazon EKS, Amazon ECS, AWS Lambda, and Amazon EC2 and explicitly requires application portability not to depend solely on a particular platform product.

No requirement has been provided for Kubernetes APIs, Kubernetes operators, an existing Kubernetes platform standard, or Kubernetes-specific tooling.

The architecture therefore requires a decision on the default runtime without preventing exceptions where individual workload characteristics justify another compute model.

---

## 2. Decision Drivers

The primary decision drivers are:

1. workload suitability for long-running payment APIs and services;
2. availability across multiple Availability Zones;
3. operational complexity;
4. security and isolation;
5. deployment and scaling capabilities;
6. platform-team operational burden;
7. application portability;
8. integration with the AWS environment;
9. cost transparency; and
10. ability to support progressive modernization.

The objective is not to select the platform with the largest feature set. The preferred platform should provide the capabilities required by the workload without introducing operational complexity that has not been justified by a requirement.

---

## 3. Decision

Amazon ECS with AWS Fargate is proposed as the default runtime for long-running containerized payment services.

Amazon ECS provides AWS-native container orchestration, while AWS Fargate provides managed compute capacity without requiring the product or platform team to maintain an EC2 worker fleet for the default runtime.

Critical ECS services will run multiple tasks across Availability Zones according to workload requirements. Application health checks, scaling, load balancing, deployment configuration, dependency resilience, and observability remain part of the application and platform design.

The proposed use of ECS/Fargate does not imply that all payment-platform workloads must use ECS.

AWS Lambda may be used for suitable short-lived or event-driven workloads. Amazon EC2 remains available where legacy or specialized workloads require host-level control. Amazon EKS may be adopted where a demonstrated Kubernetes requirement justifies its additional platform complexity.

---

## 4. Alternative — Amazon EKS

Amazon EKS provides managed Kubernetes and access to the Kubernetes API and ecosystem.

EKS would be preferred if the organization had requirements such as:

- an established enterprise Kubernetes platform standard;
- substantial existing Kubernetes operational capability;
- applications dependent on Kubernetes APIs or operators;
- tooling that requires the Kubernetes ecosystem; or
- a strategic requirement to standardize orchestration interfaces across multiple environments.

Kubernetes can provide greater standardization of orchestration definitions across environments.

However, Kubernetes portability does not automatically provide application portability. Applications may remain dependent on AWS-specific identity, networking, databases, messaging, observability, encryption, and security capabilities.

For the requirements currently provided, no Kubernetes-specific need has been identified. Selecting EKS would therefore introduce additional Kubernetes platform concepts, lifecycle management, security configuration, governance, and skills requirements without a demonstrated workload benefit.

EKS is therefore not selected as the default platform.

---

## 5. Alternative — Amazon EC2

Amazon EC2 provides the greatest level of operating-system and host control among the evaluated options.

It is appropriate where applications require:

- specialized operating-system configuration;
- unsupported runtime dependencies;
- host-level networking or storage behaviour;
- software that cannot currently be containerized; or
- migration with minimal application change.

Using EC2 as the default platform would, however, retain greater responsibility for instance lifecycle, operating-system maintenance, capacity management, security hardening, and related operational activities.

EC2 is therefore retained as an exception and migration path rather than the default modernization target.

---

## 6. Alternative — AWS Lambda

AWS Lambda provides an event-driven execution model with infrastructure scaling managed by AWS.

It is suitable for selected workloads such as:

- short-lived event handlers;
- operational automation;
- selected asynchronous processing; and
- functions that naturally fit an event-driven execution model.

The primary payment services are expected to include long-running APIs and continuously available processing components. Forcing those services into a function-based architecture solely to adopt serverless compute would make the compute platform drive application boundaries.

Lambda is therefore used selectively rather than as the universal payment runtime.

---

## 7. Application Portability

The architecture deliberately separates application portability from orchestration-platform portability.

Containerized payment services should use OCI-compatible images, externalized configuration, documented API and event contracts, controlled dependency management, and version-controlled deployment automation.

Applications should avoid relying on persistent local container state and should expose explicit behaviour for retries, timeouts, idempotency, and failure handling.

AWS-native services may be used where their security, reliability, operational, or cost benefits justify the dependency. Such dependencies must be explicit and documented.

This approach does not claim that an ECS application can move to another platform without engineering effort.

Instead, it seeks to ensure that application code and packaging are not unnecessarily dependent on ECS-specific application behaviour. A containerized service can therefore be adapted to another container runtime or Kubernetes platform without requiring the business application itself to be redesigned solely because ECS was originally selected.

---

## 8. Consequences

### Positive Consequences

- Lower container-platform operational complexity than adopting Kubernetes without a demonstrated requirement.
- No default requirement to maintain an EC2 container-host fleet when Fargate is used.
- Native integration with AWS identity, networking, load balancing, logging, and security capabilities.
- Supports Multi-AZ service deployment and independent scaling of containerized services.
- Allows product teams to focus more operational effort on application behaviour and payment-service reliability.
- Maintains alternative compute paths for workloads that do not fit the default model.

### Negative Consequences

- ECS orchestration definitions are AWS-specific.
- Fargate provides less host-level control than self-managed EC2 capacity.
- Some Kubernetes ecosystem tools and operators cannot be used directly.
- Migration to another orchestration platform would require adaptation of deployment and platform integration.
- AWS-native dependencies outside the compute layer may create additional migration effort.

These consequences are accepted because the current requirements do not demonstrate that Kubernetes portability or host-level control outweigh the operational simplicity of ECS/Fargate.

---

## 9. Risks and Mitigations

| Risk | Mitigation |
|---|---|
| Application becomes unnecessarily coupled to ECS | Use OCI containers, externalized configuration, documented interfaces, and explicit platform adapters where justified. |
| AWS-native dependencies reduce future portability | Document dependencies and evaluate them based on measurable security, reliability, operational, and cost benefits. |
| Fargate limitations emerge for specific workloads | Allow workload-specific use of ECS on EC2, EC2 directly, EKS, or another approved runtime where justified. |
| ECS skills become concentrated in a small platform team | Standardize deployment patterns, documentation, automation, and operational ownership. |
| Cost becomes inefficient for sustained workload profiles | Measure actual workload economics and reconsider capacity model where material savings justify additional operational responsibility. |
| Future enterprise strategy standardizes Kubernetes | Re-evaluate ECS against the new organizational requirement. |

---

## 10. Review Triggers

This decision must be reconsidered if:

- Kubernetes becomes an enterprise platform standard;
- application discovery identifies significant Kubernetes-specific dependencies;
- required tooling depends on Kubernetes APIs or operators;
- Fargate limitations prevent required workload behaviour;
- measured ECS/Fargate cost materially changes the economic trade-off;
- security or regulatory requirements cannot be met through the selected platform;
- workload performance or scaling requirements cannot be achieved;
- organizational platform capabilities materially change; or
- portability requirements are strengthened beyond the assumptions used in this decision.

A review trigger does not automatically require migration away from ECS. It requires the decision to be reassessed using the updated requirements and evidence.

---

## 11. Decision Outcome

Amazon ECS with AWS Fargate is proposed as the default runtime for long-running containerized payment services because it satisfies the currently stated workload requirements without introducing Kubernetes-specific operational complexity that has not been justified.

The architecture retains AWS Lambda, Amazon EC2, and Amazon EKS as workload-specific alternatives rather than treating the default platform as an absolute constraint.

The decision prioritizes workload fit and operational simplicity while addressing application portability through application architecture, contracts, packaging, and automation.
