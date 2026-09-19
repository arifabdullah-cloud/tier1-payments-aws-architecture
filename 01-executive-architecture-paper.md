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

## 2. Target Architecture

### 2.1 Architecture Overview

The target architecture uses a governed AWS multi-account model with explicit separation between central security, infrastructure, shared services, and banking-entity workloads. Critical payment workloads are designed for Multi-AZ operation within an approved AWS Region, while hybrid connectivity supports coexistence with existing on-premises systems throughout the migration.

The architecture separates four concerns:

- **Governance:** organizational structure, account boundaries, guardrails, and centralized controls.
- **Connectivity and security:** hybrid connectivity, network segmentation, inspection, and controlled ingress.
- **Application and data:** payment services, transactional data, APIs, and synchronous/asynchronous integration.
- **Operations and delivery:** observability, audit, security monitoring, Infrastructure as Code (IaC), and controlled CI/CD.

The logical architecture is shown below.

![Logical Architecture](diagrams/02-logical-architecture.png)

The logical view intentionally leaves the payment compute platform and transactional database implementation open. The compute platform is evaluated in Section 3, while the transactional data and disaster recovery strategy is evaluated in Section 4.

### 2.2 Multi-Account and Entity Isolation

AWS Organizations provides the organizational foundation for the target environment. AWS accounts are used as security, access, billing, and blast-radius boundaries rather than placing all workloads into a single shared account.

The proposed organizational model separates:

- central security tooling and log archival;
- central network and shared services;
- production and non-production workloads; and
- banking entities where separate security, regulatory, administrative, or data boundaries are required.

A conceptual structure is:

    AWS Organization
    ├── Security OU
    │   ├── Security Tooling
    │   └── Log Archive
    │
    ├── Infrastructure OU
    │   ├── Network
    │   └── Shared Services
    │
    └── Workloads OU
        ├── Entity A
        │   ├── Production
        │   └── Non-Production
        │
        └── Entity B
            ├── Production
            └── Non-Production

The entity names above are illustrative rather than assumptions about the bank's actual legal structure.

Entity isolation is not limited to network segmentation. Depending on approved regulatory and security requirements, isolation may include AWS accounts, VPCs, IAM roles, encryption keys, data stores, deployment permissions, logging boundaries, and cost allocation.

Direct communication between entity workloads is not assumed. Where cross-entity communication is required, it must use an explicitly approved integration path with appropriate authorization, logging, and security controls.

### 2.3 Availability and Failure Domains

Critical payment services are designed to operate across multiple Availability Zones within the selected primary AWS Region.

Application capacity will be distributed so that the loss of a single Availability Zone does not inherently result in loss of the critical payment journey. Selected stateful AWS services must similarly provide an appropriate Multi-AZ resilience model.

Multi-AZ architecture provides high availability against localized infrastructure failures but is not treated as a complete regional disaster recovery strategy. Regional disaster recovery, replication, failover, and recovery limitations are addressed separately in Section 4.

The architecture does not assume that 99.99% availability requires active-active operation across multiple AWS Regions. The availability target applies to defined critical payment journeys and must be validated through end-to-end dependency analysis, failure testing, monitoring, and SLO measurement.

### 2.4 Network and Hybrid Connectivity

Each banking-entity workload is hosted within an appropriately isolated Amazon VPC. Workload resources are distributed across Availability Zones using separate subnets and routing according to their function and security requirements.

Inter-VPC and hybrid connectivity uses a centrally governed transit model based on AWS Transit Gateway rather than independent point-to-point connectivity between every workload network.

The central Network account owns the shared connectivity layer. Transit Gateway routing is used to control connectivity between workload VPCs, shared services, security inspection capabilities, and approved on-premises networks.

Hybrid connectivity is designed around resilient enterprise connectivity. AWS Direct Connect is used as the primary private connectivity mechanism where available and justified, with redundant connectivity and/or AWS Site-to-Site VPN providing additional resilience according to the final network design.

Traffic crossing defined network trust boundaries is routed through appropriate inspection controls. AWS Network Firewall may provide centralized network inspection where required, while Security Groups provide workload-level network access controls.

Entity-to-entity routing is not enabled by default. Connectivity is explicitly introduced only for approved dependencies.

Detailed Direct Connect topology, bandwidth, BGP configuration, carrier diversity, and physical connectivity cannot be finalized without the bank's existing network architecture and connectivity requirements.

### 2.5 Application and Integration Architecture

External and internal consumers access the payment platform through controlled API interfaces.

