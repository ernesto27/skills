---
name: push-github-tag
description: Determine the latest stable semantic-version Git tag, increment its patch version, and create and push the new tag to GitHub. Use when releasing a patch tag such as v0.0.0 to v0.0.1.
---

# Push GitHub Tag

Create one new patch release tag from the current repository state.

## Determine the version

1. Confirm the current directory is a Git repository with a configured `origin` remote.
2. Fetch tags from `origin` before deciding the next version.
3. Consider only stable tags that exactly match `vMAJOR.MINOR.PATCH`, such as `v1.2.3`. Ignore prerelease, build-metadata, and non-semantic tags.
4. Use semantic-version ordering (not lexical ordering) to select the latest matching tag. Increment only its patch component. For example, `v0.0.0` becomes `v0.0.1`, and `v1.9.9` becomes `v1.9.10`.
5. If no stable semantic-version tag exists, propose `v0.0.1` and state that this is the initial version assumption.

## Safety checks

- Verify the proposed tag does not already exist locally or on `origin`.
- Report the latest tag, proposed tag, target commit (`HEAD`), and working-tree status.
- Do not create a tag from a dirty worktree unless the user explicitly directs it after seeing the status.
- Never delete, force-update, or overwrite a tag.

## Confirmation and push

Before either creating or pushing the tag, ask for explicit confirmation that includes the exact proposed tag. Treat approval of an earlier plan as insufficient.

After confirmation, create an annotated tag at `HEAD` using the proposed version as its message, then push only that tag to `origin`. Report the pushed tag and commit. If either command fails, stop and report the exact failure; do not retry with force options.