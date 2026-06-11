# Git Task Helper

## Purpose
The `git-task` helper streamlines the process of starting work on a new task. It integrates with Jira to automatically fetch issue details, derive appropriate branch types, create a correctly formatted local branch, and update the Jira ticket status and comments.

## What It Does
When run, the helper:
1. Verifies the environment (Git repository, `origin` remote, dependencies).
2. Fetches the latest remote refs.
3. Retrieves ticket details from the Jira Cloud REST API.
4. Derives the branch type from the Jira issue type.
5. Creates a local Git branch originating from the repository's base branch.
6. Transitions the Jira ticket to `In Progress` (or a configured state).
7. Adds a comment to the Jira ticket with the new branch name.

## Requirements
To use `git-task`, you need:
- `git`
- `curl`
- `jq`
- A Jira Cloud account
- A Jira API token
- Permission to browse issues, transition issues, and add comments.

## Jira Configuration
The helper uses environment variables to authenticate with Jira. Add these to your shell profile (`~/.bashrc`, `~/.zshrc`, etc.):

```bash
export JIRA_BASE_URL="https://example.atlassian.net"
export JIRA_EMAIL="user@example.com"
export JIRA_API_TOKEN="local-api-token"
```

> [!WARNING]
> Do not commit Jira API tokens, credentials, or personal configuration into the repository. Keep them in a local shell profile, password manager, or approved secret store.

## Global Git Config
You can configure repository-specific behavior globally using `git config`. This is useful for repositories that have different base branches or require different Jira transition names.

```ini
[task "cloud"]
	baseBranch = main
	jiraTransition = In Progress
	addComment = true

[task "kube-image-inventory"]
	baseBranch = main
	jiraTransition = In Progress
	addComment = true

[task]
	defaultType = feature
```

## Branch Naming
The generated branch will follow this format:
```text
<type>/<ticket-key>-<slugified-title>
```

Examples:
- `feature/DEVOPS-123-add-terraform-validation`
- `bugfix/DEVOPS-248-fix-github-actions-cache`
- `docs/DEVOPS-301-document-jenkins-recovery`
- `hotfix/DEVOPS-500-fix-production-deploy`
- `chore/DEVOPS-600-update-dependencies`

## Branch Type Mapping
The branch `<type>` represents the kind of work and is automatically derived from the Jira issue type:

- `Bug` -> `bugfix`
- `Story` -> `feature`
- `Task` -> `feature`
- `Improvement` -> `feature`
- `Incident` -> `hotfix`
- `Documentation` -> `docs`

If the issue type is not mapped, it defaults to `feature` (or your configured `task.defaultType`). You can always override this using the `--type` flag.

## Usage Examples

**Default Jira-driven mode:**
```bash
git-task DEVOPS-123
```

**Manual title override:**
```bash
git-task DEVOPS-123 "add terraform validation"
```

**Manual branch type override:**
```bash
git-task DEVOPS-123 --type docs
```

**Open Jira ticket after processing:**
```bash
git-task DEVOPS-123 --open
```

## How It Works
The helper dynamically determines the base branch by checking your global `git config` for the repository. If not configured, it checks `origin/HEAD`, and falls back to `main` or `master`. It then talks to Jira via the REST API v3 using Atlassian Document Format (ADF) to add comments and transitions the issue state based on the name.

## Dry Run Mode
You can see what the tool would do without making any changes to your local Git repository or remote Jira instance:

```bash
git-task DEVOPS-123 --dry-run
```

## No Jira Mode
If you do not have Jira access or just want to create a branch without making API calls, use `--no-jira`. A manual title must be provided.

```bash
git-task --no-jira DEVOPS-123 "add terraform validation"
```

## Safety Notes
- The helper creates only a **local branch**.
- It **does not push** automatically.
- It **does not create pull requests**.
- It **does not delete branches**.
- It **does not run destructive Git commands**.
- Jira mutations can be bypassed with `--no-transition` and `--no-comment`.
- `--dry-run` should be used when testing your configuration.
- Transition names depend on your specific Jira workflow.
- Users should review the generated branch name before pushing to the remote.

## Limitations
- Assumes Jira Cloud REST API v3.
- Assumes issue keys format like `DEVOPS-123`.
- Assumes an `origin` remote exists.
- Requires `curl` and `jq` installed on your machine.
- The targeted transition name must be available for the issue's current status.
- Does not automatically support every custom Jira workflow.
- Does not support multiple Jira instances automatically.
- Does not assign tickets unless added later.
- Does not create pull requests or push branches.

## Future Improvements
- Assign issue to current user.
- Open the Jira ticket in browser.
- Create pull request after first push.
- Support GitHub/GitLab/Bitbucket PR creation.
- Cache Jira issue summaries.
- Shell completion for assigned Jira tickets.
- Custom Jira issue type mappings.
- Support multiple Jira instances.
- Support GitHub Issues or Linear.
- Support `git-task finish` to move ticket to review.

## Summary
The `git-task` helper saves time by automating boilerplate branch creation and Jira updates. It is designed to be safe, requiring manual review and pushing, while keeping external configuration out of your project repositories.
