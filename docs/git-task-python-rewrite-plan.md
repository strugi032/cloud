# Git Task Python Rewrite Plan

## Goal

Rewrite `scripts/git-task` from Bash to Python while preserving the intended user-facing workflow and behavior.

The current Bash version is work in progress and has not been used as a trusted production baseline. Treat it as an implementation sketch, not as a fully validated source of truth. Preserve the documented workflow, but prefer clear, understandable Python over copying Bash-specific structure.

The Python version should be simple to read, easier to maintain, easier to test, and less dependent on shell utilities. It should prefer purpose-built Python libraries over manually wrapping command-line tools wherever that is practical.

## Current Behavior To Preserve

The rewritten helper must keep the existing command name and CLI shape:

```bash
git-task [OPTIONS] <ticket> [manual title]
```

Supported options must remain:

- `--type <type>`: override the generated branch prefix.
- `--no-jira`: skip all Jira API calls.
- `--dry-run`: print intended actions without mutating Git or Jira state.
- `--open`: open the Jira issue after processing.
- `--no-transition`: read Jira but do not transition the issue.
- `--no-comment`: read Jira but do not add a comment.
- `--help`: show usage.

The rewritten helper must continue to:

- Run from inside the target Git repository.
- Require an `origin` remote.
- Fetch the latest remote refs during normal execution.
- Detect the base branch from config, `origin/HEAD`, `origin/main`, or `origin/master`.
- Read Jira issue summary and issue type unless `--no-jira` is used.
- Generate a branch name in this format:

```text
<type>/<ticket-key>-<slugified-title>
```

- Create a local branch from `origin/<base-branch>`.
- Transition the Jira ticket to the configured status unless disabled.
- Add a Jira comment with the generated branch unless disabled.
- Print the final push command.

## Preferred Python Libraries

Use Python libraries for domain behavior instead of manually invoking shell commands where possible.

Recommended dependencies:

```text
GitPython
jira
```

Use standard-library modules for the rest:

- `argparse` for CLI parsing.
- `os` for environment variables.
- `re` for slug generation.
- `pathlib` for path handling.
- `webbrowser` for opening Jira issues.
- `sys` for process exit codes.

Avoid using `curl`, `jq`, `sed`, `base64`, or other shell utilities from the Python implementation.

## Git Implementation

Use `GitPython` for normal Git operations.

Expected responsibilities:

- Locate the repository:

```python
Repo(search_parent_directories=True)
```

- Read repository root and name from the `Repo` object.
- Verify the `origin` remote exists through `repo.remotes`.
- Fetch remote refs with pruning:

```python
repo.remotes.origin.fetch(prune=True)
```

- Read Git configuration through `GitPython` config readers.
- Check whether a local branch already exists through `repo.heads`.
- Create the branch from `origin/<base-branch>` with `repo.create_head`.
- Check out the newly created branch.

Avoid manually wrapping `git` commands unless `GitPython` cannot handle a specific operation cleanly.

## Jira Implementation

Use the `jira` Python package for Jira behavior.

Expected responsibilities:

- Read these environment variables:

```text
JIRA_BASE_URL
JIRA_EMAIL
JIRA_API_TOKEN
```

- Create a Jira client with basic auth.
- Fetch the issue by ticket key.
- Read:

```text
issue.fields.summary
issue.fields.issuetype.name
```

- Find a transition by name, case-insensitively if needed.
- Transition the issue.
- Add a comment such as:

```text
Started work on branch <branch-name>
```

If the `jira` package cannot reliably create the desired Jira Cloud comment format, it is acceptable to use a small `requests` helper only for the comment endpoint. That should be treated as an exception, not the default approach.

## Branch Type Mapping

Preserve the current mapping:

| Jira issue type | Branch type |
| --- | --- |
| `Bug` | `bugfix` |
| `Story` | `feature` |
| `Task` | `feature` |
| `Improvement` | `feature` |
| `Incident` | `hotfix` |
| `Documentation` | `docs` |

Unknown issue types should fall back to `task.defaultType` from Git config, or `feature` if that config is not set.

The `--type` flag must override Jira-derived and configured defaults.

## Git Configuration

Preserve the existing config keys:

```ini
[task "<repo-name>"]
	baseBranch = main
	jiraTransition = In Progress
	addComment = true

[task]
	defaultType = feature
```

Behavior:

- `task.<repo-name>.baseBranch` overrides base branch detection.
- `task.<repo-name>.jiraTransition` overrides the default Jira transition target.
- `task.<repo-name>.addComment = false` disables Jira comments by default.
- `task.defaultType` overrides the fallback branch type.

