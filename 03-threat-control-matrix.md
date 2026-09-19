# Threat and Control Matrix

## 1. Purpose

This document maps representative threats to the Tier 1 payment platform against preventive, detective, and responsive controls.

The control model assumes that no individual security control is sufficient. Preventive controls reduce the likelihood or impact of a threat, detective controls identify suspicious or non-compliant activity, and responsive controls provide containment, investigation, recovery, and remediation.

The matrix focuses on architecture-level threats relevant to the proposed AWS payment platform. Detailed control implementation must be aligned with the bank's security policies, regulatory obligations, threat model, and incident-response procedures.

## 2. Threat and Control Matrix

| Threat | Example Scenario / Impact | Preventive Controls | Detective Controls | Responsive Controls |
|---|---|---|---|---|
| **Privileged credential compromise** | An attacker obtains administrative credentials and attempts to modify payment infrastructure, security controls, or data. | Federated workforce identity; MFA; IAM Identity Center; least-privilege IAM roles; temporary credentials; separation of duties; SCP guardrails; restricted privileged access. | CloudTrail activity; GuardDuty findings; Security Hub correlation; authentication and authorization monitoring; alerts on high-risk administrative actions. | Revoke sessions and credentials; disable or restrict compromised identity; isolate affected resources; preserve evidence; investigate CloudTrail activity; validate or restore modified resources; rotate affected secrets and credentials. |
| **Unauthorized or malicious deployment** | Unapproved or compromised code is deployed into the production payment environment. | Protected source branches; peer review; controlled CI/CD; deployment roles; segregation of duties; approval gates; artifact scanning; environment-specific permissions; IaC controls. | Pipeline and deployment audit logs; CloudTrail; configuration monitoring; application health and behavioural monitoring; alerts on unauthorized infrastructure changes. | Stop deployment; revoke deployment access; rollback to a known approved application version where safe; investigate source and pipeline activity; validate transactional state separately from application rollback. |
| **Software supply-chain compromise** | A malicious or vulnerable dependency, base image, build component, or container artifact enters the delivery chain. | Approved dependency and base-image sources; dependency scanning; container image scanning; controlled Amazon ECR repositories; restricted build permissions; immutable/versioned artifacts; build from controlled source. | Amazon Inspector and pipeline vulnerability findings; ECR image scanning; security findings; monitoring for unexpected runtime behaviour. | Block or quarantine affected artifacts; identify impacted deployments; rebuild from trusted source and dependencies; redeploy approved artifact; rotate credentials if exposure is suspected; perform incident investigation. |
| **Payment data exfiltration** | An attacker or unauthorized user attempts to access or export regulated payment information. | Least-privilege IAM; account and VPC isolation; Security Groups; controlled network paths; encryption in transit and at rest; KMS key controls; secrets management; data minimization. | CloudTrail; GuardDuty; network/security telemetry; unusual access-pattern monitoring; application audit logs; security findings. | Revoke access; isolate affected workload or identity; block malicious network paths; preserve evidence; determine data exposure; rotate keys or credentials where required; execute regulatory and incident-response procedures. |
| **Destructive privileged action / ransomware** | A compromised identity or malicious administrator deletes or corrupts application resources, databases, logs, or backups. | Separation of duties; least privilege; SCP guardrails; protected backup administration; encryption; restricted deletion permissions; independent backup retention; controlled production access. | CloudTrail; Config; GuardDuty; Security Hub; alarms for destructive or unusual administrative actions; backup monitoring. | Restrict compromised identities; isolate affected environments; preserve evidence; prevent further replication of corruption where possible; recover from protected backups into a controlled environment; validate data integrity before production restoration. |
| **Network intrusion or unauthorized lateral movement** | A compromised workload attempts to reach payment services, another banking entity, or sensitive shared services. | Account/VPC segmentation; Transit Gateway route separation; Security Groups; AWS Network Firewall where required; AWS WAF for supported web/API traffic; no default entity-to-entity connectivity. | GuardDuty; VPC/network telemetry; Network Firewall logs; WAF logs; application and infrastructure alerts. | Block malicious traffic; modify security or routing controls; isolate compromised workloads; revoke associated credentials; investigate affected systems; rebuild compromised compute from approved artifacts where required. |
| **Application/API abuse** | Automated or malicious clients attempt unauthorized requests, excessive traffic, malformed input, or exploitation of exposed APIs. | Authentication and authorization; API Gateway controls; AWS WAF; request validation; throttling/rate controls where appropriate; secure coding and API design. | WAF and API logs; CloudWatch metrics and alarms; application security telemetry; anomalous request-rate and error monitoring. | Block or rate-limit offending sources; revoke compromised identities/tokens; isolate affected services where necessary; deploy corrective application or WAF controls; investigate exploitation attempts. |
| **Duplicate or replayed payment processing** | A client, integration, or retry mechanism submits the same logical payment more than once, risking duplicate financial execution. | Stable payment/idempotency identifiers; idempotent API and processing design; transactional state controls; explicit transaction-state transitions. | Duplicate identifier monitoring; reconciliation; application telemetry; unexpected transaction-pattern alerts. | Suppress duplicate processing; place ambiguous transactions into controlled review; reconcile authoritative and external state; correct downstream state through approved business procedures. |
| **Loss or manipulation of audit evidence** | An attacker attempts to alter or delete activity records to conceal unauthorized actions. | Centralized log archive; separation of workload and log-administration privileges; restricted deletion access; encryption; retention controls; protected storage configuration. | Monitoring of logging configuration; AWS Config; CloudTrail; alerts for logging changes or disabled controls. | Restrict affected identities; restore required logging configuration; preserve remaining evidence; investigate the gap; recover retained evidence where possible; escalate according to incident procedures. |
| **Security-control drift / misconfiguration** | A workload is deployed or changed outside the approved security baseline. | IaC; Control Tower controls; SCPs; approved templates; CI/CD validation; peer review; automated policy checks where supported. | AWS Config; Control Tower detective controls; Security Hub findings; configuration and compliance reporting. | Remediate configuration automatically where safe or through controlled change; restrict non-compliant resources; document exceptions; investigate repeated or unauthorized drift. |
| **Denial of service / resource exhaustion** | Traffic spikes or malicious activity reduce availability of critical payment APIs or exhaust downstream resources. | AWS WAF and applicable AWS DDoS protections; API throttling; scalable application capacity; load balancing; quotas and resource limits; resilient dependency design. | CloudWatch metrics and alarms; WAF telemetry; service quota monitoring; application latency/error SLOs; security findings. | Apply traffic controls; scale eligible capacity; block abusive traffic; protect constrained dependencies; activate incident procedures; communicate degraded service according to operational policy. |
| **Secrets or credential exposure** | Application credentials, API secrets, or tokens are committed to source control, exposed in images, or obtained from a compromised workload. | AWS Secrets Manager; IAM roles and temporary credentials; secret scanning; restricted secret access; no embedded production secrets in source or images. | Repository/pipeline secret scanning; CloudTrail secret-access activity; GuardDuty/security findings; application security monitoring. | Revoke and rotate exposed credentials immediately; identify affected workloads and access; redeploy with replacement credentials; investigate usage during the exposure window. |

