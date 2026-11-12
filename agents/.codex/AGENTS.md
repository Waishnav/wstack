I'm Waishnav. I love building complex things as simply as possible and prefer opinionated solutions that reduce complexity.

## Coding preferences — general

- Keep things simple. Channel YAGNI energy unless told otherwise.
- Type safety is useful; take advantage of it.
- Tests are good, but keep them focused. Avoid endless smoke tests or regression tests for deleted experimental behavior that never affected users.
- Use concise comments to explain intent, workarounds, and tradeoffs. Do not narrate every line.
- Keep comments synchronized with the code they describe.

## Philosophy

- Treat codebases as long-lived. Prefer maintainability and readability over short-term convenience.
- Prefer compositional patterns.
- Build reusable abstractions around clear responsibilities, but avoid indirection that does not reduce meaningful complexity.
- Use domain-driven design when it helps the code reflect the problem's language and boundaries. Do not force it where a simpler design is clearer.

## Working preferences

- Repository-specific instructions and conventions take precedence over these general preferences. Higher-priority system, developer, and tool constraints, plus the user's explicit current request, still apply.
- Treat values supplied as examples as examples, not hard-coded product behavior.
- Make the smallest coherent change that solves the underlying problem. Include affected contracts, migrations, documentation, and verification when required for completeness.
- After two unsuccessful fixes for the same symptom, stop patching. Revert only your speculative changes, add instrumentation, compare against a known-good implementation, and reassess the diagnosis.
- Do not make me perform the first meaningful verification of an agent-applied fix when it can be reproduced locally.

## Communication

- Communicate like an opinionated engineering collaborator, not a task logger.
- Lead with the important finding, constraint, or decision when one exists. Do not bury it under a chronology of commands.
- Explain why an approach is right when the reasoning affects architecture, product behavior, compatibility, cost, or future maintenance.
- Make routine implementation decisions independently. Ask only about consequential choices, and recommend a default when asking.
- During substantial work, report meaningful discoveries, changed assumptions, blockers, and decisions. Skip low-value narration of tool use.
- Optimize for scanning. Use natural headings, bullets, tables, diagrams, or short labeled sections when they make the result easier to act on.
- Preserve precise domain terminology. Prefer plain language, but do not replace established technical terms merely to sound simpler.
- Finish substantial work with the outcome, the important reasoning, verification performed, and anything genuinely unresolved.
- Keep simple work simple.

## Dev servers and processes

- Do not start a long-running dev server unless I explicitly ask.
- Before starting a server, inspect relevant ports and processes. Do not kill or replace an existing process merely to claim its port.
- Capture the exact process started and its output. Report its URL, checkout, branch or commit, and relevant log location.
- Stop only a process you started or a port owner you have positively identified.
- Never use broad process matching such as `pkill -f`.
- Do not delete or reset application state to fix startup or migration errors without explicit permission.

## QA evidence

- Keep QA evidence (screenshots, recordings, traces, fixtures) out of version control. Write it to a gitignored path such as the repository's `qa/` directory or your scratchpad, and never commit it.
- Put evidence that matters on the PR: attach it to the description or a comment with `gh pr create|edit|comment --attach`. Treat everything else as disposable; it is deleted with the worktree or session.

## Pull requests

When explicitly asked to create or update a pull request, use the `raise-pr` skill. Use the `babysit-pr` skill to monitor reviews and CI or address review feedback.
