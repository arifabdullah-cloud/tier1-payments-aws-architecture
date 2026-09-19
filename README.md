# Tier 1 Payments Platform — AWS Target Architecture

## Overview

This repository contains a proposed target architecture and modernization approach for a Tier 1 banking payment platform operating across multiple banking entities and jurisdictions.

The architecture addresses:

- 99.99% availability for critical payment journeys;
- 30-minute recovery time objective (RTO) for critical payment capabilities;
- near-zero recovery point objective (RPO) for committed payment transactions;
- hybrid integration with existing on-premises systems;
- isolation between banking entities;
- regulated-data residency requirements;
- security and auditability;
- application portability;
- progressive migration; and
- cost transparency.

The proposal uses AWS as the target cloud environment while treating workload requirements, transaction integrity, operational simplicity, and explicit trade-offs as the primary architecture decision drivers.

---

## Key Architecture Decisions

### Multi-Account Governance

AWS Organizations and AWS Control Tower provide the governance foundation.

Separate accounts are used as security, operational, billing, and blast-radius boundaries. Banking entities are isolated into appropriate workload accounts rather than relying solely on network segmentation within a shared account.

Central platform teams own landing-zone capabilities and organizational guardrails, while product teams retain responsibility for their applications and SLOs.

### Application Platform

Amazon ECS with AWS Fargate is proposed as the default runtime for suitable long-running containerized payment services.

AWS Lambda is used selectively for suitable event-driven workloads, while Amazon EC2 remains available for legacy or specialized workloads requiring host-level control.

Amazon EKS was evaluated but is not selected as the default because the stated requirements do not establish a Kubernetes-specific dependency or enterprise Kubernetes standard.

Application portability is addressed through OCI container packaging, externalized configuration, documented interfaces, automation, and explicit management of platform dependencies rather than by claiming complete cloud independence.

### Transactional Data

Amazon Aurora PostgreSQL is proposed as the authoritative relational transaction store, subject to validation against the existing database, schema, transaction semantics, access patterns, and workload characteristics.

Transaction integrity is supported through explicit transaction identifiers, idempotent processing, controlled transaction-state transitions, and a transactional outbox pattern for reliable publication of asynchronous work.

### Availability and Disaster Recovery

Multi-AZ deployment provides normal high availability within the primary AWS Region.

A secondary approved AWS Region provides disaster-recovery capability. Aurora Global Database provides asynchronous cross-Region replication, with controlled promotion and application recovery during a regional disaster.

Because cross-Region replication is asynchronous, the architecture describes the RPO objective as near-zero rather than guaranteeing zero data loss across every catastrophic regional-failure scenario.

Replication is not treated as a substitute for backup. Independent backup and cyber-recovery controls are required for corruption, malicious administrative activity, and destructive events.

### Hybrid Connectivity

AWS Transit Gateway provides centralized transit between approved network boundaries.

AWS Direct Connect is the preferred primary private connectivity mechanism where justified, with redundant connectivity and/or AWS Site-to-Site VPN used according to availability requirements.

Banking entities do not receive default connectivity to one another.

### Security

The architecture applies defense in depth across identity, accounts, network, applications, data, delivery pipelines, and operations.

Security controls are considered across:

- preventive controls;
- detective controls; and
- responsive controls.

Particular attention is given to privileged access, software supply-chain risk, payment-data protection, destructive administrative activity, entity isolation, audit evidence, and security-control drift.

---

## Architecture Diagrams

The following diagrams provide progressively more detailed views of the architecture.

| Diagram | Purpose |
|---|---|
| [`context-diagram.png`](diagrams/01-context-diagram.png) | Shows the payment platform, users, internal systems, external systems, and organizational actors. |
| [`logical-architecture.png`](diagrams/02-logical-architecture.png) | Shows logical application, integration, data, security, observability, and delivery capabilities. |
| [`deployment-architecture.png`](diagrams/03-deployment-architecture.png) | Shows the proposed AWS organization, accounts, network boundaries, failure domains, security controls, hybrid connectivity, and workload deployment. |
| [`dr-architecture.png`](diagrams/04-dr-architecture.png) | Shows the primary/secondary Region recovery model, transactional data replication, failover sequence, reconciliation, and cyber-recovery considerations. |

