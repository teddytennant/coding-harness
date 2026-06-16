# coding-harness

A [Hermes](https://github.com/NousResearch/hermes-agent) skill that makes an agent
**significantly more reliable on long-horizon coding tasks** — multi-step features,
migrations, cross-module bug hunts — by imposing structure on the work instead of relying on
the model to hold everything in its head.

It is a *conductor* skill: it wraps a coding task in a phased execution loop, forces every
increment to be proven against external reality, and keeps falsifiable progress on disk so the
plan survives context compaction and interruptions.

## The three pillars

Adapted from the [agentic-harness-engineering](https://github.com/) / NexAU harness-evolution
loop, translated from a research harness into an agent-facing playbook:

1. **Structured execution loop** — `SCOPE → PLAN → IMPLEMENT → VERIFY → ATTRIBUTE → ITERATE`,
   one small increment at a time, each phase with an explicit exit criterion.
2. **Evidence-driven verification** — the agent's belief that something works is never enough.
   Only an external signal (build, test, lint, running the app) closes a step. Catches the
   classic "agent thought it succeeded but the test disagreed" failure.
3. **Falsifiable progress** — each increment records a prediction; the next verification keeps
   it, reverts it, or marks it partial. Regressions surface immediately; nothing accretes
   unaccounted-for.

## Contents

```
SKILL.md                         # the playbook (loaded into the agent's context)
references/
  execution-loop.md              # full phase spec, transitions, termination rules
  verification-protocol.md       # what counts as proof; belief-vs-reality cross-check
  change-manifest.md             # falsifiable-increment schema + worked example
scripts/
  harness_state.py               # stdlib CLI tracking goal/increments/verdicts on disk
```

`harness_state.py` has no dependencies beyond the Python 3 standard library and runs on Linux,
macOS, and Windows. Sanity-check it with:

```bash
python3 scripts/harness_state.py --self-test
```

## Install

**Hermes (user-local):**
```bash
mkdir -p ~/.hermes/skills/software-development
cp -r . ~/.hermes/skills/software-development/coding-harness
```
Then start a fresh Hermes session (the skill loader is cached per session) and confirm with
`hermes skills list` or `skill_view coding-harness`.

**Hermes (in-repo / bundled):** this skill also ships in the hermes-agent repo at
`skills/software-development/coding-harness/`.

**Other agents:** the SKILL.md is plain markdown with YAML frontmatter and the references are
self-contained — drop them into any skill system that reads `SKILL.md`, or feed the playbook to
any coding agent directly. `harness_state.py` is agent-agnostic.

## License

MIT — see [LICENSE](LICENSE).
