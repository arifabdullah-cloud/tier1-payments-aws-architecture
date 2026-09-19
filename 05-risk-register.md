# Risk Register

## 1. Purpose

This register captures material architecture, migration, security, operational, and dependency risks identified for the Tier 1 payment platform modernization.

The register is architecture-level and must be refined as application discovery, regulatory validation, performance testing, and migration planning provide additional evidence.

Risk ratings are indicative rather than substitutes for the bank's formal enterprise risk methodology.

## 2. Risk Register

| ID | Risk | Potential Impact | Likelihood | Impact | Mitigation / Treatment | Owner |
|---|---|---|---|---|---|---|
| R01 | Existing application dependencies are incomplete or poorly understood | Migration failure, unexpected outages, delayed cutover | Medium | High | Complete dependency discovery and critical-journey mapping before migration; validate dependencies during migration waves | Application / Architecture |
| R02 | Existing application cannot be containerized without significant change | Delays adoption of the proposed ECS/Fargate default or requires an alternative runtime | Medium | Medium | Assess runtime during discovery; retain EC2 as an exception/migration path; modernize progressively | Application Team |
| R03 | Cross-Region asynchronous replication does not provide absolute zero RPO during catastrophic Region failure | Latest committed transactions may require reconciliation following regional loss | Low–Medium | Critical | Monitor replication health and lag; maintain transaction IDs and reconciliation capability; validate business interpretation of "near-zero RPO"; revisit architecture if absolute zero-loss is mandatory | Architecture / Data / Business |
| R04 | DR dependencies are incomplete even when the database is recoverable | 30-minute RTO may not be achieved | Medium | High | Include application, identity, networking, messaging, integrations, DNS and operational dependencies in DR testing; measure actual recovery time | Platform / Product / Operations |
| R05 | Data migration introduces missing, duplicated, or inconsistent payment records | Financial integrity and regulatory impact | Medium | Critical | Controlled migration; validation and reconciliation; explicit authoritative system; idempotency; cutover gates; defined post-cutover reconciliation | Data / Product |
| R06 | Hybrid connectivity failure disrupts dependencies remaining on-premises | Payment processing degradation or outage | Medium | High | Resilient connectivity; redundant paths where justified; dependency timeouts/retries; failure testing; progressive removal of hybrid dependencies | Network / Platform |
| R07 | Banking entities are insufficiently isolated | Unauthorized cross-entity access or regulatory breach | Low | Critical | Separate workload accounts and VPCs; explicit routing; IAM boundaries; KMS controls; SCP guardrails; security validation | Platform / Security |
| R08 | Privileged identity compromise leads to destructive or unauthorized activity | Service disruption, data exposure, data corruption | Medium | Critical | Federation, MFA, temporary credentials, least privilege, separation of duties, centralized audit, threat detection, protected backups and incident-response procedures | Security / Platform |
| R09 | Software supply-chain compromise introduces malicious or vulnerable artifacts | Compromise of production payment workloads | Medium | High | Controlled source and build pipeline; dependency/image scanning; protected ECR repositories; restricted deployment roles; traceable artifact promotion | Product / Security |
| R10 | Security or regulatory requirements differ by country | Target architecture may be non-compliant in a particular jurisdiction | Medium | Critical | Validate residency, encryption, logging, access and recovery requirements for each entity before deployment; isolate entities where required | Risk / Compliance / Architecture |
| R11 | Fargate limitations or economics are unsuitable for a workload | Performance, functionality or cost targets may not be met | Medium | Medium | Benchmark representative workloads; measure cost; allow ECS on EC2, EC2 or EKS where justified; use ADR review triggers | Platform / Product |
| R12 | Platform portability is overestimated | Future migration requires more engineering effort than expected | Medium | Medium | OCI images, external configuration, documented contracts, IaC, explicit AWS dependencies; avoid claims of complete cloud independence | Architecture / Product |
| R13 | Traffic rollback occurs after the target has committed financial transactions | Inconsistent transaction state between old and new platforms | Medium | Critical | Separate traffic rollback from transaction/data rollback; explicit system-of-record ownership; reconciliation and controlled cutover procedures | Product / Data / Operations |
| R14 | Replication propagates corruption or malicious changes to the DR environment | Primary and DR copies become unusable | Low–Medium | Critical | Independent protected backups; restricted backup administration; corruption detection; isolated recovery procedures; restore testing | Data / Security |
| R15 | Observability gaps prevent rapid diagnosis during incidents | Increased outage duration and failure to meet RTO/SLO objectives | Medium | High | Define application and infrastructure telemetry before migration; centralized monitoring; tracing; SLOs; operational exercises | Product / Operations |
| R16 | AWS service quotas or capacity assumptions are not validated | Scaling or recovery operations fail during high demand or DR | Low–Medium | High | Capacity modelling; quota review; representative load testing; DR capacity validation; request required quota increases before production | Platform |
| R17 | Migration-era components become permanent technical debt | Increased complexity, attack surface and operational cost | High | Medium | Record migration debt with owner, risk, removal condition and review/expiry date; include debt closure in migration exit criteria | Architecture / Platform |
| R18 | Cost allocation is insufficient across entities or products | Poor cost transparency and inability to manage cloud consumption effectively | Medium | Medium | Account boundaries, tagging standards, cost allocation, budgets and ownership; review consumption during migration | Platform / FinOps |
| R19 | Skills and operating model do not mature with the new platform | Increased incidents, slow recovery and excessive dependency on a small number of specialists | Medium | High | Training, standardized platform patterns, documentation, runbooks, operational exercises and explicit ownership | Engineering Management / Platform |
| R20 | Legacy system is decommissioned before target stability is proven | Loss of fallback capability and increased business risk | Low–Medium | Critical | Defined stabilization period; measurable decommission exit criteria; business/technology approval; retain required recovery and archival data | Program / Product / Operations |

## 3. Risk Treatment Principles

Risks are managed using four broad treatment approaches:

- **Mitigate** — introduce controls that reduce likelihood or impact.
- **Avoid** — change the architecture or migration approach where the risk is unacceptable.
- **Transfer** — allocate responsibility contractually or through an appropriate service where applicable, without assuming accountability disappears.
- **Accept** — formally acknowledge residual risk when further mitigation is not proportionate or technically achievable.

Critical risks affecting payment integrity, regulatory compliance, security, or recovery objectives require explicit stakeholder review before the relevant production migration gate.

## 4. Residual Risk

The target architecture reduces risk but does not eliminate it.

In particular, asynchronous cross-Region replication leaves a residual possibility that the most recent committed transactions have not reached the secondary Region when a catastrophic regional failure occurs.

The architecture therefore describes the recovery objective as **near-zero RPO** rather than guaranteeing zero data loss.

Transaction identification, replication monitoring, controlled failover, and reconciliation reduce the business impact of this residual risk.

If the business requirement is subsequently clarified as absolute zero loss of committed transactions across instantaneous regional failure, the data architecture and associated consistency, latency, availability, and cost trade-offs must be reconsidered.

## 5. Risk Review

The register should be reviewed:

- after application and dependency discovery;
- after regulatory requirements are validated;
- before each significant migration wave;
- following performance and resilience testing;
- following DR exercises;
- before authoritative data cutover;
- after material architecture changes; and
- before legacy decommissioning.

Risks that cannot be sufficiently mitigated at architecture level must be escalated through the bank's applicable risk-acceptance process.
