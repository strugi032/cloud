# AI-Assisted Development

## Purpose

This document describes how AI tools can support engineering work when used with clear context, review, and constraints.

The goal is not to blindly trust AI, but to use it as an assistant for understanding, drafting, refactoring, documenting, testing, and reviewing work. Human judgment remains the final authority.

## What Are Generative LLMs?

Generative LLMs generate text or code based on patterns and context.
They do not truly understand systems the way humans do.
They can produce useful drafts but can also produce wrong or outdated answers.
They need clear context and human review.

## What Is Gemini CLI?

Gemini CLI is a terminal-based AI coding assistant that can work with local project context.

Typical use cases:
- understanding a codebase
- explaining files
- drafting documentation
- writing or improving tests
- proposing refactors
- generating scripts
- reviewing changes
- summarizing errors and logs

## Markdown Files and AI Context

Markdown files are useful for AI-assisted work because they are:
- easy to read
- easy to version in Git
- easy to review in pull requests
- easy for AI tools to consume
- good for documenting conventions and decisions

Common examples:
- `README.md`
- `GEMINI.md`
- `docs/*.md`
- ADR documents
- runbooks
- troubleshooting notes

## What Is GEMINI.md?

`GEMINI.md` is used to provide persistent project context and instructions to Gemini CLI.

It should include:
- project purpose
- tech stack
- coding conventions
- test commands
- lint commands
- formatting rules
- project structure
- rules for safe changes
- documentation style
- commands that should not be run without approval

It should not include:
- secrets
- passwords
- tokens
- private credentials
- temporary personal notes
- huge duplicated documentation
- vague instructions

## Example GEMINI.md for a Python Project

```markdown
# Project Context

This is a Python project.

## Tech Stack

- Python 3.x
- pytest
- ruff
- mypy

## Common Commands

- Run tests: `pytest`
- Run linting: `ruff check .`
- Run formatting: `ruff format .`
- Run type checks: `mypy .`

## Coding Rules

- Keep changes small and reviewable.
- Prefer simple code over clever code.
- Add or update tests when behavior changes.
- Do not change public APIs without explaining why.
- Do not introduce new dependencies without approval.

## AI Assistant Rules

- Explain the plan before making large changes.
- Do not run destructive commands.
- Do not modify secrets or credentials.
- Show diffs or summarize changed files.
- Ask for clarification if requirements are unclear.
```

## What Are Skills?

Skills are reusable instructions or behavior packs that help AI assistants perform specific types of work more consistently.

Examples:
- Python development skill
- documentation writing skill
- testing skill
- Terraform review skill
- API integration skill

Skills should be specific and practical, not generic.

## What Are MCP Servers?

MCP servers are a way to connect the AI assistant to external tools, data, or documentation.

Examples:
- documentation search
- GitHub integration
- issue tracker integration
- cloud documentation
- local tools
- internal APIs

MCP increases capability but also increases risk, because the agent may gain access to tools or data.

## Safe Use of MCP

When using MCP:
- only connect trusted MCP servers
- avoid exposing secrets
- understand what tools the MCP server provides
- prefer read-only access where possible
- review actions before execution
- keep project and global configuration separate
- do not blindly approve tool calls

## How to Use Gemini CLI in a Python Project

1. Start with context
   - Make sure `README.md` and `GEMINI.md` are clear.
2. Ask for understanding first
   - Example prompt: "Read the project structure and summarize how the application works."
3. Ask for a plan before changes
   - Example prompt: "Before editing files, propose a short plan."
4. Keep changes small
   - Example prompt: "Refactor only this module and do not change behavior."
5. Require tests
   - Example prompt: "Add or update pytest tests for this behavior."
6. Review the diff
   - Example prompt: "Summarize the changed files and explain why each change was needed."
7. Run validation
   - Example prompt: "Run tests and linting. If something fails, explain the failure before fixing it."

## Good Prompt Examples

```text
Read this repository and summarize the main components, entry points, and test setup. Do not change files.
```

```text
Review this module for readability and possible bugs. Do not edit anything yet. Give me findings first.
```

```text
Create a small refactor plan for this function. Keep behavior unchanged and include test impact.
```

```text
Add pytest coverage for this edge case. Keep the production code unchanged unless required.
```

```text
Explain this error log and suggest the most likely root cause. Do not make changes yet.
```

```text
Update the documentation to match the current code. Do not invent features that are not present.
```

## Bad Prompt Examples

```text
Fix everything.
```
Too broad.

```text
Rewrite the whole project.
```
Too risky.

```text
Make it production ready.
```
Too vague.

```text
Run whatever commands you need.
```
Unsafe.

## What Not to Delegate Blindly

Do not delegate the following blindly:
- security decisions
- production changes
- infrastructure changes
- dependency upgrades
- authentication/authorization changes
- data migrations
- secrets handling
- incident response actions
- cost-impacting cloud changes

AI can help draft and analyze these, but a human must review and approve.

## Practical Review Checklist

- Did the AI understand the project context?
- Are the changes small and reviewable?
- Are tests included or updated?
- Were new dependencies introduced?
- Were secrets or credentials touched?
- Were commands safe?
- Is the reasoning clear?
- Does the final diff match the original request?

## Recommended Repository Structure

```text
.
├── GEMINI.md
├── README.md
├── docs/
│   ├── ai-assisted-development.md
│   ├── adr-template.md
│   └── runbooks/
├── src/
└── tests/
```

## Final Notes

- AI is useful when context is clear.
- Markdown makes context reviewable.
- `GEMINI.md` gives project-specific instructions.
- MCP adds tool access, but must be controlled.
- Small changes, tests, and human review are still required.
