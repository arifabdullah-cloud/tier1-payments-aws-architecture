# Validation and Limitations

## 1. Purpose

The proposed architecture is based on the requirements and information available within this assessment.

Architecture decisions must be validated using workload evidence, testing, organizational standards, and applicable regulatory requirements before production implementation.

This document identifies the principal validation activities and known limitations of the proposed design.

---

## 2. Application Platform Validation

Amazon ECS with AWS Fargate is proposed as the default runtime for suitable long-running containerized payment services.

Before production adoption, validate:

- application container compatibility;
- CPU and memory requirements;
- startup and shutdown behaviour;
- scaling characteristics;
- application health-check behaviour;
- deployment and rollback behaviour;
- network and storage requirements;
- runtime security requirements;
- representative performance under expected and peak traffic;
- service quotas; and
- expected cost under sustained and variable workload profiles.

The architecture does not assume that every existing workload can or should run on Fargate.

Workloads that fail these validation criteria require workload-specific reassessment.

---

## 3. Availability Validation

The architecture uses Multi-AZ deployment as the normal high-availability model within the primary AWS Region.

The 99.99% target applies to defined critical payment journeys rather than implying that every individual component independently provides 99.99% availability.

Validation must therefore include:

- identification of critical user and system journeys;
- dependency mapping for each journey;
- availability objectives for required dependencies;
- application timeout and retry behaviour;
- load-balancer and task failure behaviour;
- Availability Zone failure testing;
- database failover behaviour;
- hybrid connectivity failure; and
- failure of required downstream services.

Availability must be measured at the service or journey level rather than inferred solely from individual AWS service characteristics.

---

## 4. Performance and Capacity Validation

The assessment does not include sufficient workload data to establish production capacity.

Representative testing must determine:

- transactions per second;
- concurrent requests;
- peak demand;
- latency requirements;
- database transaction rates;
- connection requirements;
- queue depth and processing rates;
- network throughput;
- scaling response;
- downstream dependency limits; and
- recovery capacity requirements.

Testing should include expected load, peak load, failure conditions, and recovery conditions.

DR capacity must also be validated rather than assuming that the secondary Region can absorb production traffic at the required rate.

---

## 5. Data Architecture Validation

Amazon Aurora PostgreSQL is proposed as the authoritative transactional data store, subject to validation.

Validation must assess:

- existing database engine and compatibility;
- schema complexity;
- transaction semantics;
- isolation requirements;
- query and access patterns;
- connection behaviour;
- data volume and growth;
- migration tooling;
- performance;
- operational requirements;
- backup and restoration;
- encryption requirements; and
- regulatory constraints.

The database choice should be revisited if workload evidence demonstrates that the proposed relational model does not meet the required behaviour.

---

## 6. RPO Validation

The architecture targets near-zero RPO for committed payment transactions.

Aurora Multi-AZ provides resilience within the primary Region, while cross-Region disaster recovery uses asynchronous replication.

Asynchronous replication introduces a residual window in which the most recent committed transactions may not yet exist in the secondary Region when catastrophic regional failure occurs.

The architecture therefore does not claim absolute zero RPO across all regional-failure scenarios.

Validation must include:

- measurement of cross-Region replication lag;
- behaviour during replication interruption;
- database promotion behaviour;
- identification of transactions near the failure boundary;
- reconciliation capability; and
- business acceptance of the resulting residual risk.

If "near-zero" is subsequently defined as requiring guaranteed zero loss of committed transactions across instantaneous regional failure, the architecture must be reconsidered.

---

## 7. RTO Validation

The 30-minute RTO cannot be validated from database promotion time alone.

End-to-end recovery testing must include:

1. incident detection;
2. disaster declaration and decision time;
3. containment of primary writes where possible;
4. assessment of replication state;
5. promotion of the secondary database;
6. startup or scaling of application capacity;
7. validation of identity, networking and security controls;
8. validation of messaging and integration dependencies;
9. critical-journey testing;
10. traffic redirection; and
11. initial transaction reconciliation.

