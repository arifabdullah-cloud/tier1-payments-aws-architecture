# Assumptions and Exclusions

## Purpose

The assessment scenario intentionally leaves several implementation and organizational details unspecified. The following assumptions are used to establish clear architecture boundaries without inventing organization-specific requirements.

These assumptions should be validated during architecture discovery before production implementation.

## Assumptions

### A01 — Countries and Regulatory Requirements

The exact countries, legal entities, and applicable regulatory requirements are not specified.

The architecture therefore assumes that approved AWS Regions and data residency requirements will be determined for each banking entity before production deployment.

The architecture must support isolation of workloads and regulated data according to those approved boundaries.

### A02 — Existing On-Premises Environment

Existing on-premises systems remain operational during the modernization programme.

The target architecture must therefore support hybrid connectivity and coexistence between on-premises and AWS-hosted workloads throughout the migration.

### A03 — Existing Application Architecture

The current payment platform implementation, programming languages, runtime dependencies, and application topology are not specified.

The architecture will therefore avoid assuming that all existing components can immediately be containerized or converted to serverless workloads.

### A04 — Transaction Volume and Performance

Peak transaction volumes, transaction sizes, latency requirements, and growth forecasts are not provided.

The proposed architecture will define scalable patterns, but final capacity, performance, and cost sizing require workload measurements and performance testing.

### A05 — Existing Data Platform

The existing database technology, schema, data volume, and replication capabilities are not specified.

Database technology will therefore be selected based on required transaction semantics, availability, recovery, residency, and operational requirements rather than assumed compatibility with the existing platform.

### A06 — Identity Provider

The bank is assumed to have an existing enterprise identity provider capable of federation with AWS.

The architecture should reuse centralized enterprise identities rather than create independent long-lived administrator identities for normal operations.

### A07 — Platform Ownership

As stated in the assessment, the central platform team owns landing zones and organizational guardrails, while product teams own their applications and Service Level Objectives (SLOs).

The architecture will maintain this separation of responsibility.

### A08 — Availability and Recovery Objectives

The following requirements are treated as hard architecture constraints:

- 99.99% target availability for critical payment journeys
- 30-minute Recovery Time Objective (RTO)
- Near-zero Recovery Point Objective (RPO) for committed payment transactions

Near-zero RPO will not automatically be interpreted as absolute zero data loss across every failure scenario. The credibility and limitations of the recovery design will be explicitly addressed in the Data and Disaster Recovery section.

## Exclusions

The following are outside the scope of this assessment unless required to explain an architecture decision:

- Detailed application source-code design
- Exact infrastructure sizing
- Detailed cost estimates without workload consumption data
- Country-specific regulatory interpretation
- Vendor or commercial contract selection
- Detailed payment business-process design
- Production implementation code
- Detailed operational runbooks

## Validation Requirement

All assumptions should be validated with the relevant business, security, risk, infrastructure, application, and regulatory stakeholders before production implementation.
