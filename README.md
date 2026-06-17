# agent-skills

Pete's personal Claude Code skill library. Curated from three open-source skill
packs, with overlaps merged by hand instead of multiplied. See [SOURCES.md](./SOURCES.md) for exactly where each
skill came from and [THIRD_PARTY_LICENSES.md](./THIRD_PARTY_LICENSES.md) for
the MIT attribution this repo is legally obligated to carry.

## But wait, what are skills?

Agent Skills are modular, reusable packages of instructions and context that teach AI agents how to perform specialized
tasks without needing conversational hand-holding. Operating as lightweight file-based standards, they allow agents to
load relevant domain expertise and workflows dynamically.

## What's here

47 skills under `skills/`, organized loosely around the software lifecycle:

**Define** — `spec-driven-development`, `idea-refine`, `interview-me`,
`doubt-driven-development`, `domain-modeling`, `codebase-design`

**Plan** — `planning-and-task-breakdown`, `api-and-interface-design`,
`incremental-implementation`

**Build** — `test-driven-development`, `frontend-ui-engineering`,
`code-simplification`, `source-driven-development`, `vercel-react-best-practices`,
`vercel-composition-patterns`, `web-design-guidelines`,
`vanilla-js`, `css-architecture`, `angular`, `vue`, `drupal`

**Accessibility & Standards** — `accessibility-508-wcag`, `uswds`, `vads`

**SEO** — `seo`

**Verify** — `code-review-and-quality`, `debugging-and-error-recovery`,
`browser-testing-with-devtools`, `security-and-hardening`, `performance-optimization`

**Ship** — `git-workflow-and-versioning`, `resolving-merge-conflicts`,
`ci-cd-and-automation`, `shipping-and-launch`, `deploy-to-vercel`,
`vercel-cli-with-tokens`, `vercel-optimize`, `deprecation-and-migration`

**Operate** — `observability-and-instrumentation`, `documentation-and-adrs`,
`triage`, `handoff`, `git-guardrails-claude-code`, `setup-pre-commit`

**Meta** — `context-engineering`, `using-agent-skills`, `writing-great-skills`

## Install

This follows the same layout Claude Code expects natively: a `skills/`
directory of folders, each with a `SKILL.md`. Two ways to use it:

```bash
# Symlink the whole pack into your global Claude Code skills directory
ln -s "$(pwd)/skills"/* ~/.claude/skills/

# Or, per-project: drop this repo's skills/ contents into a project's
# .claude/skills/ directory so they're scoped to that codebase only
```

A handful of skills have their own subfolders (`rules/`, `references/`,
`resources/`, `scripts/`, `lib/`) alongside `SKILL.md` — those came with the
original skill and need to travel with it; don't flatten them out.

## Dependencies

- `browser-testing-with-devtools` needs the `chrome-devtools` MCP server configured.
- The `vercel-*` skills assume you're authenticated to Vercel (CLI or MCP) for
  anything beyond static guideline-review use.

## Resources

Primary references used for this collection and skill ecosystem browsing:

- https://www.skills.sh/
- https://github.com/vercel-labs/agent-skills
- https://github.com/addyosmani/agent-skills/tree/main
- https://github.com/mattpocock/skills/tree/main

Additional places to find skills and related tooling:

- https://github.com/hesreallyhim/awesome-claude-code
- https://github.com/VoltAgent/awesome-agent-skills
- https://github.com/sickn33/antigravity-awesome-skills

## Why these 47 and not more

The brief was a personal Claude Code setup, not a public showcase, so this
isn't "everything good on skills.sh", it's specifically what's useful for
frontend/design-systems/accessibility work, deduplicated so skills aren't
fighting each other for the same trigger phrases. The full reasoning for every
inclusion, merge, and cut is in [SOURCES.md](./SOURCES.md).

## Maintaining this

When you add a skill, your own or pulled from somewhere else, add a row to
SOURCES.md and, if it's not yours originally, an entry in
THIRD_PARTY_LICENSES.md. `writing-great-skills` (from mattpocock/skills) is
in here specifically as your reference for what makes a skill's `description`
field actually fire correctly versus going stale and never triggering.

## License

The compiled selection, structure, README, and SOURCES/attribution files in
this repo are MIT, Copyright (c) 2026 Peter Benoit. Individual skill content
remains under the license of its original author. See THIRD_PARTY_LICENSES.md.
