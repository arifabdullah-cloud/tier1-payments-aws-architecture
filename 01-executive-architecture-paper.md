# Executive Architecture Paper

**Selected Cloud:** Amazon Web Services (AWS)

## Executive Summary

This paper proposes a target architecture for modernization of a multi-country Tier 1 payments platform on Amazon Web Services (AWS).

The architecture is designed around the required 99.99% availability target for critical payment journeys, a 30-minute Recovery Time Objective (RTO), near-zero Recovery Point Objective (RPO) for committed payment transactions, regulatory data residency, banking-entity isolation, hybrid integration with existing on-premises systems, security, auditability, cost transparency, and progressive migration.

The design treats transaction integrity, security, regulatory boundaries, and recoverability as primary architecture concerns. AWS managed services will be used where their operational and resilience benefits justify the dependency, while application portability will be addressed through application architecture, interfaces, data and event contracts, portable packaging, and deployment automation rather than relying on a single compute platform.

---

## 1. Architecture Framing

### 1.1 Architecture Principles

The following principles guide architecture decisions throughout the modernization.

| ID | Principle | Application to the Payments Platform |
|---|---|---|
| P1 | Security and compliance by design | Security, regulatory controls, auditability, and data protection are incorporated into the architecture and delivery lifecycle rather than added after deployment. |
| P2 | Design for failure | Critical payment journeys must tolerate expected infrastructure and application failures without depending on manual recovery for routine failure scenarios. |
| P3 | Preserve transaction integrity | Availability must not be achieved at the expense of payment correctness. Committed transactions must remain durable, traceable, and protected against unintended duplicate processing. |
| P4 | Isolate according to risk and regulatory boundaries | Banking entities, environments, workloads, administrative privileges, and regulated data are separated according to their security and regulatory requirements. |
| P5 | Automate repeatable controls and change | Infrastructure, security controls, configuration, testing, and deployment should be automated and version controlled wherever practical. |
| P6 | Prefer managed services where justified | AWS managed services are preferred where they satisfy security, reliability, operational, portability, and cost requirements. Lower-level control is retained where workload requirements justify it. |
| P7 | Achieve portability through application design | Portability is achieved through portable packaging, documented APIs and event contracts, externalized configuration, deployment automation, and deliberate management of service dependencies rather than assuming that a particular orchestration platform guarantees portability. |
| P8 | Observability by design | Critical journeys must provide sufficient metrics, logs, traces, and events to support SLO measurement, operations, audit, security investigation, and incident response. |
| P9 | Prove recovery and migrate progressively | Recovery capability must be regularly validated. Migration is performed through measurable and reversible stages rather than a single high-risk cutover. |

These principles are informed by the AWS Well-Architected Framework, particularly its Security, Reliability, Operational Excellence, Performance Efficiency, and Cost Optimization pillars. They are adapted to the specific requirements of a regulated Tier 1 payments workload rather than used as generic cloud principles.

### 1.2 Quality Attributes and Hard Constraints

| Attribute / Constraint | Architecture Objective |
|---|---|
| Availability | Target 99.99% end-to-end availability for defined critical payment journeys. Critical components must avoid single points of failure within the intended availability design. |
| Reliability | Preserve correct, durable, and traceable payment state. Retry and recovery mechanisms must not create unintended duplicate financial transactions. |
| Recoverability | Critical payment capabilities must support a 30-minute RTO. Near-zero RPO is required for committed payment transactions, with limitations explicitly identified where this cannot credibly be guaranteed for a given failure scenario. |
| Security | Apply least privilege, strong identity controls, encryption, network protection, secure administration, and preventive, detective, and responsive controls. |
| Auditability | Material administrative, security, configuration, and deployment activities must produce protected and centrally accessible audit evidence. |
| Data Residency | Regulated data must remain within locations approved for the relevant banking entity. Primary, replica, backup, and DR data placement must respect the same requirement. |
| Entity Isolation | Banking entities must be isolated according to agreed security, administrative, data, and regulatory boundaries. |
| Hybrid Integration | The target platform must coexist and integrate with existing on-premises systems during migration. |
| Performance | The architecture must support predictable performance; final latency, throughput, and capacity targets require measured workload characteristics and performance testing. |
| Scalability | The platform must accommodate transaction growth and peak demand without fundamental architectural redesign. |
| Portability | Application design must minimize unnecessary coupling to the compute platform while explicitly documenting justified dependencies on AWS-native services. |
| Operability | Platform and product teams must be able to measure health, SLOs, dependencies, and failure conditions using actionable telemetry. |
| Maintainability | Application and infrastructure changes should be version controlled, testable, repeatable, and reversible where practical. |
| Cost Transparency | Cloud consumption must be attributable to appropriate entities, environments, and workloads. |
| Migration | Modernization must support progressive migration, coexistence, validation, and rollback rather than requiring a single big-bang replacement. |

