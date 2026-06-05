# Production Readiness Checklist

This checklist validates whether a service is technically and operationally ready for production traffic.

## 1. Ownership & Documentation

- [ ] Primary and secondary owner/team defined.
- [ ] Service description and business impact documented.
- [ ] Architecture diagram available and updated.
- [ ] On-call rotation established and communicated.
- [ ] Operational runbook exists (common issues, mitigation).

## 2. Deployment & Configuration

- [ ] CI/CD pipeline is fully automated and tested.
- [ ] Environment promotion path (Dev -> Staging -> Prod) is clear.
- [ ] Rollback process is documented and tested.
- [ ] Secrets are managed securely (not in Git or plain-text vars).
- [ ] ConfigMaps handle environment-specific overrides.

## 3. Reliability & Scaling

- [ ] Resource Requests and Limits are configured based on usage data.
- [ ] Horizontal Pod Autoscaler (HPA) is configured and tested.
- [ ] Pod Disruption Budgets (PDBs) are defined.
- [ ] Health checks (Liveness, Readiness, Startup) are correctly configured.
- [ ] Retry logic and circuit breakers are implemented for dependencies.

## 4. Observability

- [ ] Logs are structured (JSON) and forwarded to a central system.
- [ ] Key metrics (Golden Signals: Latency, Traffic, Errors, Saturation) are tracked.
- [ ] Critical alerts have defined thresholds and clear routing.
- [ ] Service dashboard exists in the central observability tool.
- [ ] SLOs (Service Level Objectives) are defined and measurable.

## 5. Data & State

- [ ] Database backups are scheduled and tested for restoration.
- [ ] Data retention policies are implemented.
- [ ] Database migrations are handled as part of the deployment.
- [ ] Persistent storage performance meets application requirements.

## 6. Security & Compliance

- [ ] RBAC follows the principle of least privilege.
- [ ] Network Policies restrict traffic to required ports/services.
- [ ] Vulnerability scanning is integrated into the build process.
- [ ] Compliance requirements (GDPR, SOC2, etc.) are validated.
- [ ] Audit logging is enabled for administrative actions.

## 7. Incident Response

- [ ] Escalation path is documented in the runbook.
- [ ] Stakeholders are identified for communication.
- [ ] Post-incident review process is understood by the team.

## Definition of Done

A service is considered "Production Ready" when all "Critical" items are checked, a soak period in Staging has passed without issues, and the owning team has formally accepted support responsibility.

> [!WARNING]
> A service is not production-ready just because it was deployed successfully. It must be observable, supportable, documented, and owned. Deploying "blind" creates technical debt and operational risk.
