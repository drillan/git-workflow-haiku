---
description: Clean up gone branches using Haiku sub-agent
---

## /clean_gone

Clean up all local branches marked as [gone] (deleted on remote) and their associated worktrees.

### Usage

```
/clean_gone
```

### What it does

1. Identifies all branches marked as [gone] in `git branch -v`
2. Finds associated worktrees for each [gone] branch
3. Removes worktrees using `git worktree remove`
4. Deletes branches using `git branch -D`
5. Reports cleanup summary

### Instructions

When the user invokes `/clean_gone`:

1. Use the Task tool to invoke the `branch-cleaner` agent:
   - subagent_type: `branch-cleaner`
   - description: `Clean gone branches`
   - prompt: `Clean up all local branches marked as [gone] including worktrees`

2. Present the agent's result to the user (cleanup summary or "no cleanup needed")

3. Do not send any other text or messages besides the Task tool call.

### How [gone] branches appear

After running `git fetch --prune`, branches deleted on remote but still local are marked with [gone]:

```bash
$ git branch -v
* main              abc1234 [Your commit message]
  feature/old       def5678 [gone]
  +feature/with-wt  ghi9012 [gone]
```

- `[gone]` marker indicates remote branch no longer exists
- `+` prefix indicates associated worktree exists

### Cleanup Process

The branch-cleaner agent executes:

1. **Identification**: Find all branches with [gone] marker
2. **Worktree detection**: Check if each [gone] branch has an associated worktree
3. **Worktree removal**: Delete worktrees using `git worktree remove --force`
4. **Branch deletion**: Delete branches using `git branch -D`
5. **Report**: List all removed branches and worktrees

### Error Handling

The branch-cleaner agent handles all errors:
- Worktree removal failures (continues with branch deletion)
- Branch deletion failures
- Permission denied errors
- No [gone] branches (reports "No cleanup needed")

All errors are reported with the specific branch/worktree involved.

### Safety

- Never deletes main or master branch (protection)
- Forces worktree removal to ensure cleanup
- Continues on errors (doesn't stop after first failure)
- Verifies deletion and reports results

### Common Scenarios

**Scenario 1: No [gone] branches**
```
ℹ️ No cleanup needed
No branches marked as [gone] found.
```

**Scenario 2: Branches with worktrees**
```
✅ Cleanup completed successfully

Branches removed: 2
Worktrees removed: 1

Removed:
- feature/with-wt (worktree: /path/to/worktree)
- feature/old (no worktree)
```

**Scenario 3: Cleanup with errors**
```
⚠️ Cleanup completed with warnings

Branches removed: 1
Worktrees removed: 0
Failed: 1

Removed:
- feature/old (no worktree)

Failed:
- feature/with-wt (error: worktree path locked)
  Resolution: Manually run: git worktree remove --force <path>
```

### Prerequisites

- Local git repository
- `git fetch --prune` should have been run to mark remote-deleted branches as [gone]
- Branches marked [gone] must actually be deleted on remote
