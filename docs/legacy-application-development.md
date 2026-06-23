# AI-Assisted Legacy Application Development

This document applies the principles from `ai-assisted-development.md` to maintaining, extending, and modernizing existing applications.

It is for situations where an engineer or AI agent must work in a repository with incomplete documentation, older patterns, fragile integrations, hidden operational assumptions, or previous feature work that should not be rediscovered from scratch.

The goal is practical: understand the system before changing it, keep the scope small, validate behavior, and preserve useful context for the next engineer or AI session.

Common examples:
- replacing an obsolete integration
- migrating between APIs
- refactoring an internal tool
- extending an operational workflow
- improving reliability around an existing job or service

## 1. AI-Assisted Discovery

The AI agent should inspect the context that explains how the system works today:
- repository instructions
- README files
- relevant modules
- tests
- configuration
- integration code
- ADRs
- changelog entries
- feature notes
- operational documentation

Expected output:
- short system summary
- relevant files and modules
- main data or request flow
- integration points
- existing tests
- assumptions
- unknowns
- risks

Do not modify files during discovery.

The engineer must verify important conclusions instead of assuming the repository summary is correct. In legacy systems, a summary can miss behavior hidden in scheduled jobs, shell scripts, deployment configuration, or manual support steps.

For integrations, document the useful shape of the dependency:
- authentication method
- credential ownership
- secret locations or references
- required permissions
- rate limits
- payload differences
- timeout behavior
- retry behavior
- idempotency expectations
- fallback or rollback options

Never store credential values in documentation.

## 2. AI-Assisted Planning

After discovery, the AI agent should propose a short plan.

The plan should include:
- minimal implementation scope
- files expected to change
- existing patterns that should be reused
- tests that should be added or updated
- compatibility risks
- migration or rollout considerations
- rollback approach
- unresolved questions

The plan should be short enough to review. If it takes a long document to justify the change, the scope is probably too large or the system is not understood yet.

## 3. Engineer Checkpoint

Before implementation, the engineer checks the plan.

Verify that:
- current behavior is understood
- scope is minimal
- the plan follows existing architecture
- assumptions are explicitly marked
- no unrelated refactoring is included
- validation and rollback are realistic

This is where the engineer corrects the AI agent's assumptions and narrows the work before code changes begin.

## 4. AI-Assisted Implementation

The AI agent should:
- follow the reviewed plan
- modify only required files
- preserve existing behavior outside the requested change
- reuse existing patterns
- avoid opportunistic cleanup
- avoid large refactoring unless required
- add or update tests with the implementation
- report any necessary deviation from the plan

If implementation reveals that the approved plan is incorrect, stop and revise the plan before expanding the scope.

For example, replacing an old API client should not also rename unrelated modules, change formatting across the repository, or restructure tests unless the change requires it.

## 5. Validation and AI Review

Validation should match the risk and shape of the change.

Check what is relevant:
- existing tests
- new tests
- happy path
- failure path
- edge cases
- integration behavior
- configuration compatibility
- deployment or startup validation
- relevant logs, metrics, health checks, and application behavior

Relevant logs, metrics, health checks, and application behavior should show no unexpected regression after the change.

After validation, use AI review on the final diff. The AI agent should:
- inspect the final diff
- compare it with the original request and reviewed plan
- identify unnecessary changes
- identify missing tests
- check backward compatibility
- check operational impact
- report issues without automatically rewriting the implementation

A fresh AI session can be used for final review. Human review remains responsible for accepting or rejecting the result.

## 6. Deployment and Observation

Deploy through the normal process for the project.

After deployment:
- verify health checks
- inspect relevant logs and metrics
- validate the changed behavior
- execute rollback if agreed failure signals appear

Keep this focused on the actual change. A small internal-tool refactor does not need the same rollout notes as a risky integration migration.

## 7. Preserve Useful Context

Work completed for Feature A should reduce rediscovery when Feature B is implemented later.

Useful durable context may include:
- ADRs for architectural decisions
- feature notes for non-obvious behavior
- changelog entries for historical traceability
- operational notes for deployment, monitoring, failure handling, and recovery
- concise comments only where the code cannot explain the reasoning clearly

Do not create documentation for every trivial change.

Use this rule of thumb:
- Create an ADR only for a meaningful architectural decision.
- Create a feature note when behavior or integration logic needs explanation.
- Add an operational note when deployment, monitoring, or recovery changes.
- Use a changelog entry for a concise history of notable changes.

Previous PR review findings can be useful context, but they do not need a separate document for every pull request. Preserve only the review notes that explain risk, tradeoffs, or future work.

## 8. Feature A to Feature B Example

Feature A replaces an old on-call notification provider with a new external integration.

During the work, the engineer stores durable context:
- mapping rules between old and new event fields
- retry behavior and timeout assumptions
- configuration keys and secret references
- rollout decision and fallback option
- known limitations that were accepted

Later, Feature B adds another notification path. The next engineer or AI session reads the stored context first, then inspects the related code. They do not need to reconstruct the migration from commit history, chat logs, or guesswork.

That is the point of durable context: each feature should make the next related feature easier to understand.

## 9. Recommended Repository Structure

Keep documentation close to the repository and easy to scan.

Example:

```text
docs/
|-- adr/
|-- features/
|-- changelog.md
|-- ai-assisted-development.md
`-- legacy-application-development.md
```

Suggested use:
- `docs/adr/`: meaningful architecture decisions
- `docs/features/`: feature notes for non-obvious behavior
- `docs/changelog.md`: concise history of notable changes
- `docs/ai-assisted-development.md`: general AI-assisted development guide
- `docs/legacy-application-development.md`: this legacy workflow

## 10. Workflow Diagram

The Eraser workflow diagram shows the loop from feature request through discovery, planning, implementation, review, deployment, documentation, and future context reuse.

![Legacy application development workflow](images/ai-assisted-feature-development-flow.png)

Step summary:
- Feature Request: define the problem, constraints, and acceptance criteria.
- Repository Review: use the AI agent to inspect the existing system before choosing an approach.
- Feature Plan: ask the AI agent for a short implementation, validation, and rollback plan.
- Engineer Checkpoint: verify the plan and narrow scope before code changes.
- AI Implementation: make the reviewed change and preserve existing behavior outside the request.
- Local Testing: run tests and validation relevant to the change.
- AI PR Review: inspect the final diff for unnecessary changes, missing tests, compatibility risks, and operational impact.
- Human Review: accept or reject the result based on understanding, supportability, and risk.
- Merge & Deploy: release through the normal process and observe the changed behavior.
- Update Docs: record only useful durable context.
- Next Feature Context: feed ADRs, feature notes, changelog entries, and operational notes into the next related change.

## 11. Practical Checklist

- Repository context inspected.
- Existing behavior understood.
- Assumptions documented.
- Implementation plan reviewed.
- Scope kept minimal.
- Tests added or updated.
- Failure paths checked.
- Final diff reviewed.
- Deployment signals identified.
- Rollback understood.
- Useful durable context preserved.
