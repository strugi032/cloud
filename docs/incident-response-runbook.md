# Incident Response Runbook

This runbook defines the process for detecting, triaging, and resolving production incidents.

## 1. Purpose

To ensure that incidents are handled consistently, communication is clear, and resolution is achieved as quickly as possible with minimal impact on users.

## 2. Severity Levels

| Severity | Description | Example |
|---|---|---|
| **SEV1** | Critical production outage | Core service unavailable for most users; data loss. |
| **SEV2** | Major degradation | Important feature unavailable; high latency for many users. |
| **SEV3** | Minor degradation | Partial impact; workaround available; low-volume feature issue. |
| **SEV4** | Low impact issue | Non-urgent operational issue; cosmetic bugs; minor internal tools. |

## 3. Incident Roles

- **Incident Commander (IC):** Leads the response, coordinates roles, and makes final decisions.
- **Communications Lead:** Handles internal and external status updates.
- **Operations/Tech Lead:** Directs the technical investigation and mitigation efforts.

## 4. Incident Flow

```text
Detect -> Triage -> Communicate -> Mitigate -> Resolve -> Review
```

### Phase 1: Detection
Incidents are detected via automated alerts, internal reports, or customer support tickets.

### Phase 2: Triage
Determine the severity and impact. If SEV1 or SEV2, appoint an Incident Commander immediately.

### Phase 3: Communication
Establish a dedicated communication channel (Slack/Teams). Post initial internal status update. If SEV1, update the public status page.

### Phase 4: Mitigation
The primary goal is to **restore service**, not to find the root cause. This may involve rolling back, scaling up, or bypassing a failing component.

### Phase 5: Resolution
The incident is resolved when the system is stable and the immediate threat to service is gone.

### Phase 6: Review (Post-Incident Review)
Schedule a postmortem within 48-72 hours for all SEV1 and SEV2 incidents.

## 5. Postmortem Template

```markdown
# Postmortem: [Title]

**Date:** YYYY-MM-DD
**Severity:** SEV1 / SEV2
**Incident Commander:** [Name]
**Participants:** [List]

## Summary
Brief description of what happened and the impact.

## Timeline
- HH:MM [UTC] - Detection
- HH:MM [UTC] - Triage & IC assigned
- HH:MM [UTC] - Mitigation started
- HH:MM [UTC] - Service restored

## Root Cause
Detailed technical explanation of why the incident happened.

## What went well
- Success 1
- Success 2

## What didn't go well
- Issue 1
- Issue 2

## Action Items
- [ ] Task 1 (Owner, Due Date)
- [ ] Task 2 (Owner, Due Date)
```

> [!IMPORTANT]
> The postmortem process is blameless. The goal is to identify systemic weaknesses, not to punish individuals. Focus on how the system allowed the failure to occur.
