# Incident Response Runbook

## Table of Contents

- [Purpose](#purpose)
- [Audience](#audience)
- [What Is Incident Response?](#what-is-incident-response)
- [What Is On-Call?](#what-is-on-call)
- [On-Call Models](#on-call-models)
- [Incident Management and Paging Tools](#incident-management-and-paging-tools)
- [Severity Levels](#severity-levels)
- [Incident Roles](#incident-roles)
- [Paging and Escalation](#paging-and-escalation)
- [First 15 Minutes Checklist](#first-15-minutes-checklist)
- [Incident Flow](#incident-flow)
- [Communication](#communication)
- [Mitigation and Resolution](#mitigation-and-resolution)
- [RCA and Postmortem](#rca-and-postmortem)
- [Postmortem Template](#postmortem-template)
- [Best Practices](#best-practices)
- [Incident Response Readiness Checklist](#incident-response-readiness-checklist)

---

## Purpose

The purpose of this runbook is to provide a consistent and structured way to detect, triage, communicate, mitigate, resolve, and review production incidents.

By following this process, we reduce confusion during high-pressure situations and ensure that teams know:
*   Who is responsible for what.
*   What to do in the first 15 minutes.
*   How to communicate status to stakeholders.
*   When and how to escalate to other teams.
*   How to restore service safely.
*   How to document the incident to prevent it from happening again.

> [!NOTE]
> During an active incident, the goal is not to write perfect documentation. The goal is to restore service safely, communicate clearly, and keep the response coordinated.

---

## Audience

This document is designed for engineers at all levels:
*   **Junior Engineers:** To understand the end-to-end incident process before joining a rotation.
*   **Medior Engineers:** As a reference guide for joining on-call rotations and managing SEV2/SEV3 incidents.
*   **Senior/Staff Engineers:** To coordinate major SEV1 incidents and lead technical mitigation.
*   **Engineering Managers:** To understand the incident flow and support the team during escalations.

---

## What Is Incident Response?

Incident response is the structured process used when a production service is degraded, unavailable, unstable, or behaving incorrectly. It is the bridge between detecting a problem and returning the system to a healthy state.

The process follows a standard flow:
```text
Detect -> Triage -> Communicate -> Mitigate -> Resolve -> Review
```

> [!IMPORTANT]
> Incident response is not only a technical process. It is also a communication and coordination process. Even the best technical fix is useless if stakeholders are left in the dark.

---

## What Is On-Call?

On-call is a responsibility model where one or more engineers are responsible for responding to production alerts during a defined time window.

The on-call engineer is not expected to fix every problem alone. They are expected to:
1.  **Acknowledge** the alert quickly.
2.  **Assess** the actual impact (is it a real incident?).
3.  **Start** the response process (create channels, assign roles).
4.  **Escalate** to specialists or managers when needed.
5.  **Coordinate** the mitigation efforts.

> [!WARNING]
> On-call without clear escalation rules is risky. A single engineer should not be the only person responsible for a serious production incident.

---

## On-Call Models

Different teams use different models based on their size, service criticality, and geographic distribution.

| Model | Description | Best For | Risk |
| :--- | :--- | :--- | :--- |
| **24/7 On-Call** | Engineers cover incidents 24/7, including nights/weekends. | Critical production systems. | High burnout risk if poorly rotated. |
| **Business Hours** | Response only during local working hours. | Internal tools or low-criticality systems. | No immediate response at night. |
| **Daily Rotation** | One engineer covers a full 24-hour block. | Small or medium teams. | Can be stressful during busy periods. |
| **Weekly Rotation** | One engineer covers a full 7-day week. | Stable services with low alert volume. | Long shifts can be tiring. |
| **Follow-the-Sun** | Teams in different time zones hand over responsibility. | Global teams across continents. | Requires strong handover processes. |
| **Primary/Secondary** | Primary responds first, secondary acts as backup. | Most production teams. | Escalation must be automated. |

### Follow-the-Sun
Follow-the-sun on-call is used by distributed teams across different time zones. Responsibility moves between regions so engineers usually respond during their local daytime. This model reduces night work but requires high-quality handover notes.

---

## Incident Management and Paging Tools

Tools vary by company maturity, but their purpose remains the same: ensuring the right people are notified at the right time.

| Tool | Typical Use |
| :--- | :--- |
| **PagerDuty / Opsgenie** | On-call schedules, escalation policies, and urgent paging. |
| **Rootly / Blameless** | Incident management automation and Slack-based workflows. |
| **Grafana OnCall** | Integrated on-call management for teams using Grafana. |
| **ServiceNow / Jira SM** | Enterprise incident tracking and change management. |
| **Slack / MS Teams** | Real-time communication and coordination during the event. |
| **Alertmanager** | Routing and grouping alerts from Prometheus. |

> [!NOTE]
> Tools do not solve incident response by themselves. The process, ownership, escalation rules, and alert quality are far more important than the software used.

---

## Severity Levels

Defining severity helps the team prioritize the response and determine who needs to be notified.

| Severity | Description | Example | Expected Response |
| :--- | :--- | :--- | :--- |
| **SEV1** | Critical outage | Core service down for all/most users; data loss. | Immediate page, IC assigned, hourly updates. |
| **SEV2** | Major degradation | Important feature down; high latency; partial outage. | Page on-call, incident channel, regular updates. |
| **SEV3** | Minor issue | Workaround exists; limited user impact. | Handled during business hours; ticket created. |
| **SEV4** | Low impact | Cosmetic bug; internal tool minor issue. | Backlog item; non-urgent. |

> [!WARNING]
> When unsure between two severity levels, start with the higher severity. It is easier to downgrade an incident later than to lose time during a real outage.

---

## Incident Roles

Standardized roles prevent "too many cooks in the kitchen" and ensure clear accountability.

| Role | Responsibility |
| :--- | :--- |
| **Incident Commander (IC)** | Coordinates the response and owns the process. Does not usually fix the code. |
| **Primary On-Call** | The first responder who acknowledges the alert and starts triage. |
| **Secondary On-Call** | Backup responder; joins if the primary does not respond or needs help. |
| **Technical Lead** | Leads the technical investigation and directs the actual mitigation. |
| **Communications Lead** | Handles internal status updates and public status page entries. |
| **Subject Matter Expert** | Domain expert paged in for specific deep-dive knowledge. |

> [!IMPORTANT]
> The Incident Commander does not need to be the person fixing the issue. Their job is to keep the response coordinated so the engineers can focus on the technical solution.

---

## Paging and Escalation

*   **Paging:** Sending an urgent, disruptive notification (phone call, SMS, push) to a responder.
*   **Escalation:** Moving the responsibility to the next person/team if the incident is not acknowledged or requires more help.

### Example Escalation Policy
1.  Page **Primary** on-call engineer.
2.  If not acknowledged in **5 minutes**, page **Secondary** on-call.
3.  If not acknowledged in **another 5 minutes**, page the **Engineering Manager**.
4.  If the incident is SEV1, notify the **Incident Commander** and **Comms Lead** immediately.

> [!WARNING]
> Paging should be reserved for actionable, urgent alerts. If every warning pages an engineer, "alert fatigue" will set in and real incidents will be missed.

---

## First 15 Minutes Checklist

If you are paged, follow these steps immediately:

- [ ] **Acknowledge** the alert in the paging tool.
- [ ] **Triage:** Confirm if the impact is real and determine initial severity.
- [ ] **Communication:** Create or join the dedicated incident Slack channel.
- [ ] **Roles:** Assign an Incident Commander (usually the first responder for SEV2+).
- [ ] **Impact:** Identify exactly which services and users are affected.
- [ ] **Investigation:** Check dashboards, logs, and recent deployment history.
- [ ] **Mitigate:** Decide if an immediate rollback is possible and safe.
- [ ] **Update:** Send the first internal status update.

---

## Incident Flow

1.  **Detection:** Alert triggers or a user reports an issue.
2.  **Triage:** Quick assessment of severity and impact.
3.  **Communication:** Notifying the team and stakeholders.
4.  **Mitigation:** Temporary fix to restore service (rollback, scale, failover).
5.  **Resolution:** Permanent fix or confirmation that the system is stable.
6.  **Review:** Postmortem and Root Cause Analysis (RCA).

---

## Communication

Regular updates reduce anxiety for stakeholders and keep the team focused.

### Internal Status Update Template
```text
**Status:** [Investigating / Mitigating / Monitoring / Resolved]
**Severity:** [SEV1 / SEV2 / SEV3]
**Impact:** Describe affected users, services, or functionality.
**Current Action:** What the team is doing right now.
**Next Update:** [HH:MM UTC]
```

> [!NOTE]
> During SEV1 and SEV2 incidents, short and regular updates are better than waiting for perfect information.

---

## Mitigation and Resolution

Mitigation is about **restoring service**, not finding the root cause.
*   **Rollback:** Revert to the last known good version.
*   **Scale:** Increase capacity if the system is saturated.
*   **Feature Flag:** Disable the broken feature immediately.
*   **Failover:** Move traffic to a healthy region or secondary database.
*   **Bypass:** Temporarily disable a non-critical failing dependency.

> [!IMPORTANT]
> During an active incident, restoring service is more important than finding the full root cause. Investigation happens after the system is stable.

---

## RCA and Postmortem

Once the incident is resolved, we shift to learning and prevention.

| Term | Meaning |
| :--- | :--- |
| **RCA** | Root Cause Analysis: The technical "Why" did it happen. |
| **Postmortem** | The full review: Timeline, Impact, Response, RCA, and Action Items. |

> [!WARNING]
> A postmortem without action items is just a story. The real value comes from fixing the systemic weaknesses revealed by the incident.

---

## Postmortem Template

```markdown
# Postmortem: [Incident Title]

## Date
YYYY-MM-DD

## Severity
SEV1 / SEV2 / SEV3

## Incident Commander
[Name]

## Summary
Brief description of what happened and the customer impact.

## Timeline
| Time (UTC) | Event |
| :--- | :--- |
| HH:MM | Alert triggered |
| HH:MM | Incident acknowledged |
| HH:MM | Mitigation started (e.g., Rollback) |
| HH:MM | Service restored |

## Root Cause
Detailed technical explanation of the failure.

## What Went Well / What Didn't
*   [+] Quick detection via monitoring.
*   [-] Escalation to the networking team took too long.

## Action Items
| Task | Owner | Due Date | Status |
| :--- | :--- | :--- | :--- |
| Implement automated rollback | [Team] | YYYY-MM-DD | Open |
```

---

## Best Practices

*   **Actionable Alerts:** Only page for things that require immediate human action.
*   **Blameless Culture:** Focus on the system, not the person who made the mistake.
*   **Runbook Maintenance:** If a runbook is out of date, update it immediately after the incident.
*   **Dashboards:** Ensure critical dashboards are linked directly in the alert notification.
*   **Handover:** If an incident lasts many hours, perform a formal handover to a fresh engineer.

---

## Incident Response Readiness Checklist

- [ ] Every production service has a clearly defined owner.
- [ ] An active on-call schedule exists in a paging tool.
- [ ] Automatic escalation policies are configured.
- [ ] Alerts are routed to the correct teams.
- [ ] Runbooks exist for all common failure modes.
- [ ] Severity levels are understood by the whole team.
- [ ] A blameless postmortem process is established.