## Error Handling

The Python version should produce clear errors and non-zero exit codes for fatal failures.

Fatal failures include:

- Not running inside a Git repository.
- Missing `origin` remote.
- Missing ticket argument.
- Missing manual title when `--no-jira` is used.
- Missing Jira environment variables when Jira is enabled.
- Jira issue cannot be fetched.
- Jira summary or issue type cannot be read.
- Base branch cannot be detected.
- Local branch already exists.
- Branch creation fails.
- Requested Jira transition is not available.

If Git branch creation succeeds but a Jira transition or comment fails, do not hide the problem. Print a clear error or warning explaining what failed, keep the branch in place, and tell the user to move the ticket to the target status manually and/or add the branch comment manually.

Non-fatal Jira failures after successful local Git work should not make the local branch unusable. The script should leave the user with a meaningful message and the final push command when possible.

## Dry Run Behavior

`--dry-run` must avoid all mutations.

It may read Git state, read Git config, fetch Jira issue data, and compute branch names. It must not fetch remote refs, because `git fetch` mutates local remote-tracking state. It must not:

- Create a branch.
- Check out a branch.
- Transition a Jira issue.
- Add a Jira comment.
- Open a browser, even when `--open` is passed.

The dry-run output should include the same important fields as the current script:

- Repository name.
- Ticket key.
- Summary.
- Issue type.
- Base branch.
- New branch.
- Jira URL when Jira is enabled.
- Planned transition.
- Planned comment.

## File Layout

Keep the executable script path stable:

```text
scripts/git-task
```

Add dependency metadata using one of these simple options:

```text
requirements.txt
```

or:

```text
pyproject.toml
```

For this repository, `requirements.txt` is likely enough unless broader Python packaging is introduced. Keep setup simple and document the exact install command. The script should use a Python shebang, remain executable, and show a friendly message if required Python packages are missing.

## Documentation Updates

Update `docs/git-task-helper.md` after the rewrite. The README/documentation should be good enough for someone who has not used the old Bash script before.

Required changes:

- Replace Bash implementation notes with Python notes.
- Replace `curl` and `jq` requirements with Python 3 plus Python package requirements.
- Document dependency installation.
- Keep the Jira environment variable documentation.
- Keep all existing usage examples unless behavior intentionally changes.
- Mention that Git operations are handled through `GitPython`.
- Mention that Jira operations are handled through the `jira` Python package.

## Suggested Implementation Steps

1. Add dependency metadata for `GitPython` and `jira`.
2. Replace `scripts/git-task` with a Python script using `argparse`.
3. Implement pure helper functions first:
   - `slugify`
   - issue type mapping
   - boolean Git config parsing
   - branch name generation
4. Implement Git repository loading and config reading with `GitPython`.
5. Implement base branch detection.
6. Implement Jira issue loading with the `jira` package.
7. Implement dry-run output before adding mutation code.
8. Implement branch creation and checkout.
9. Implement Jira transition and comment behavior.
10. Implement browser opening with `webbrowser`.
11. Update documentation.
12. Run verification commands.

## Verification

At minimum, run:

```bash
python3 -m py_compile scripts/git-task
scripts/git-task --help
scripts/git-task --no-jira --dry-run DEVOPS-123 "add terraform validation"
```

If Jira credentials are available, also test:

```bash
scripts/git-task --dry-run DEVOPS-123
```

Only run a non-dry-run Jira test against a real ticket when the ticket is safe to transition and comment on.

## Testing Recommendations

Add focused local tests where practical. The old Bash script is WIP and untrusted, so tests should validate the intended behavior described in this plan rather than blindly matching every Bash detail.

High-value test targets:

- Slug generation.
- Issue type mapping.
- Branch type override precedence.
- Required manual title in `--no-jira` mode.
- Git config fallback behavior.
- Dry-run does not fetch, create branches, check out branches, transition Jira, comment, or open a browser.
- Jira transition matching by name.
- Jira transition/comment failure after branch creation prints clear manual follow-up instructions.

Avoid tests that require real Jira credentials or mutate a real Git remote. Use mocks or temporary local repositories for those cases.

## Acceptance Criteria

The rewrite is complete when:

- `scripts/git-task` is Python.
- The documented CLI behavior is preserved.
- Normal Git work is implemented through `GitPython`.
- Normal Jira work is implemented through the `jira` package.
- No `curl` or `jq` dependency remains.
- Documentation matches the Python implementation.
- Basic verification commands pass.
- Dry-run is fully non-mutating.
- Jira failures after branch creation produce clear manual follow-up instructions.