The 99.99% availability target, 30-minute RTO, near-zero RPO for committed transactions, hybrid integration, entity isolation, data residency, security, auditability, cost transparency, progressive migration, and ownership model are treated as hard architecture constraints.

### 1.3 Decision Ownership

Architecture decisions span multiple organizational responsibilities. The Solution Architect coordinates these requirements and translates them into technical decisions but does not independently own regulatory, business, or enterprise policy decisions.

| Decision Area | Primary Owner | Architecture Responsibility |
|---|---|---|
| Critical business journeys and business criticality | Business / Product | Translate criticality into availability, recovery, performance, and dependency requirements. |
| Regulatory obligations and permitted data locations | Risk / Compliance / Legal | Translate approved boundaries into enforceable technical controls and data placement. |
| Landing zone and organizational guardrails | Central Platform Team | Design workloads within the governed AWS foundation and identify required platform capabilities. |
| Enterprise and hybrid networking | Platform / Network Team | Jointly define resilient connectivity, routing, inspection, and failure behaviour. |
| Security standards and control objectives | Security | Map security requirements to preventive, detective, and responsive architecture controls. |
| Application architecture | Product / Application Team | Define application patterns within enterprise architecture standards and guardrails. |
| Application SLOs | Product Team | Provide architecture and telemetry capable of measuring and meeting defined SLOs. |
| Transaction and data architecture | Product / Data Owners | Define transaction boundaries, consistency, lifecycle, replication, reconciliation, and recovery patterns. |
| Disaster recovery requirements | Business / Risk / Technology | Implement, test, and demonstrate recovery against agreed RTO and RPO objectives. |
| Cloud cost accountability | Product / Platform / FinOps | Provide allocation, visibility, and optimization mechanisms for cloud consumption. |

### 1.4 Assumptions and Exclusions

The scenario intentionally leaves several implementation details unspecified, including the participating countries, approved AWS Regions, transaction volumes, existing database technology, application runtime architecture, and enterprise identity provider.

The architecture therefore distinguishes explicit assessment requirements from assumptions. Unknown workload characteristics will not be replaced with invented capacity or performance figures.

Detailed assumptions, exclusions, and validation requirements are maintained in [Assumptions and Exclusions](06-assumptions-and-exclusions.md).

### 1.5 Architecture Governance

The AWS environment will use a centrally governed multi-account model. AWS Organizations will provide the organizational hierarchy, with Organizational Units (OUs) and AWS accounts used to establish administrative, workload, environment, and entity boundaries.

AWS Control Tower will provide the governed landing-zone foundation. Organizational controls will be applied according to central platform, security, and regulatory requirements. Preventive controls will be used where prohibited actions should be technically restricted, while detective controls will identify configuration non-compliance. Proactive controls may be applied to supported infrastructure definitions before provisioning.

Service Control Policies (SCPs) will be used where organization-level permission boundaries are required. SCPs are treated as permission guardrails rather than permission grants; workload access remains governed through the appropriate AWS Identity and Access Management (IAM) policies and roles.

Significant infrastructure configuration should be managed through approved Infrastructure as Code (IaC) and deployment pipelines wherever practical. This provides version history, peer review, repeatability, automated validation, and traceability between approved change and deployed infrastructure.

Audit and configuration evidence will be centrally retained. AWS CloudTrail will provide evidence of AWS API and account activity, while AWS Config and associated controls can provide resource configuration history and compliance evaluation where applicable.

Material architecture decisions will be documented using Architecture Decision Records (ADRs), including context, decision drivers, alternatives, consequences, risks, and review triggers. Deviations from architecture standards require an explicit exception with an accountable owner, documented rationale, compensating controls where necessary, and a review or expiry condition.

Governance will favor automated controls for deterministic requirements while reserving human architecture review for decisions requiring business, regulatory, security, or technical judgment. This approach is intended to provide strong governance without making the central platform team a deployment bottleneck for product teams.
