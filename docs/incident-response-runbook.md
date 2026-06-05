# Incident Response Runbook

This runbook defines the process for detecting, triaging, and resolving production incidents.

## 1. Severity Levels

| Severity | Description | Example |
| :--- | :--- | :--- |
| **SEV1** | Critical production outage | Core service unavailable for most users; data loss. |
| **SEV2** | Major degradation | Important feature unavailable; high latency for many users. |
| **SEV3** | Minor degradation | Partial impact; workaround available; low-volume feature issue. |
| **SEV4** | Low impact issue | Non-urgent operational issue; cosmetic bugs; minor internal tools. |

## 2. Incident Roles

*   **Incident Commander (IC):** Leads the response, coordinates roles, and makes final decisions.
*   **Communications Lead:** Handles internal and external status updates.
*   **Operations Lead:** Directs the technical investigation and mitigation efforts.

## 3. First 15 Minutes Checklist

- [ ] **Identify:** Confirm the incident and determine initial severity.
- [ ] **Appoint:** Assign an Incident Commander (IC).
- [ ] **Channel:** Create a dedicated Slack channel or Zoom bridge.
- [ ] **Update:** Post an initial internal status update.

## 4. Incident Flow

```text
Detect -> Triage -> Communicate -> Mitigate -> Resolve -> Review
```

### Communication Flow
*   **Internal:** Updates every 30 mins for SEV1, 60 mins for SEV2.
*   **External:** Update status page within 15 mins of SEV1 confirmation.

**Internal Status Template:**
```text
*Status:* [Investigating/Mitigating]
*Impact:* [Percentage of users / Feature name]
*Next Update:* [HH:MM UTC]
```

## 5. Escalation Rules

*   **SEV1:** Page the on-call engineer AND their manager immediately.
*   **SEV2:** Page the on-call engineer.
*   **SEV3/4:** Open a ticket or notify via channel during business hours.

## 6. Mitigation & Resolution

*   **Mitigation:** The goal is to restore service, not find the root cause. Roll back first, investigate later.
*   **Resolution Criteria:** Service is stable, monitoring is green, and the immediate threat is gone.

## 7. Postmortem Template

```markdown
# Postmortem: [Title]

**Date:** YYYY-MM-DD
**Severity:** SEV1 / SEV2
**Incident Commander:** [Name]

## Summary
Brief description of what happened and the impact.

## Root Cause
Detailed technical explanation.

## Action Items
- [ ] Task 1 (Owner, Due Date)
```

> [!IMPORTANT]
> The postmortem process is blameless. The goal is to identify systemic weaknesses.
