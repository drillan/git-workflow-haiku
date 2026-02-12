# /merge-pr

Wait for PR CI checks to complete, then execute merge.

## Usage

```
/merge-pr <pr-number> [--merge|--rebase]
```

## Arguments

| Argument | Type | Required | Description |
|----------|------|----------|-------------|
| `pr-number` | integer | Yes | GitHub PR number |
| `--merge` | flag | No | Create a merge commit |
| `--rebase` | flag | No | Rebase merge |

Default: `--squash` (squash commits into one)

## Instructions

When the user invokes `/merge-pr <number> [options]`:

1. Use the Task tool to invoke the `pr-merger` agent with the full argument string (e.g., `"123 --squash"`)
   - agent_type: `"pr-merger"`
   - Provide the PR number and merge strategy flags as the prompt

2. The agent handles all workflow phases:
   - **Phase 1**: PR validation (exists, not merged, mergeable)
   - **Phase 2**: CI validation (watch checks, ask for confirmation if failed)
   - **Phase 3**: Merge execution (execute with specified strategy, delete remote branch)
   - **Phase 4**: Post-merge cleanup (switch to main, pull, delete worktree and local branch)

3. Present the agent's result to the user:
   - Success: Show merge summary (method, branch cleanup status)
   - Failure: Show error with resolution guidance
   - Cancellation: Show cancellation reason (e.g., CI failed and user declined)

## Output Format

### Success

```
✅ PR #100 merged successfully

Merge method: squash
Base branch: main
Remote branch: deleted
Local branch: deleted
Worktree: deleted (if applicable)
```

### Failure

```
❌ Failed to merge PR #100

Cause: [specific cause]
Resolution: [suggestion]
```

## Error Handling

| Error | Action |
|-------|--------|
| PR not found | `⚠️ PR #N not found` |
| PR already merged | `ℹ️ PR #N is already merged` |
| PR closed | `⚠️ PR #N is closed` |
| Has conflicts | Provide conflict resolution guidance |
| CI failed | Show failed checks and ask for confirmation |
| Merge blocked | Show block reason (branch protection, etc.) |

## Safety Checks

1. **Never force merge** without explicit user confirmation
2. **Always show diff** before merge if changes are large
3. **Warn about breaking changes** if detected in commit messages
4. **Suggest squash** for PRs with many small commits
