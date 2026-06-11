# Git Task Helper

## Purpose

Starting work on a Jira ticket often involves repeated manual steps:

* fetch latest changes
* switch to the default branch
* create a correctly named branch
* include the Jira ticket ID in the branch name
* keep branch naming consistent across repositories

The `git task` helper makes this process repeatable and less error-prone.

## Example Usage

```bash
git task DEVOPS-123 "add terraform validation"
```

Creates:

```text
feature/DEVOPS-123-add-terraform-validation
```

```bash
git task DEVOPS-248 "fix github actions cache"
```

Creates:

```text
feature/DEVOPS-248-fix-github-actions-cache
```

```bash
git task DEVOPS-301 "document jenkins recovery"
```

Creates:

```text
docs/DEVOPS-301-document-jenkins-recovery
```

## How Git Custom Commands Work

Git can run custom commands when an executable named `git-task` exists in the user’s `$PATH`.

This allows the command to be used as:

```bash
git task DEVOPS-123 "add terraform validation"
```

instead of:

```bash
git-task DEVOPS-123 "add terraform validation"
```

## Why No Per-Repository Hidden Files

The helper should not require files like `.git-task.env` inside every repository.

Reasons:

* avoids repository clutter
* avoids committing personal workflow settings
* works across many repositories
* keeps the helper local to the developer
* avoids changing shared project structure

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

Optional global Git config example:

```ini
[task "kube-image-inventory"]
	baseBranch = main
	branchPrefix = feature
	includeTitle = true

[task "cloud"]
	baseBranch = main
	branchPrefix = docs
	includeTitle = true

[task "legacy-project"]
	baseBranch = master
	branchPrefix = bugfix
	includeTitle = false
```

This configuration is stored in the user’s global Git configuration, not in the repository.

## Expected Behavior

The command:

```bash
git task DEVOPS-123 "add terraform validation"
```

should:

* verify that the current directory is inside a Git repository
* detect the repository name from `origin`
* detect the default base branch
* read optional global config for that repository
* fetch latest changes
* check out the remote base branch
* create a new branch
* slugify the short description
* print what it did

It should not:

* push the branch automatically
* modify Jira automatically
* delete branches
* run destructive commands
* require repository-local config files

## Example Script

Path:

```text
~/bin/git-task
```

Script:

```bash
#!/usr/bin/env bash
set -euo pipefail

task_id="${1:-}"
title="${2:-}"

if [[ -z "$task_id" ]]; then
  echo "usage: git task <jira-ticket> [short description]"
  echo "example: git task DEVOPS-123 \"add terraform validation\""
  exit 1
fi

if ! git rev-parse --is-inside-work-tree >/dev/null 2>&1; then
  echo "error: not inside a Git repository"
  exit 1
fi

repo_name="$(basename -s .git "$(git config --get remote.origin.url 2>/dev/null || echo unknown)")"

detect_base_branch() {
  local head_ref

  head_ref="$(git symbolic-ref refs/remotes/origin/HEAD 2>/dev/null || true)"

  if [[ -n "$head_ref" ]]; then
    echo "${head_ref#refs/remotes/origin/}"
    return
  fi

  if git show-ref --verify --quiet refs/remotes/origin/main; then
    echo "main"
    return
  fi

  if git show-ref --verify --quiet refs/remotes/origin/master; then
    echo "master"
    return
  fi

  echo "main"
}

slugify() {
  echo "$1" \
    | tr '[:upper:]' '[:lower:]' \
    | sed -E 's/[^a-z0-9]+/-/g' \
    | sed -E 's/^-+|-+$//g'
}

base_branch="$(git config --get "task.${repo_name}.baseBranch" || detect_base_branch)"
branch_prefix="$(git config --get "task.${repo_name}.branchPrefix" || echo "feature")"
include_title="$(git config --get "task.${repo_name}.includeTitle" || echo "true")"

if [[ "$include_title" == "true" && -n "$title" ]]; then
  branch="${branch_prefix}/${task_id}-$(slugify "$title")"
else
  branch="${branch_prefix}/${task_id}"
fi

echo "Repository:  $repo_name"
echo "Base branch: $base_branch"
echo "New branch:  $branch"

git fetch origin
git checkout "origin/${base_branch}"
git checkout -b "$branch"

echo "Branch created successfully."
```

## Installation

```bash
mkdir -p ~/bin
chmod +x ~/bin/git-task
```

Ensure `~/bin` is in `$PATH`:

```bash
export PATH="$HOME/bin:$PATH"
```

## Global Config Examples

Example for normal feature work:

```bash
git config --global task.kube-image-inventory.baseBranch main
git config --global task.kube-image-inventory.branchPrefix feature
git config --global task.kube-image-inventory.includeTitle true
```

Example for documentation repository:

```bash
git config --global task.cloud.baseBranch main
git config --global task.cloud.branchPrefix docs
git config --global task.cloud.includeTitle true
```

Example for older repository using `master`:

```bash
git config --global task.legacy-project.baseBranch master
git config --global task.legacy-project.branchPrefix bugfix
git config --global task.legacy-project.includeTitle false
```

## More Usage Examples

```bash
git task DEVOPS-410 "add runbook template"
```

Creates:

```text
feature/DEVOPS-410-add-runbook-template
```

```bash
git task DEVOPS-411 "improve adr template"
```

With `cloud` configured to use `docs` prefix, creates:

```text
docs/DEVOPS-411-improve-adr-template
```

```bash
git task DEVOPS-500 "fix deployment pipeline"
```

Creates:

```text
feature/DEVOPS-500-fix-deployment-pipeline
```

## Optional Jira Integration

Jira integration can be added later, but should not be enabled by default.

Possible future behavior:

* read ticket title from Jira
* move ticket to “In Progress”
* print ticket URL
* validate that the ticket exists

This should remain optional. Jira integration should not be implemented in the main script because different repositories and teams may use different Jira projects, workflows, statuses, or permissions.

## Safety Notes

* the helper only creates a local branch
* it does not push automatically
* it does not modify Jira by default
* it does not delete anything
* it does not run destructive commands
* users should review the branch name before pushing

## Summary

* `git task` is a small local Git helper
* it starts work on Jira-style tasks consistently
* it avoids repository-local config files
* it uses default branch detection
* it supports optional global Git config
* it can be extended later, but should stay simple
