# Observability Checklist

This checklist ensures that a service provides enough visibility to understand its behavior during normal operation and incidents.

## 1. Logs
- [ ] Logs are structured (JSON) for easy parsing.
- [ ] Correlation IDs are included for cross-service requests.
- [ ] Logs are forwarded to a central log management system.

## 2. Metrics (Golden Signals)
- [ ] **Latency:** Time it takes to service a request.
- [ ] **Traffic:** Demand placed on the system.
- [ ] **Errors:** The rate of requests that fail.
- [ ] **Saturation:** How "full" the service is.

## 3. Traces
- [ ] Distributed tracing is implemented for microservices.
- [ ] Trace IDs are propagated through all service boundaries.

## 4. Dashboards
- [ ] High-level service overview dashboard exists.
- [ ] Operational dashboards are linked from runbooks.

## 5. Alerts
- [ ] Alerts are actionable (not "just FYI").
- [ ] Thresholds are based on SLOs.
- [ ] Severity levels are defined (Page vs. Ticket).

## 6. Kubernetes-Specific Checks
- [ ] **Pod Restarts:** Alert on frequent container restarts.
- [ ] **OOMKilled:** Monitor for memory-related crashes.
- [ ] **CPU Throttling:** Track if CPU limits are causing latency.
- [ ] **Node Pressure:** Monitor node health (Disk/Memory pressure).
- [ ] **HPA Behavior:** Ensure horizontal scaling is triggering as expected.

## 7. SLOs & Operational Ownership
- [ ] SLIs/SLOs are clearly defined and measurable.
- [ ] Alert routing is configured to the owning team.

> [!IMPORTANT]
> Observability is not only about collecting data. It is about making the system understandable during normal operation and incidents.
