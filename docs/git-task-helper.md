# Git Task Helper

## Purpose

Starting work on a Jira ticket often involves repeated manual steps:

* fetch latest changes
* switch to the default branch
* create a correctly named branch
* include the Jira ticket ID in the branch name
* keep branch naming consistent across repositories

The `git-task` helper makes this process repeatable and less error-prone.

## Example Usage

```bash
git-task DEVOPS-123 "add terraform validation"
```

Creates:

```text
feature/DEVOPS-123-add-terraform-validation
```

```bash
git-task DEVOPS-248 "fix github actions cache"
```

Creates:

```text
feature/DEVOPS-248-fix-github-actions-cache
```

```bash
git-task DEVOPS-301 "document jenkins recovery"
```

Creates:

```text
docs/DEVOPS-301-document-jenkins-recovery
```

## Why `git-task` Instead of `git task`

Using `git-task` makes it clear that this is a custom local helper script, not a built-in Git command. This reduces confusion and clarifies that the helper is a personal workflow script.

## Optional Git Subcommand Usage

If the executable named `git-task` is available in your `$PATH`, Git can also run it as an optional subcommand.

This allows the command to be used as:

```bash
git task DEVOPS-123 "add terraform validation"
```

This behavior is optional and relies on Git's standard custom command discovery.

## Configuration Model

Configuration values are determined in this order:

1. detect the default branch from `origin/HEAD`
2. use sensible defaults
3. allow optional overrides from global Git config

Default behavior:

```text
base branch: detected from origin/HEAD, fallback to main or master
branch prefix: feature
include title in branch name: true
```

## Expected Behavior

The command:

```bash
git-task DEVOPS-123 "add terraform validation"
```

should:

* verify that the current directory is inside a Git repository
* detect the repository name from `origin`
* fetch from origin before detecting remote branches
* detect the default base branch
* read optional global config for that repository
* fetch latest changes
* check out a new branch from the remote base branch using `git switch`
* slugify the short description
* print what it did

It should not:

* push the branch automatically
* modify Jira automatically
* delete branches
* run destructive commands
* require repository-local config files

## Installation

```bash
mkdir -p ~/bin
cp scripts/git-task ~/bin/git-task
chmod +x ~/bin/git-task
```

Ensure `~/bin` is in `$PATH`:

```bash
export PATH="$HOME/bin:$PATH"
```

## Global Config Examples

Example for normal feature work:

```ini
[task "kube-image-inventory"]
	baseBranch = main
	branchPrefix = feature
	includeTitle = true
```

Example for documentation repository:

```ini
[task "cloud"]
	baseBranch = main
	branchPrefix = docs
	includeTitle = true
```

Example for older repository using `master`:

```ini
[task "legacy-project"]
	baseBranch = master
	branchPrefix = bugfix
	includeTitle = false
```

## Usage Examples

```bash
git-task DEVOPS-410 "add runbook template"
```

Creates:

```text
feature/DEVOPS-410-add-runbook-template
```

```bash
git-task DEVOPS-411 "improve adr template"
```

With `cloud` configured to use `docs` prefix, creates:

```text
docs/DEVOPS-411-improve-adr-template
```

```bash
git-task DEVOPS-500 "fix deployment pipeline"
```

Creates:

```text
feature/DEVOPS-500-fix-deployment-pipeline
```

## Limitations

* assumes the repository has an `origin` remote
* creates local branches only
* does not push automatically
* does not validate whether the Jira ticket exists
* does not update Jira
* repositories with unusual branching rules may need global Git config overrides

## Safety Notes

* does not delete branches
* does not push automatically
* does not run destructive commands
* prints what it is going to create
* user should review the branch name before pushing

## Optional Jira Integration

Jira integration can be added later, but should not be enabled by default.

Possible future improvements:

* validate ticket exists
* read ticket title
* print ticket URL
* move ticket to “In Progress”

This is not enabled by default because different teams use different Jira workflows, statuses, permissions, and project keys.

## Summary

* `git-task` is a small local Git helper
* it starts work on Jira-style tasks consistently
* it avoids repository-local config files
* it uses default branch detection
* it supports optional global Git config overrides
* it can be extended later, but should stay simple
