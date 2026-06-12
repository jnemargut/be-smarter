# Be Smarter

Models anchor on their first idea and spend the rest of the answer defending it. /be-smarter makes any model go broad before it commits: question the framing, generate real options, weigh the tradeoffs, then converge. It calibrates how much thinking each task deserves, runs headless by default, and has a dial that puts you in the loop when you want to be one of the thinkers.

One skill, any task, and any of the 32+ tools that read the open [SKILL.md standard](https://agents.md/). No model swap, no API, no setup beyond a copy.

Sibling project: [decision-kit](https://github.com/jnemargut/decision-kit). Decision-kit puts you in the loop. Be-smarter thinks that way even when you're not.

## Why this works

Decision-kit taught us something by accident: even when humans rubber-stamped every recommendation, forcing divergent-then-convergent thinking made the output better. The structure was doing work the whole time. The research agrees:

- Divergent-then-convergent decomposition measurably improves problem-solving ([MacGyver benchmark](https://arxiv.org/html/2512.23601v1))
- But more thinking is not better thinking: accuracy follows an inverted U with reasoning length, and easy tasks degrade under deliberation ([Mind Your Step](https://arxiv.org/html/2410.21333v1), [OptimalThinkingBench](https://arxiv.org/html/2508.13141v1))
- So depth has to be allocated, cheaply, per task ([Reasoning on a Budget](https://arxiv.org/html/2507.02076v1))

Be-smarter is those three findings turned into a protocol.

## What a run looks like

```
/be-smarter design the credit-pack purchase flow
```

1. **Triage.** Stakes, ambiguity, reversibility, novelty, each 1-3. The sum picks a gear, G0 (just answer) through G4 (full treatment plus adversarial passes). The score is shown, always: `triage: 2·2·2·2 = 8 → G2`.
2. **Frame** (G3+). What is actually being asked? At least two alternative framings, the questions an expert would ask, an assumptions ledger. Converges to one problem statement and success criteria the answer could fail.
3. **Solve** (G2+). The 2-5 decisions hiding in the task, 3-4 real options each, tradeoffs, picks with reasoning, checked against the criteria. Then it stops. Settled picks stay settled.
4. **Answer.** The task gets done with the picks, and ends with a 3-5 line thinking summary.

Every run above G0 appends to `.thinking/trace.json`: what was considered, what won, why. The trace is the audit trail, the checkpoint, and the memory.

## The human dial

```
/be-smarter design the purchase flow --human=review
```

| Position | What you get |
|----------|--------------|
| `off` (default) | Fully headless. Trace + summary. |
| `review` | Everything runs, then one pause: a batch review of every call made. Confirm with "looks good" or override by exception: "for decision-002 I want B". |
| `full` | Pauses at both convergence points (framing, picks) for your judgment before continuing. |

Human-readable decision pages are rendered from the trace only when you step in (or later, if you ask to "show the thinking"). Nobody pays for pages nobody reads.

Depth flags: `--quick` caps the gear, `--deep` floors it, `--gear=N` sets it.

## Install

The repo root is the skill. Copy it into your tool's skills directory:

```bash
git clone https://github.com/jnemargut/be-smarter
# Claude Code
cp -r be-smarter ~/.claude/skills/be-smarter
# Codex CLI
cp -r be-smarter ~/.codex/skills/be-smarter
# Gemini CLI, Cursor, others: same move, their skills path
```

No agent at all? Paste [PROTOCOL.md](PROTOCOL.md) into any chat. Same four steps, no files.

## What's in the box

| File | What it is |
|------|------------|
| [SKILL.md](SKILL.md) | The skill. Host-neutral, spec-conformant. |
| [PROTOCOL.md](PROTOCOL.md) | The paste-anywhere version. |
| [references/triage-rubric.md](references/triage-rubric.md) | How scoring works, with worked examples. |
| [references/trace-schema.md](references/trace-schema.md) | The trace format (decision-kit compatible). |
| [references/hitl-dial.md](references/hitl-dial.md) | The dial, pause grammar, page rendering. |
| [SPEC.md](SPEC.md) | The three rules an implementation must keep. |
| [examples/](examples/) | Including the decision record of this repo designing itself. |

## Contributing

Read [CONTRIBUTING.md](CONTRIBUTING.md). The short version: open an issue first for anything non-trivial, keep PRs focused, test on real tasks across at least two hosts, and keep the skill body host-neutral. This project was designed with decision-kit; the full decision record is in [examples/designing-be-smarter](examples/designing-be-smarter).

## License

[Apache 2.0](LICENSE)
