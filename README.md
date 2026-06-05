# Platform Engineering Playbook

This repository contains practical DevOps and Platform Engineering documentation, checklists, templates, and runbooks designed for modern engineering teams.

## Purpose

The goal of this playbook is to provide senior engineers with a standardized set of tools and processes for managing Kubernetes, CI/CD, cloud infrastructure, observability, and production reliability. 

These templates are intended to be adapted to your specific environment and organizational needs.

## Document Index

| Document | Purpose |
| :--- | :--- |
| [Project Delivery Playbook](./docs/project-delivery-playbook.md) | Defines how to start, align, plan, and track engineering projects |
| [Kubernetes Workload Sizing](./docs/kubernetes-resource-sizing.md) | Guide for optimizing Kubernetes resource requests and limits using Goldilocks |
| [Kubernetes Migration Checklist](./docs/kubernetes-migration-checklist.md) | Checklist for migrating workloads to or between Kubernetes clusters |
| [Production Readiness Checklist](./docs/production-readiness-checklist.md) | Validates whether a service is ready for production |
| [Incident Response Runbook](./docs/incident-response-runbook.md) | Defines how to handle incidents, escalations, communication, and postmortems |
| [Cloud Migration Risk Register](./docs/cloud-migration-risk-register.md) | Tracks common risks during cloud or platform migration projects |
| [CI/CD Standards](./docs/ci-cd-standards.md) | Defines recommended CI/CD pipeline structure, quality gates, and deployment practices |
| [Observability Checklist](./docs/observability-checklist.md) | Covers logs, metrics, traces, dashboards, alerts, and ownership |
| [ADR Template](./docs/adr-template.md) | Template for documenting important architecture and engineering decisions |

## Recommended Reading Order

1.  **Project Kickoff:** Start with the [Project Delivery Playbook](./docs/project-delivery-playbook.md) if you are beginning a new initiative.
2.  **Architecture & Design:** Use the [ADR Template](./docs/adr-template.md) to document key decisions.
3.  **CI/CD & Standards:** Establish your [Delivery Pipeline Standards](./docs/ci-cd-standards.md).
4.  **Production Readiness:** Use the [Readiness Checklist](./docs/production-readiness-checklist.md) before any major production release.
5.  **Incident Response:** Ensure you have the tools to handle issues once live with the [Incident Runbook](./docs/incident-response-runbook.md).

## How to Use This Repository

These documents are designed as **live templates**. 

*   **Fork/Clone:** Take what you need and store it in your own internal documentation repository.
*   **Adapt:** Replace placeholders (e.g., `<namespace>`, `<Service Name>`) with actual values.
*   **Automate:** Wherever possible, turn these checklists into automated CI/CD gates or monitoring alerts.

## Repository Structure

```text
.
├── README.md
└── docs/
    ├── adr-template.md                   # Decision records
    ├── ci-cd-standards.md                # Delivery standards
    ├── cloud-migration-risk-register.md  # Risk management
    ├── incident-response-runbook.md      # Ops & On-call
    ├── kubernetes-migration-checklist.md # Migration planning
    ├── kubernetes-resource-sizing.md      # Workload optimization
    ├── observability-checklist.md        # Monitoring & Reliability
    ├── production-readiness-checklist.md  # Go-live validation
    └── project-delivery-playbook.md      # Project kickoff and lifecycle
```

> [!WARNING]
> These documents are templates. Do not follow them blindly. Always adapt the checklists and standards to your specific technical constraints, compliance requirements, and team structure.