The measured recovery time across this complete sequence must demonstrate that the 30-minute objective is achievable.

---

## 8. Cyber-Recovery Validation

Cross-Region replication is not treated as a substitute for backup.

Logical corruption, malicious administrative activity, or ransomware-related changes may propagate through replication.

Validation must therefore include:

- protected backup creation;
- backup access separation;
- retention controls;
- deletion protection where applicable;
- restoration into an isolated or controlled recovery environment;
- data-integrity validation;
- credential and key dependencies;
- recovery from known-good recovery points; and
- regular restore exercises.

Recovery testing must demonstrate that backups are usable, not merely that backup jobs report successful completion.

---

## 9. Network and Hybrid Validation

The hybrid architecture depends on reliable and controlled communication between AWS and remaining on-premises systems.

Validation must include:

- expected latency;
- required bandwidth;
- route propagation and isolation;
- redundant connectivity behaviour;
- DNS resolution;
- firewall behaviour;
- certificate dependencies;
- failure and reconvergence behaviour;
- dependency timeout behaviour; and
- entity isolation.

A successful network connection alone does not prove application-level resilience.

---

## 10. Security Validation

Security controls require validation against the bank's actual policies, threat model, and regulatory obligations.

Validation should include:

- IAM role and permission review;
- privileged-access paths;
- SCP behaviour;
- account and entity isolation;
- encryption and key ownership;
- secrets handling;
- network controls;
- audit logging;
- security finding aggregation;
- alert ownership and escalation;
- vulnerability management;
- software supply-chain controls;
- backup administration; and
- incident-response integration.

Penetration testing, threat modelling, security architecture review, and applicable compliance assessments should be performed according to organizational policy.

---

## 11. Migration Validation

Each migration wave requires evidence-based exit criteria.

Validation includes:

- functional correctness;
- transaction integrity;
- performance;
- security;
- observability;
- operational readiness;
- rollback readiness;
- data reconciliation;
- unresolved defects;
- dependency readiness; and
- outstanding migration debt.

Migration progression should not depend solely on a planned project date.

---

## 12. Cost Validation

The architecture has not been optimized against a known production workload profile.

Cost validation should include:

- Fargate consumption;
- database capacity;
- cross-AZ and cross-Region data transfer;
- hybrid connectivity;
- NAT and network-processing costs where applicable;
- security and observability services;
- logging volume and retention;
- backup storage;
- DR capacity;
- support requirements; and
- migration-period duplicate infrastructure.

Cost optimization must follow measurement rather than compromise required security, resilience, or transaction integrity without explicit risk acceptance.

---

## 13. Known Limitations

The principal limitations of the assessment are:

1. Existing application internals have not been provided.
2. Production workload volumes and performance characteristics are unknown.
3. The existing database technology and schema are unknown.
4. Exact countries and regulatory requirements are unknown.
5. External dependency SLOs and recovery characteristics are unknown.
6. Existing enterprise security and operational tooling are not fully specified.
7. Near-zero RPO cannot currently be interpreted as guaranteed zero loss during catastrophic regional failure.
8. The architecture has not been benchmarked against representative production traffic.
9. Detailed production cost has not been calculated.
10. Migration tooling cannot be finalized until the source technology and data characteristics are known.

These limitations are explicit to prevent architecture assumptions from being presented as validated production facts.

---

## 14. Architecture Validation Outcome

The proposed architecture should therefore be treated as a target architecture and decision framework rather than an implementation-ready production design.

The architecture becomes progressively more authoritative as assumptions are replaced by evidence from:

- discovery;
- workload measurements;
- proof-of-concept testing;
- security review;
- regulatory validation;
- performance testing;
- resilience exercises;
- DR testing; and
- migration execution.

Material evidence that contradicts an architecture assumption or decision must trigger reassessment rather than being forced into the existing design.