## 3. Privileged Access

Privileged access is treated as a high-risk security path rather than normal user access.

Routine administration should use federated identities, MFA, temporary credentials, and role-based access. Persistent individual administrative credentials should be avoided.

Privileges should be scoped by account, environment, entity, and administrative responsibility. Access to production payment workloads must not automatically provide equivalent access to security tooling, centralized logs, backup administration, or another banking entity.

High-risk administrative actions must generate audit evidence and appropriate security alerts.

Emergency or break-glass access requires tightly controlled credentials, explicit activation procedures, enhanced monitoring, post-use credential rotation where appropriate, and mandatory review of activity performed during the emergency session.

## 4. Software Supply-Chain Security

The delivery pipeline is part of the production security boundary.

Source code, infrastructure definitions, dependencies, build environments, container images, and deployment credentials must therefore be protected against unauthorized modification.

Artifacts should be built through controlled pipelines and promoted between environments rather than rebuilt independently for production. This provides stronger traceability between reviewed source, tested artifact, and deployed version.

Container and dependency scanning reduces known vulnerability risk but does not prove that an artifact is trustworthy. Access controls, source review, build integrity, artifact provenance, runtime monitoring, and incident response remain necessary.

Production deployment permissions should be separated from ordinary developer access and scoped to the required application, account, and environment.

## 5. Security Operations Model

Security findings from workload accounts should be centrally visible to the security function while respecting applicable data-residency restrictions.

Representative AWS capabilities include:

- **AWS CloudTrail** for AWS API and account activity;
- **AWS Config** for configuration history and compliance evaluation;
- **Amazon GuardDuty** for threat detection;
- **Amazon Inspector** for supported vulnerability-management use cases;
- **AWS Security Hub** for centralized security findings and posture visibility;
- **Amazon CloudWatch** for workload and operational telemetry;
- **AWS WAF** for supported web/API application-layer controls; and
- **AWS Network Firewall** where centralized network inspection is required.

These services support the control model but do not replace operational ownership. Findings require defined severity, ownership, escalation, containment, investigation, remediation, and closure procedures.

## 6. Control Ownership

Security responsibilities remain distributed across organizational functions.

The central security team owns enterprise security policy, monitoring standards, security investigation, and organization-wide security visibility.

The central platform team owns landing-zone controls, account provisioning, shared infrastructure, organizational guardrails, and platform security capabilities.

Product teams remain responsible for secure application design, dependency management, application telemetry, vulnerability remediation, and correct use of platform security capabilities.

Risk, compliance, and legal functions determine applicable regulatory and control requirements. Architecture translates those requirements into technical controls but does not independently define regulatory obligations.

## 7. Validation and Limitations

This matrix is an architecture-level threat and control model rather than a complete enterprise threat assessment.

Before production implementation it must be validated against:

- the bank's formal threat model;
- applicable regulatory requirements;
- security policies and control frameworks;
- payment-specific fraud and financial-crime controls;
- existing SOC and incident-response capabilities;
- enterprise identity and privileged-access architecture;
- software-development and supply-chain standards; and
- country-specific data-residency and logging restrictions.

Detailed control configuration, alert thresholds, retention periods, incident severity definitions, and operational procedures require implementation-specific design.
