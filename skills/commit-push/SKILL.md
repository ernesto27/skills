---
name: commit-push
description: Commit already-staged changes and push to the current branch. Use when the user says "commit and push", "commit-push", "/commit-push", or asks to commit staged changes and push.
disable-model-invocation: true
allowed-tools: Bash(git commit *) Bash(git push *) Bash(git status *) Bash(git diff *) Bash(git log *)
argument-hint: "[commit message]"
---

# Commit & Push

Commit already-staged changes and push to the current branch.

## Steps

1. Run `git diff --cached --stat` to review what will be committed.
2. Write the commit message:
   - If `$ARGUMENTS` is provided, use it verbatim.
   - Otherwise inspect `git diff --cached` and write a short imperative-mood summary (50 chars max subject line).
3. Show the proposed commit message and ask the user to confirm before committing. Wait for approval.
4. Commit: `git commit -m "<message>"` — no Co-Authored-By line
5. Push: `git push`
6. Confirm with `git log --oneline -1`.


