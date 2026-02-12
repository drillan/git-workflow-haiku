---
description: Create a git commit using Haiku sub-agent
---

## /commit

Create a single git commit with automatically generated commit message.

### Usage

```
/commit
```

### What it does

1. Analyzes current staged and unstaged changes
2. Generates an appropriate commit message
3. Stages relevant files
4. Creates the commit with `Co-Authored-By` trailer

### Instructions

When the user invokes `/commit`:

1. Use the Task tool to invoke the `git-committer` agent:
   - subagent_type: `git-committer`
   - description: `Create git commit`
   - prompt: `Create a git commit based on current changes`

2. Present the agent's result to the user (success message, error with resolution, or warning about secrets)

3. Do not send any other text or messages besides the Task tool call.

### Error Handling

The git-committer agent handles all error conditions:
- No changes to commit
- Secret files detection
- Commit hook failures
- Git permission issues

If the agent encounters errors, it will report them with specific resolution guidance.

### Safety

The agent enforces safety rules from CLAUDE.md:
- Never use --amend after hook failure (creates NEW commit instead)
- Never skip hooks (--no-verify)
- Never commit secrets (.env, credentials, keys)
