# Observability Checklist

This checklist ensures that a service provides enough visibility to understand its behavior during normal operation and troubleshooting.

## 1. Logs

- [ ] Logs are structured (e.g., JSON) for easy parsing.
- [ ] Sensitive data (PII, credentials) is masked or excluded.
- [ ] Correlation IDs are included for cross-service requests.
- [ ] Log levels (INFO, WARN, ERROR) are used correctly.
- [ ] Logs are forwarded to a central log management system.

## 2. Metrics

- [ ] **Golden Signals** are tracked:
    - **Latency:** Time it takes to service a request.
    - **Traffic:** Demand placed on the system.
    - **Errors:** The rate of requests that fail.
    - **Saturation:** How "full" the service is.
- [ ] Custom business metrics (e.g., "orders processed") are implemented.
- [ ] Resource usage (CPU, Memory, Disk, Network) is tracked.
- [ ] Metrics have appropriate labels/tags for filtering.

## 3. Traces

- [ ] Distributed tracing is implemented for microservices.
- [ ] Trace IDs are propagated through all service boundaries.
- [ ] Database and external API calls are instrumented.
- [ ] Sampling rate is tuned to balance cost and visibility.

## 4. Dashboards

- [ ] High-level service overview dashboard exists.
- [ ] Dashboards show trends (over time), not just current values.
- [ ] Operational dashboards are linked from runbooks.
- [ ] Visualizations are clear and easy to interpret during an incident.

## 5. Alerts

- [ ] Alerts are actionable (not "just FYI").
- [ ] Thresholds are based on SLOs, not arbitrary numbers.
- [ ] Severity levels are defined (Page vs. Ticket).
- [ ] Alerts include links to dashboards and relevant runbooks.
- [ ] Noise/flapping is minimized through appropriate grouping and delays.

## 6. SLOs (Service Level Objectives)

- [ ] SLIs (indicators) are clearly defined.
- [ ] SLO targets (e.g., 99.9% success) are agreed with stakeholders.
- [ ] Error budgets are tracked and used to prioritize reliability work.

## 7. Operational Ownership

- [ ] Alert routing is configured to the owning team.
- [ ] Runbooks are updated whenever a new alert is created.
- [ ] On-call engineers have access to all observability tools.

> [!IMPORTANT]
> Observability is not only about collecting data. It is about making the system understandable during normal operation and incidents. If you cannot explain *why* a system is failing by looking at its telemetry, your observability is incomplete.
