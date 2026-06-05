# Cloud Migration Risk Register

This document tracks and manages risks associated with cloud or platform migration projects.

## Purpose

To proactively identify, assess, and mitigate risks that could impact the timeline, cost, or success of a migration initiative.

## How to use this Register

1.  **Identify:** List any event that could negatively impact the project.
2.  **Assess:** Rate the Impact and Probability.
3.  **Mitigate:** Define clear actions to reduce the risk.
4.  **Track:** Update the status regularly during project standups.

## Definitions

### Impact Levels
- **High:** Critical path blocked; production outage; significant data loss.
- **Medium:** Significant delay; high work-around effort; partial impact.
- **Low:** Minimal delay; easy workaround; minor impact.

### Probability Levels
- **High:** Likely to occur.
- **Medium:** Possible, but not certain.
- **Low:** Unlikely to occur.

## Risk Register

| Risk | Impact | Probability | Owner | Mitigation | Status |
|---|---|---|---|---|---|
| Missing dependency | High | Medium | Application Team | Run comprehensive discovery/mapping before migration | Open |
| DNS issue | High | Low | Platform Team | Lower TTLs 48h before cutover; test DNS locally | Open |
| Missing rollback plan | High | Medium | Tech Lead | Define and test rollback steps before release window | Open |
| IAM / Permission gap | Medium | High | Cloud Engineer | Use IaC to pre-provision roles; test in Staging | Open |
| Network connectivity | High | Medium | Networking Team | Validate VPN/DirectConnect throughput and latency | Open |
| Secret management gap | High | Low | Security Team | Audit all hardcoded secrets; migrate to Vault/Secret Manager | Open |
| Database sync delay | High | Medium | DBA Team | Perform mock data migrations; monitor replication lag | Open |
| Observability gap | Medium | Medium | SRE Team | Ensure logging and metrics are functional before traffic switch | Open |
| Unclear ownership | Medium | High | Project Manager | Assign explicit owners for every phase in the kickoff | Open |
| Scope creep | Medium | Medium | Tech Lead | Strictly define MVP and "In-Scope" during planning | Open |

> [!IMPORTANT]
> A risk register is only useful if it is updated. Review these risks at the start of every phase and after any significant architectural change.
