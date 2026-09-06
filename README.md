# agent-dots

Version-controlled global agent instructions and skills, stowed into `$HOME`.
Mirrors the `~/dotfiles` (GNU Stow) workflow.

## Layout

Single Stow package `agents/` holds the whole tree. Canonical sources live in
one place; per-agent paths are symlinks to them:

- Instructions: `agents/.codex/AGENTS.md` is canonical.
  `agents/.claude/CLAUDE.md` and `agents/.config/opencode/AGENTS.md`
  symlink to it. `agents/AGENTS.md` (stows to `~/AGENTS.md`) is separate
  content (OS/dotfiles policy), not the same file.
- Skills: `agents/.agents/skills/<name>` holds the one real copy of every
  **shared** skill. Each harness symlinks only the shared skills it uses.
  **Harness-specific** skills live as real content under their own harness
  dir, exactly where they were before the migration:
  - codex-owned: `cleanup-work`, `frontend-skill`,
    `invariant-driven-engineering` (real dirs under `agents/.codex/skills/`)
  - claude-owned: `plannotator-*` x6 (real dirs under
    `agents/.claude/skills/`)
  - pi-owned: `chrome-devtools-pi` (real dir under
    `agents/.pi/agent/skills/`), plus its own `web-perf` wording override
  - codex (14 linked): agents-sdk, babysit-pr, cloudflare,
    cloudflare-email-service, create-mcp-app, durable-objects, raise-pr,
    sandbox-sdk, web-perf, workers-best-practices, wrangler
    (+ 3 owned above)
  - claude (11 linked): agents-sdk, chatgpt-apps, cloudflare,
    cloudflare-email-service, create-mcp-app, durable-objects,
    react-doctor, sandbox-sdk, web-perf, workers-best-practices, wrangler
    (+ 6 owned above + external omarchy)
  - opencode (9 linked): agents-sdk, cloudflare, cloudflare-email-service,
    create-mcp-app, durable-objects, sandbox-sdk, web-perf,
    workers-best-practices, wrangler
  - pi (10 linked): agents-sdk, chatgpt-apps, cloudflare,
    cloudflare-email-service, durable-objects, react-doctor, sandbox-sdk,
    workers-best-practices, wrangler (+ chrome-devtools-pi owned,
    web-perf override)
  To expose a skill to another harness, add one symlink + commit.

## Install

```bash
cd ~/agent-dots
./install.sh
```

Or one package explicitly: `./install.sh agents`.

## Syncing agent-made changes

Edits to existing files (AGENTS.md, SKILL.md, ...) land in the repo
automatically — those paths are symlinks. But when an agent *adds* a new
skill or command under `$HOME`, it lands outside the repo. Fold it back:

```bash
./sync.sh   # moves additions to canonical layout, restows, reports conflicts
git status --short   # review, then commit + push
```

`sync.sh` never touches secrets, caches, sessions, or machine dirs, and
never commits — that stays your call.

## Verify

```bash
find ~ -xtype l | grep -E 'skills|AGENTS|CLAUDE'  # expect no output
```

## Known drift (not committed, reinstall via skill tool)

These were dangling symlinks with no surviving content at migration time,
so they are intentionally absent. Reinstall them and re-run `./install.sh`
to fold them back in:

- anthropic-frontend-design, figma, grill-me, frontend-design, find-skills,
  playwriter, gws-shared, gws-gmail, gws-calendar, minimalist-entrepreneur,
  subagent-delegation

Lockfiles (`.skill-lock.json`) record original install sources.

## External links

`~/.claude/skills/omarchy` points outside the repo (absolute path into
the omarchy install). Stow refuses absolute symlinks, so it is not part
of the stowed tree — `install.sh` creates it.

## Secrets policy

`*opencode.json*`, `auth.json`, `*.credentials.json`, `cli.json`,
`service.json`, sessions/caches/sqlite are gitignored. Commit
`*.example` templates instead if a config needs sharing.
