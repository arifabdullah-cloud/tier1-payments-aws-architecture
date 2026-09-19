# References

## 1. Purpose

The architecture uses AWS documentation and the AWS Well-Architected Framework as primary technical references.

References are grouped by the architecture decisions they support. Access dates should be recorded as part of final submission if required by the assessment format.

---

## 2. Architecture and Reliability

### AWS Well-Architected Framework

AWS, *AWS Well-Architected Framework*.

https://docs.aws.amazon.com/wellarchitected/latest/framework/welcome.html

Used as the general framework for architecture quality attributes, operational excellence, security, reliability, performance efficiency, cost optimization, and sustainability.

### Reliability Pillar

AWS, *AWS Well-Architected Framework — Reliability Pillar*.

https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/welcome.html

Used to support the design-for-failure approach, failure-domain considerations, resilience testing, and recovery planning.

---

## 3. Multi-Account Governance and Landing Zone

### AWS Control Tower

AWS, *What Is AWS Control Tower?*

https://docs.aws.amazon.com/controltower/latest/userguide/what-is-control-tower.html

Used to support the proposed governed multi-account landing-zone model.

### AWS Control Tower Multi-Account Landing Zone

AWS, *AWS Multi-Account Landing Zone*.

https://docs.aws.amazon.com/controltower/latest/userguide/aws-multi-account-landing-zone.html

Used to support centralized governance with separate workload, security, infrastructure, and shared-service boundaries.

### Service Control Policies

AWS, *Service Control Policies (SCPs)*.

https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps.html

Used to support organization-level permission guardrails. SCPs are treated as permission boundaries rather than mechanisms that directly grant permissions.

### AWS Control Tower Controls

AWS, *AWS Control Tower Controls Reference Guide*.

https://docs.aws.amazon.com/controltower/latest/controlreference/controls.html

Used to support preventive, detective, and proactive governance controls.

---

## 4. Security Architecture

### AWS Shared Responsibility Model

AWS, *Shared Responsibility Model*.

https://aws.amazon.com/compliance/shared-responsibility-model/

Used to distinguish AWS responsibilities from customer responsibilities.

### AWS Security Reference Architecture

AWS, *AWS Security Reference Architecture — Dedicated Accounts*.

https://docs.aws.amazon.com/prescriptive-guidance/latest/security-reference-architecture/dedicated-accounts.html

Used to support dedicated security, log archive, and security-tooling account patterns.

### Security Tooling Account

AWS, *AWS Security Reference Architecture — Security Tooling Account*.

https://docs.aws.amazon.com/prescriptive-guidance/latest/security-reference-architecture/security-tooling.html

Used to support centralized security visibility and delegated security administration.

### AWS CloudTrail

AWS, *What Is AWS CloudTrail?*

https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-user-guide.html

Used to support AWS API and account activity auditing.

### AWS Config

AWS, *What Is AWS Config?*

https://docs.aws.amazon.com/config/latest/developerguide/WhatIsConfig.html

Used to support resource configuration history and compliance evaluation.

### Amazon GuardDuty

AWS, *What Is Amazon GuardDuty?*

https://docs.aws.amazon.com/guardduty/latest/ug/what-is-guardduty.html

Used to support threat detection.

### AWS Security Hub

AWS, *What Is AWS Security Hub CSPM?*

https://docs.aws.amazon.com/securityhub/latest/userguide/what-is-securityhub.html

Used to support centralized security findings and security posture visibility.

### Amazon Inspector

AWS, *What Is Amazon Inspector?*

https://docs.aws.amazon.com/inspector/latest/user/what-is-inspector.html

Used to support applicable vulnerability-management and workload scanning controls.

---

## 5. Application Platform

### AWS Compute Decision Guide

AWS, *Choosing an AWS Compute Service*.

https://docs.aws.amazon.com/decision-guides/latest/decision-guides/choosing-aws-compute-service.html

Used to compare Amazon EC2, container services, AWS Lambda, and other compute models.

### AWS Container Decision Guide

AWS, *Choosing an AWS Container Service*.

https://docs.aws.amazon.com/decision-guides/latest/decision-guides/choosing-aws-container-service.html

Used to support comparison of AWS container-platform alternatives.

### Amazon ECS

AWS, *Amazon ECS Clusters*.

https://docs.aws.amazon.com/AmazonECS/latest/developerguide/clusters.html

Used as a technical reference for the selected ECS orchestration model.

### ECS Shared Responsibility Model

AWS, *AWS Shared Responsibility Model for Amazon ECS*.

https://docs.aws.amazon.com/AmazonECS/latest/developerguide/security-shared-model.html

Used to support the ECS/Fargate responsibility model and the distinction between Fargate-managed compute infrastructure and customer responsibility for application, IAM, network, container image, and data configuration.

