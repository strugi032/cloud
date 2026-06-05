# CI/CD Standards

This document defines the standard structure, quality gates, and deployment practices for CI/CD pipelines.

## Pipeline Principles

*   **Consistency:** All pipelines should follow a similar structure to reduce cognitive load.
*   **Security:** Secrets are never stored in Git; scans are integrated into the pipeline.
*   **Feedback:** Pipelines should fail fast and provide clear error messages.
*   **Automation:** Deployments to non-production environments should be fully automated.

## Branching Strategy

*   **Main-based Development:** Feature branches merge into `main`.
*   **Short-lived Branches:** Feature branches should exist for days, not weeks.
*   **Tags for Release:** Production deployments should be triggered by semver tags or manual promotion of a validated build.

## Recommended Pipeline Flow

```text
Commit -> Build -> Test -> Scan -> Package -> Deploy to Dev -> Promote -> Deploy to Production
```

## Pipeline Stages

### 1. Build
- Compile code and install dependencies.
- Linting (Style, Syntax, Static Analysis).

### 2. Test
- Unit tests (minimum code coverage required).
- Integration tests (mocked external dependencies).

### 3. Security Scan
- SAST (Static Application Security Testing).
- Dependency vulnerability scanning (SCA).
- Secret scanning in the repository.

### 4. Package
- Container image creation (Dockerfile).
- Image signing and pushing to a private registry.
- Helm chart or Kustomize manifest packaging.

### 5. Deploy to Non-Prod (Dev/Staging)
- Automated deployment upon successful build of `main`.
- Automated smoke tests and health checks.

### 6. Promotion & Approval
- Manual or automated gate for Production.
- Requires successful deployment and validation in Staging.

### 7. Deploy to Production
- Deployment using the **exact same artifact** as Staging.
- Strategy: Rolling update, Canary, or Blue/Green.

## Secrets Handling

> [!WARNING]
> Secrets must not be stored in Git, pipeline logs, or plain-text pipeline variables. Use a dedicated secret manager (e.g., HashiCorp Vault, AWS Secrets Manager, GitHub Secrets) and inject them at runtime or as environment-specific variables.

## Pipeline Observability

- Track pipeline success rates and duration.
- Ensure deployment events are sent to the central observability tool (e.g., Grafana annotations).
- Alert on persistent pipeline failures.

> [!NOTE]
> Quality gates (e.g., mandatory 80% test coverage) should be enforced but bypassable in emergency "hotfix" situations with appropriate approval.
