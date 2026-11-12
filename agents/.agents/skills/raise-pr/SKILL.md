---
name: raise-pr
description: Use when the user asks to file, open, create, raise or update a PR, including each layer of a stacked PR.
---

# Raise PR

Never open or update a PR unless the user explicitly asked for that remote action. Check whether the branch already has a PR and update it instead of opening a duplicate. For dependent PRs, follow the `stacked-prs` skill for the stack mechanics and this skill for each layer's title and body.

## Prepare

1. Read the repository instructions and any PR template. Identify the intended base and review the full diff against it.
2. Confirm that the branch holds one coherent concern. Check for unrelated changes, generated files, debug output, secrets, migrations, configuration changes and documentation drift.
3. Run the verification the repository requires, plus whatever exercises the change for real: the app, a harness or a live run. A PR body can only claim what was actually run.
4. Look at recent PRs in the repository for title conventions.
5. Push only as part of a filing the user requested.

## Title

Follow the repository's convention. Say what the change achieves, not how it was built. Use Conventional Commit form only when the repository does.

> Prefer: `fix(dates): date mentions store dates and read relative, like Notion`
>
> Avoid: `refactor(dates): add DateMention node and relative formatter`

## Body

Write for a reviewer who knows the codebase but not this conversation. Explain behaviour and decisions; the diff already shows the code.

**Opening.** One or two sentences: what this does and why it matters, derived from the user's request or the product problem. For a stacked layer, say where it sits: "Part 1 of 2; the UI is the next PR. Stacked on #240."

**Sections.** Use only the ones that carry information, with sentence-case `##` headings. A small fix may need only the opening and a short Verification section.

| Section | Contents |
| --- | --- |
| `What changed` | Behaviour-level bullets grouped by area, each with a **bold lead-in**. Name the files, flags, settings or commands a reviewer must know about, never a file inventory. |
| `Why` | The problem and observable impact, when the opening can't carry it. Include the rejected alternative when a reviewer would ask about it. |
| Domain sections | Free-form headings such as `Semantics`, `Contracts and migration` or `Agents and import`, when an area has its own rules. Use a table for matrices of types, conditions or platforms. |
| `Review` | Findings from an independent review (name the reviewer and model) and what was fixed in this PR as a result. |
| `Verification` | What was actually exercised, against what, with concrete results: the scenario run and what happened, or "engine 85/85: every type and condition, both week starts". End with the checks that passed (type-check, lint, tests, build) in one line. |
| `Unverified` or `Known gaps` | What was not run or is intentionally missing: other platforms, other harnesses, long-running behaviour, follow-up work. Always include it when such gaps exist. |

**Evidence.** For visible behaviour, attach screenshots or recordings with alt text that says what the image shows (see below).

**Style.** Use plain, specific language and keep the domain terms. Put numbers where they exist (limits, timings, counts). Prefer short bullets to long paragraphs, but give each bullet a full sentence. Keep it proportional: a large layer can run to a few screens, while a one-line fix gets a few lines.

Do not include:

- inventories of files, functions, commits or implementation steps
- a `Summary` section that repeats the opening, or bullets that merely restate the diff
- raw command transcripts, CI status, or "tests pass" with nothing about what they exercised
- generic filler such as "improves maintainability" or "ensures robustness"
- claims about runs that did not happen

Add attribution only through the footer the harness or repository already prescribes.

## Attach evidence

Use `gh pr create --attach <file>` for a new PR, `gh pr edit <pr> --attach <file>` to update one, or `gh pr comment <pr> --attach <file>` for evidence that belongs in a follow-up comment, such as a re-check after review fixes. Repeat `--attach` for several files and append `#alt text` to each path, for example `--attach './routine-select.png#Hands settings with the Routine context select'`.

If the body references a local path, such as `![Before](./before.png)`, attach the same file and `gh` rewrites the reference to the uploaded URL. Attached files the body doesn't reference are appended. Fall back to a browser upload only when `--attach` fails.

Keep evidence files out of the repository; write them to a gitignored path or the scratchpad. Attach only what a reviewer needs. Anything not attached is disposable.

## Submit

Write the body to a file and pass it with `--body-file`, so that quoting and backticks survive. Open a ready PR by default; use a draft only when the user asks or the work is intentionally incomplete.

Report the PR URL afterwards. Keep your handoff to the user separate from the PR body, and tell them about anything left unresolved.
