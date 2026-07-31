# sdlc-graph-engineering — repository guide

A single-plugin Claude Code marketplace. The plugin installs **graph engineering** into someone
else's project. This file is short by design; the method itself lives in the skill.

## Sources of truth

- Marketplace manifest: `.claude-plugin/marketplace.json` (name: `sdlc-graph-engineering`)
- Plugin manifest: `plugins/sdlc-graph-engineering/.claude-plugin/plugin.json`
- **The method**: `plugins/sdlc-graph-engineering/skills/sdlc-graph-engineering-install/SKILL.md`
- References: `references/{templates,evals,failure-modes}.md` beside it

```
.claude-plugin/marketplace.json
plugins/sdlc-graph-engineering/
├── .claude-plugin/plugin.json
└── skills/sdlc-graph-engineering-install/{SKILL.md,references/}
docs/assets/            screenshots used by README.md
```

## Changing the skill — REQUIRED

1. **Keep the plugin self-contained.** Reference only files inside
   `plugins/sdlc-graph-engineering/`; never `../`. Use `${CLAUDE_PLUGIN_ROOT}` for absolute paths.
   This is not style — a plugin that reads above its own root breaks on install.
2. **A new rule belongs beside a new failure mode.** Every rule in `SKILL.md` exists because a real
   graph broke without it. If you cannot name the failure it prevents, it is advice, not a rule —
   add the entry to `references/failure-modes.md` first, then the rule that references it.
3. **Re-check the counts you published.** The number of failure modes, steps, and stop types appears
   in `SKILL.md`, both READMEs and the manifests. They drift silently, and they are the first thing a
   reader uses to decide whether the docs are current.
4. **Bump `version` in BOTH manifests** — `plugin.json` and `marketplace.json`. They must agree, and
   installed users only receive the change if it moves.
5. **Validate — must pass:**
   ```bash
   claude plugin validate ./plugins/sdlc-graph-engineering --strict
   claude plugin validate .
   ```
6. **Update `README.md`** (root and plugin) in the same change, not as a follow-up. Skipping this is
   how the catalog drifts from the skill.

## Authoring rules

- Follow the open Agent Skills spec. Real frontmatter fields only — no `metadata`, `license`, or
  `compatibility` keys.
- The skill's `description` states **capability + when to trigger**; that string is the whole
  triggering mechanism, so trigger phrases belong there, not in the body.
- Least-privilege `allowed-tools`. This skill writes files into a user's project — anything that
  broadens what it may touch is a security change, not a convenience.
- The marketplace `name` must never contain "claude" (reserved for official marketplaces).
- Keep this file under 100 lines; describe current state, not history.

## The skill's own standard, applied to itself

The skill tells people to bound their cycles, type their stops, and ship the eval with the change.
Hold this repo to the same bar:

- **No unbounded claim.** "Usually", "as needed", "consider" in a step is the prose vagueness this
  skill exists to remove. Give it a number or a named condition.
- **Derive, never duplicate.** If a summary table and a contract state the same facts, one of them
  will go stale and it will be the one people read.
- **Never edit `references/` and `SKILL.md` in opposite directions.** `SKILL.md` names the step that
  loads each reference; a reference that no step loads is dead weight.

## Repository rules

- **`main` is PR-only.** Push a side branch and open a PR.
- The repo is public. Nothing in it should contain project-internal paths, hostnames, or names from
  the private repos this skill was developed against.