---

## Deliverables

| Document | Purpose |
|---|---|
| [`01-executive-architecture-paper.md`](01-executive-architecture-paper.md) | Architecture framing, target architecture, application-platform decision, and data/DR architecture. |
| [`02-architecture-decision-record.md`](02-architecture-decision-record.md) | ADR documenting the ECS/Fargate default and alternatives including EKS, EC2, and Lambda. |
| [`03-threat-control-matrix.md`](03-threat-control-matrix.md) | Maps representative threats to preventive, detective, and responsive controls. |
| [`04-migration-roadmap.md`](04-migration-roadmap.md) | Defines progressive migration waves, coexistence, dependencies, exit criteria, rollback considerations, and migration-debt controls. |
| [`05-risk-register.md`](05-risk-register.md) | Records material architecture, migration, security, data, operational, and dependency risks. |
| [`06-assumptions-and-exclusions.md`](06-assumptions-and-exclusions.md) | Identifies assumptions required to develop the architecture and areas intentionally outside the assessment scope. |
| [`07-validation-and-limitations.md`](07-validation-and-limitations.md) | Identifies architecture limitations and evidence required before production implementation. |
| [`08-references.md`](08-references.md) | Provides supporting AWS documentation and architecture references. |
| [`09-ai-assistance-disclosure.md`](09-ai-assistance-disclosure.md) | Describes the use of generative AI during preparation of the assessment. |

---

## Migration Approach

Modernization is progressive rather than a big-bang migration.

The proposed sequence is:

    Wave 0 — Discovery and Baseline

                    ↓

    Wave 1 — Cloud Foundation and Guardrails

                    ↓

    Wave 2 — Connectivity, Security and Observability

                    ↓

    Wave 3 — Low-Risk / Peripheral Capabilities

                    ↓

    Wave 4 — Payment Services and Hybrid Coexistence

                    ↓

    Wave 5 — Transactional Data Migration

                    ↓

    Wave 6 — Controlled Production Cutover

                    ↓

    Wave 7 — Stabilization and Legacy Decommissioning

Each wave requires evidence-based exit criteria before progression.

Application rollback, traffic rollback, data rollback, and correction of committed financial transactions are treated as separate concerns.

---

## Architecture Principles

The proposal is guided by the following principles:

1. Security and compliance by design.
2. Design for failure.
3. Preserve transaction integrity.
4. Isolate by risk and regulatory boundary.
5. Automate repeatable controls and change.
6. Prefer managed services where justified.
7. Preserve application portability through application design.
8. Build observability into the platform.
9. Prove recovery and migrate progressively.

---

## Important Assumptions

The assessment does not provide complete information about:

- existing application internals;
- current database technology and schema;
- transaction volumes and performance characteristics;
- exact countries and regulatory requirements;
- existing enterprise identity and security tooling;
- external dependency SLOs; or
- detailed migration constraints.

The proposed architecture therefore represents a target architecture and decision framework rather than an implementation-ready production design.

Material assumptions require validation during discovery and detailed design.

See [`06-assumptions-and-exclusions.md`](06-assumptions-and-exclusions.md) and [`07-validation-and-limitations.md`](07-validation-and-limitations.md) for details.

---

## Validation Approach

Architecture decisions should be validated using evidence from:

- application and dependency discovery;
- workload measurements;
- proof-of-concept testing;
- performance and capacity testing;
- security and threat-model review;
- regulatory validation;
- Availability Zone failure testing;
- regional DR exercises;
- backup restoration;
- transaction reconciliation;
- migration execution; and
- operational readiness exercises.

Material evidence that contradicts an architecture assumption or decision should trigger reassessment rather than being forced into the existing design.

---

## References

AWS documentation is used to validate relevant AWS service behaviour and architecture patterns.

See [`08-references.md`](08-references.md) for the supporting references.

---

## AI Assistance

Generative AI was used as a supporting tool during preparation of this assessment.

See [`09-ai-assistance-disclosure.md`](09-ai-assistance-disclosure.md) for the disclosure.
