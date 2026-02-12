# git-workflow-haiku

Claude Code plugin that runs git workflow commands using Haiku sub-agents for cost-effective, fast execution.

## Commands

| Command | Description |
|---------|-------------|
| `/git-workflow-haiku:commit` | Create a git commit with auto-generated message |
| `/git-workflow-haiku:merge-pr` | Merge a PR with CI validation and cleanup |
| `/git-workflow-haiku:commit-push-pr` | Commit, push, and create a PR in one step |
| `/git-workflow-haiku:clean_gone` | Clean up local branches deleted on remote |

## Installation

```
/plugin marketplace add drillan/git-workflow-haiku
/plugin install git-workflow-haiku@git-workflow-haiku
```

## Usage

### `/git-workflow-haiku:commit`

Analyzes staged/unstaged changes and creates a commit with an appropriate message.

```
/git-workflow-haiku:commit
```

### `/git-workflow-haiku:merge-pr`

Validates PR status, waits for CI, executes merge, and cleans up branches/worktrees.

```
/git-workflow-haiku:merge-pr 123
/git-workflow-haiku:merge-pr 123 --rebase
/git-workflow-haiku:merge-pr 123 --merge
```

Default strategy: `--squash`

### `/git-workflow-haiku:commit-push-pr`

Creates a branch (if on main), commits changes, pushes to remote, and opens a PR.

```
/git-workflow-haiku:commit-push-pr
```

### `/git-workflow-haiku:clean_gone`

Removes local branches marked as `[gone]` and their associated worktrees.

```
/git-workflow-haiku:clean_gone
```

## Architecture

Each command delegates to a dedicated Haiku sub-agent via the Task tool:

| Command | Agent | Tools |
|---------|-------|-------|
| `commit` | git-committer | Bash |
| `merge-pr` | pr-merger | Bash, Read |
| `commit-push-pr` | pr-creator | Bash |
| `clean_gone` | branch-cleaner | Bash |

All agents run on `claude-haiku-4-5`, providing fast execution at lower cost compared to Sonnet/Opus.

## License

MIT