Amazon Route 53 provides DNS and traffic-routing capabilities, while AWS WAF provides application-layer protection for supported HTTP/S entry points. Amazon API Gateway provides a managed API entry layer for appropriate payment APIs and backend integrations.

The underlying application runtime remains deliberately undecided in this section. Amazon ECS, Amazon EKS, AWS Lambda, and Amazon EC2 are evaluated against representative payment-platform components in Section 3.

Application integration uses both synchronous and asynchronous communication patterns.

Synchronous communication is retained where an immediate response is required as part of the critical transaction path. Asynchronous integration is preferred for activities that do not need to block payment completion, reducing unnecessary runtime coupling between payment processing and secondary consumers.

Amazon EventBridge and Amazon SQS may be used for event routing and durable asynchronous processing where their respective behavior fits the integration requirement.

For example, a committed payment may produce a payment-completed event for downstream notification, reporting, analytics, or other consumers without requiring all of those consumers to be available before the critical transaction can complete.

Events and queues do not replace transaction integrity mechanisms. Reliable coordination between committed transactional state and event publication is addressed as part of the data architecture in Section 4.

Hybrid integration provides controlled connectivity to existing on-premises systems during coexistence and migration. The design does not assume the implementation technology of those existing systems.

### 2.6 Identity, Encryption and Secrets

Workforce access to AWS is federated with the bank's enterprise identity provider through a centrally managed identity model, such as AWS IAM Identity Center, subject to validation against the existing identity architecture.

AWS IAM roles and policies provide workload and administrative authorization according to least-privilege principles. Long-lived application credentials are avoided where AWS workload identities and temporary credentials can be used.

Deployment pipelines use controlled deployment roles scoped to the relevant workload and environment. A deployment identity for one banking entity must not implicitly provide deployment privileges to another entity.

Sensitive application secrets are managed separately from application source code using an approved secrets-management capability such as AWS Secrets Manager.

AWS Key Management Service (AWS KMS) provides managed encryption-key capabilities for supported workloads and data services. Key ownership and separation will follow the required entity, regulatory, and data-classification boundaries.

Data is encrypted in transit and at rest using controls appropriate to the selected services and regulatory requirements.

### 2.7 Observability, Audit and Security Monitoring

Observability is designed around the critical payment journey rather than infrastructure health alone.

Application and platform telemetry includes:

- metrics;
- structured logs;
- distributed traces where appropriate;
- alarms and operational events; and
- business/service indicators required to measure defined SLOs.

Amazon CloudWatch provides native AWS monitoring, logging, and alerting capabilities. Application telemetry should support correlation across distributed components without unnecessarily recording sensitive payment or authentication data.

AWS CloudTrail provides audit evidence of relevant AWS API and account activity. AWS Config can provide resource configuration history and compliance evaluation for supported resources.

Security findings are centrally visible to the security function. Services such as Amazon GuardDuty, Amazon Inspector, AWS Config, and AWS Security Hub may contribute to organization-wide detection and security posture management according to the final security control design.

Long-term audit and security logs are separated from workload administration through centralized security and log-archive capabilities. Log centralization must itself respect applicable data residency and sensitive-data requirements.

Product teams remain responsible for application telemetry and SLOs, while central platform and security teams provide organization-wide logging, security monitoring, governance, and shared operational capabilities.

### 2.8 Deployment and Delivery

Application and infrastructure changes are delivered through controlled CI/CD pipelines rather than routine manual production changes.

The delivery flow follows the pattern:

    Source Control
         ↓
    Build and Test
         ↓
    Security / Dependency Checks
         ↓
    Approved Artifact
         ↓
    Controlled Deployment
         ↓
    Workload Environment

For containerized components, Amazon Elastic Container Registry (ECR) provides a managed container-image registry. Container images are scanned according to the required vulnerability-management policy before promotion to production.

Infrastructure changes are managed through approved Infrastructure as Code where practical, enabling version control, peer review, repeatability, validation, and traceability.

The architecture does not prescribe a specific enterprise source-control or CI/CD product because the bank's existing development toolchain is not provided. Existing enterprise tooling may be retained where it satisfies the required identity, security, approval, audit, and deployment controls.

Production releases should support progressive deployment and controlled rollback where technically feasible. Application rollback must be distinguished from database and transactional-state recovery, because a software version can often be reverted more easily than committed financial data.

The resulting delivery model combines centralized platform guardrails with decentralized application ownership: the central platform team defines the governed AWS foundation, while product teams build, deploy, observe, and operate their applications within those boundaries.
