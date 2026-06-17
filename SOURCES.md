# Sources

Every skill in `skills/` traces back to one of three upstream repos (or is new).
"Modified" means the content was edited after copying — see the note for what changed.

| Skill | Source repo | Original path | Modified? |
|---|---|---|---|
| api-and-interface-design | addyosmani/agent-skills | skills/api-and-interface-design | No |
| browser-testing-with-devtools | addyosmani/agent-skills | skills/browser-testing-with-devtools | No |
| ci-cd-and-automation | addyosmani/agent-skills | skills/ci-cd-and-automation | No |
| code-review-and-quality | addyosmani/agent-skills | skills/code-review-and-quality | No |
| code-simplification | addyosmani/agent-skills | skills/code-simplification | No |
| codebase-design | mattpocock/skills | skills/engineering/codebase-design | No |
| context-engineering | addyosmani/agent-skills | skills/context-engineering | No |
| debugging-and-error-recovery | addyosmani/agent-skills | skills/debugging-and-error-recovery | **Yes** — grafted the "build a tight feedback loop" technique list from mattpocock/skills' `diagnosing-bugs` into Step 1, so we didn't ship two competing debugging skills |
| deploy-to-vercel | vercel-labs/agent-skills | skills/deploy-to-vercel | No |
| deprecation-and-migration | addyosmani/agent-skills | skills/deprecation-and-migration | No |
| documentation-and-adrs | addyosmani/agent-skills | skills/documentation-and-adrs | No |
| domain-modeling | mattpocock/skills | skills/engineering/domain-modeling | No |
| doubt-driven-development | addyosmani/agent-skills | skills/doubt-driven-development | No |
| frontend-ui-engineering | addyosmani/agent-skills | skills/frontend-ui-engineering | No |
| git-guardrails-claude-code | mattpocock/skills | skills/misc/git-guardrails-claude-code | No |
| git-workflow-and-versioning | addyosmani/agent-skills | skills/git-workflow-and-versioning | No |
| handoff | mattpocock/skills | skills/productivity/handoff | No |
| idea-refine | addyosmani/agent-skills | skills/idea-refine | No |
| incremental-implementation | addyosmani/agent-skills | skills/incremental-implementation | No |
| interview-me | addyosmani/agent-skills | skills/interview-me | No |
| observability-and-instrumentation | addyosmani/agent-skills | skills/observability-and-instrumentation | No |
| performance-optimization | addyosmani/agent-skills | skills/performance-optimization | No |
| planning-and-task-breakdown | addyosmani/agent-skills | skills/planning-and-task-breakdown | No |
| resolving-merge-conflicts | mattpocock/skills | skills/engineering/resolving-merge-conflicts | No |
| security-and-hardening | addyosmani/agent-skills | skills/security-and-hardening | No |
| setup-pre-commit | mattpocock/skills | skills/misc/setup-pre-commit | No |
| shipping-and-launch | addyosmani/agent-skills | skills/shipping-and-launch | No |
| source-driven-development | addyosmani/agent-skills | skills/source-driven-development | No |
| spec-driven-development | addyosmani/agent-skills | skills/spec-driven-development | No |
| test-driven-development | addyosmani/agent-skills | skills/test-driven-development | No |
| triage | mattpocock/skills | skills/engineering/triage | No |
| using-agent-skills | addyosmani/agent-skills | skills/using-agent-skills | No |
| vercel-cli-with-tokens | vercel-labs/agent-skills | skills/vercel-cli-with-tokens | No |
| vercel-composition-patterns | vercel-labs/agent-skills | skills/composition-patterns | No |
| vercel-optimize | vercel-labs/agent-skills | skills/vercel-optimize | No |
| vercel-react-best-practices | vercel-labs/agent-skills | skills/react-best-practices | No |
| web-design-guidelines | vercel-labs/agent-skills | skills/web-design-guidelines | No |
| writing-great-skills | mattpocock/skills | skills/productivity/writing-great-skills | No |

## Deliberately left out

A few things were considered and cut, in case future-you wonders why they're missing:

- **mattpocock's `tdd` and `diagnosing-bugs`** — superseded by addyosmani's `test-driven-development`
  and `debugging-and-error-recovery`, which are more thorough and part of an internally
  cross-referenced system. `diagnosing-bugs`' best idea was merged in rather than lost (see above).
- **mattpocock's `grilling` / `grill-me`** — superseded by addyosmani's `interview-me` and
  `idea-refine`, which do the same job with explicit stop-conditions and phase-awareness.
- **Everything in mattpocock's `deprecated/`** — by his own labeling.
- **mattpocock's `ask-matt`, `setup-matt-pocock-skills`** — bootstrap/router scripts specific to
  his own repo, not portable.
- **mattpocock's `migrate-to-shoehorn`, `scaffold-exercises`** — specific to his course business.
- **mattpocock's writing-workflow skills** (`edit-article`, `obsidian-vault`, `writing-beats`,
  `writing-fragments`, `writing-shape`) — personal article-writing workflow, not coding-agent
  work. Revisit if you start drafting blog posts through Claude Code.
- **vercel's `react-native-skills`** — no signal you do mobile work. Easy to add later.
- **vercel's `writing-guidelines`** — Vercel's own house prose voice, not yours.

## Still pending

- **peterbenoit/agent-config** — 404'd when Claude tried to clone it. Send over what's
  worth keeping and it slots in here.