### Fargate Task Maintenance

AWS, *Task Retirement and Maintenance for AWS Fargate on Amazon ECS*.

https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task-maintenance.html

Used to support the operational model in which AWS maintains the underlying Fargate infrastructure while applications must remain resilient to task replacement and maintenance events.

### Amazon EKS Security Best Practices

AWS, *Amazon EKS Best Practices Guide — Security*.

https://docs.aws.amazon.com/eks/latest/best-practices/security.html

Used when evaluating the security and operational responsibilities associated with the EKS alternative.

### AWS Fargate or AWS Lambda

AWS, *Choosing Between AWS Fargate and AWS Lambda*.

https://docs.aws.amazon.com/decision-guides/latest/decision-guides/fargate-or-lambda.html

Used to support workload-specific selection between long-running container services and event-driven functions.

---

## 6. Networking and Hybrid Connectivity

### Redundant Hybrid Connectivity

AWS, *AWS Well-Architected Framework — Provision Redundant Connectivity Between Private Networks in the Cloud and On-Premises Environments*.

https://docs.aws.amazon.com/wellarchitected/latest/framework/rel_planning_network_topology_ha_conn_private_networks.html

Used to support resilient hybrid connectivity and the use of redundant connectivity paths.

### Hub-and-Spoke Network Topology

AWS, *AWS Well-Architected Framework — Prefer Hub-and-Spoke Topologies Over Many-to-Many Mesh*.

https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/rel_planning_network_topology_prefer_hub_and_spoke.html

Used to support the Transit Gateway-based network model and explicit routing between permitted network boundaries.

### AWS Direct Connect and IPsec VPN

AWS, *Hybrid Networking Lens — AWS Direct Connect and IPsec VPN*.

https://docs.aws.amazon.com/wellarchitected/latest/hybrid-networking-lens/aws-direct-connect-and-ipsec-vpn.html

Used to support hybrid connectivity design considerations involving Direct Connect and VPN connectivity.

---

## 7. Data and Disaster Recovery

### Amazon Aurora Availability and Durability

AWS, *Amazon Aurora — Availability and Durability*.

https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/Concepts.AuroraHighAvailability.html

Used to support Aurora high-availability and recovery design considerations.

### Aurora Global Database

AWS, *Amazon Aurora Global Database*.

https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-global-database.html

Used to support the proposed cross-Region database disaster-recovery architecture.

### Aurora Global Database Switchover and Failover

AWS, *Using Switchover or Failover in Amazon Aurora Global Database*.

https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/aurora-global-database-disaster-recovery.html

Used to support the regional recovery approach and RPO/RTO analysis.

AWS documents that unplanned Aurora Global Database failover normally has a non-zero RPO because cross-Region replication is asynchronous. This limitation is reflected explicitly in the proposed architecture rather than treating cross-Region replication as guaranteed zero-loss recovery.

### Aurora Replication Options

AWS, *Aurora Global Databases — AWS Prescriptive Guidance*.

https://docs.aws.amazon.com/prescriptive-guidance/latest/aurora-replication-options/aurora-global-database.html

Used to support the distinction between the primary writer Region and asynchronous secondary Region replication.

### Amazon Aurora Backup and Restore

AWS, *Backing Up and Restoring an Amazon Aurora DB Cluster*.

https://docs.aws.amazon.com/AmazonRDS/latest/AuroraUserGuide/BackupRestoreAurora.html

Used to support the separation of database replication from backup and recovery controls.

### AWS Backup

AWS, *What Is AWS Backup?*

https://docs.aws.amazon.com/aws-backup/latest/devguide/whatisbackup.html

Used to support centralized backup policy and recovery-management considerations in the proposed backup and cyber-recovery architecture.

---

## 8. Threat and Control Mapping

The threat-control matrix uses the AWS security and governance references listed above, particularly:

- AWS Shared Responsibility Model;
- AWS Security Reference Architecture;
- AWS CloudTrail;
- AWS Config;
- Amazon GuardDuty;
- AWS Security Hub;
- Amazon Inspector;
- AWS Control Tower; and
- AWS Organizations SCPs.

The threat scenarios themselves are architecture-level scenarios derived from the assessment requirements rather than claims that AWS documentation defines the bank's complete threat model.

---

## 9. Scope of References

AWS documentation is used to validate AWS service behaviour and architecture patterns.

It is not used as evidence of:

- country-specific regulatory compliance;
- legal interpretation;
- suitability for a specific bank without further validation;
- guaranteed application-level availability;
- guaranteed achievement of the stated RTO or RPO; or
- production performance for the payment workload.

Those conclusions require workload testing, regulatory review, security assessment, and organizational validation as described in `07-validation-and-limitations.md`.
