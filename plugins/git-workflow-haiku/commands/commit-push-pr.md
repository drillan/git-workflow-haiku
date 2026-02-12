---
description: Commit, push, and open a PR using Haiku sub-agent
---

## /commit-push-pr

Complete workflow: create branch → commit changes → push to remote → create pull request.

### Usage

```
/commit-push-pr
```

### What it does

1. Checks current branch
2. Creates a new branch if on main/master
3. Creates a commit with generated message
4. Pushes the branch to remote
5. Creates a pull request via `gh pr create`
6. Returns the PR URL

### Instructions

When the user invokes `/commit-push-pr`:

1. Use the Task tool to invoke the `pr-creator` agent:
   - subagent_type: `pr-creator`
   - description: `Create PR workflow`
   - prompt: `Commit changes, push to remote, and create a pull request`

2. Present the agent's result to the user (PR URL, success summary, or error with resolution)

3. Do not send any other text or messages besides the Task tool call.

### Workflow

The pr-creator agent executes:

**Phase 1: Branch Management**
- If on main/master: create feature branch
- If already on feature branch: use existing branch

**Phase 2: Create Commit**
- Stage relevant files
- Generate commit message based on changes
- Create commit with `Co-Authored-By` trailer

**Phase 3: Push to Remote**
- Push branch with `-u` (upstream tracking)
- Set default pull target to origin

**Phase 4: Create PR**
- Analyze all changes (git diff main...HEAD)
- Generate PR title (from commit message)
- Generate PR description with:
  - Summary (1-3 bullet points)
  - Test plan (markdown checklist)
  - Claude Code attribution
- Create PR using `gh pr create`
- Return PR URL

### Error Handling

The pr-creator agent handles all phases:
- Branch creation errors
- No changes to commit
- Secret files detection
- Push failures (conflicts, permissions)
- gh CLI not available
- PR creation failures

Each error includes specific resolution guidance.

### Safety

The agent enforces safety rules from CLAUDE.md:
- Never use --amend (creates NEW commit instead)
- Never force push (no --force or --force-with-lease)
- Never commit secrets (.env, credentials, keys)
- Always wait for verification if errors occur

### Requirements

- `gh` CLI must be installed and authenticated
- Working git repository
- Origin remote configured
