---
name: stacked-prs
description: Create and manage GitHub stacked PRs with gh stack. Use for stacked/dependent PRs, multi-layer PR workflows, cascading rebase, or breaking a large change into reviewable layers.
---

# Stacked PRs (`gh stack`)

GitHub Stacked PRs are recent and probably absent from your training data, so trust this file and `gh stack <command> --help` over memory. They are in public preview and work only within one repository (no forks). The CLI is the `github/gh-stack` extension; this file was written against v0.3.0. Run `gh stack --version`, and if it is older, tell the user and suggest `gh extension upgrade stack`. Docs: https://gh.io/stacks

## Model

```
top    feat/ui   → PR base: feat/api
       feat/api  → PR base: feat/auth
bottom feat/auth → PR base: main (trunk)
```

Dependencies point down. Each PR shows only its own layer's diff, and GitHub groups the PRs into one numbered stack. Split a stack where the concern changes (schema → API → UI) or where a layer is large enough to review on its own.

## Commands

| Do | Command |
| --- | --- |
| Start | `gh stack init [branches...] [-b trunk]` |
| Next layer | `gh stack add [branch] [-Am "msg"]` (only from the top) |
| Push and open PRs | `gh stack submit --auto` (non-interactive; drafts unless `--open`) |
| Push only | `gh stack push` (atomic by default) |
| Sync after merges | `gh stack sync [--prune]` |
| Cascade a rebase | `gh stack rebase [--upstack\|--downstack]`, then `gh stack push` |
| Navigate | `gh stack up/down [n]`, `top`, `bottom`, `trunk` |
| Check out a stack | `gh stack checkout <stack#\|PR#\|URL\|branch>` |
| Inspect | `gh stack view --json` (PR numbers per branch) or `--short` |
| Link existing PRs | `gh stack link <bottom> … <top>` (branches, PR numbers or URLs; needs no local tracking) |
| Merge | `gh stack merge [stack#\|PR#] --yes [--squash]` |
| Dissolve | `gh stack unstack [--local]` |

`modify`, interactive `submit` and the merge wizard open full-screen editors. Agents should avoid them, or pass the flag that skips them.

## Agent flow

```bash
gh stack init feat/auth            # or adopt existing branches: gh stack init b1 b2 b3
# commit the layer…
gh stack add -Am "API routes" feat/api
# commit the layer…
gh stack submit --auto             # pushes every branch and opens draft PRs with generated titles
gh stack view --json               # map branches to PR numbers
```

`--auto` produces placeholder titles and empty bodies. Then write each layer with the `raise-pr` skill and apply it with `gh pr edit <n> --title … --body-file … [--attach …]`. Each body should say where the layer sits ("Part 2 of 3. Stacked on #240") and cover only that layer's change. Run `gh pr ready <n>` (or `submit --open`) when the stack is ready for review.

If the PRs already exist with hand-set bases, `gh stack link <bottom-PR> … <top-PR>` groups them without rewriting anything.

**Fix a lower layer:** check out the branch that owns the code, commit there, run `gh stack rebase --upstack`, then `gh stack push`. Never patch a lower layer's bug in an upper layer.

**After the bottom merges:** run `gh stack sync --prune`. When the merged base branch is deleted, GitHub retargets the stack to the default branch, and `sync` and `rebase` follow it. If the old base never merged, they ask first; `--accept-base-change` skips that question.

## Worktrees

Since v0.2, stack tracking belongs to the repository, not the directory, so every worktree sees the same stacks. Branches of one stack can live in different worktrees. `rebase`, `sync` and `modify` run each change in the checkout that holds the branch, and they stop if any of those checkouts has uncommitted files. `gh stack` never creates, removes or stashes worktrees. This requires Git 2.36 or later.

## Rules that bite

1. `submit` creates and updates PRs; `sync` never opens them.
2. Merging is atomic from the bottom up to the chosen PR. An upper layer cannot merge on its own, and branch protection is checked when the merge runs.
3. Stacks need linear history. In repositories that require signed commits, rebase locally, because server-side rebases are not signed.
4. Once a stack is fully merged it is closed, and a further `submit` starts a new stack on the trunk.
5. `add` works only from the top, and `modify` needs a clean tree.
6. Never push or open PRs unless the user asked for it.
