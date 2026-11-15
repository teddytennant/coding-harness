# coding-harness

A [Hermes](https://github.com/NousResearch/hermes-agent) skill for coding tasks that run
across many steps or files. It has the agent work in small increments, prove each one with a
build, test, lint or run, and keep its progress in a JSON file so a context compaction doesn't
lose the plan.

## Install

```bash
git clone https://github.com/teddytennant/coding-harness ~/.hermes/skills/software-development/coding-harness
```

`SKILL.md` is plain markdown with YAML frontmatter, so any agent that reads `SKILL.md` files
can load it the same way.

## Use

Hermes picks the skill up for multi-step work; the description in `SKILL.md` tells it when.
The skill says not to use it for anything that takes fewer than about five tool calls.

The agent tracks state with `scripts/harness_state.py` (Python 3 stdlib only), which writes
`./.hermes/coding-harness/state.json`:

```bash
python3 scripts/harness_state.py init "Migrate auth from sessions to JWT, all tests green"
python3 scripts/harness_state.py add-increment "Add JWT helpers" --predict "new unit tests pass" --risk "breaks session test"
python3 scripts/harness_state.py record-verification ch_001 pass --note "pytest tests/auth -q: 14 passed"
python3 scripts/harness_state.py status
python3 scripts/harness_state.py --self-test
```

## The loop

Each increment goes through SCOPE, PLAN, IMPLEMENT, VERIFY, ATTRIBUTE and ITERATE. Before
making a change the agent writes down what it expects to happen and what might break. VERIFY
only counts a signal the agent didn't write itself: a build exit code, a test run, a linter,
or running the program. Then the result gets a verdict of keep, revert or partial, and a
failed check means root-causing it and getting the tree back to green before moving on. The
task ends on a full-suite pass.

The details are in `references/`: `execution-loop.md` for the phases and when to stop,
`verification-protocol.md` for what counts as proof, and `change-manifest.md` for the state
schema with a worked example.

The loop is adapted from
[agentic-harness-engineering](https://github.com/china-qijizhifeng/agentic-harness-engineering),
which evolves a coding agent's harness automatically; this is a hand-written playbook version
of its iteration loop and change manifest.

## License

MIT, see [LICENSE](LICENSE).
