# Contributing to Be Smarter

Thanks for considering a contribution. Be-smarter is one skill done carefully, not a collection. There is plenty of room to make it sharper without making it bigger.

This guide is short on purpose. The TL;DR: read [SPEC.md](SPEC.md), keep the skill host-neutral, and open a PR.

## What you can contribute

### Calibration improvements
The triage rubric is the load-bearing wall. If the gear map picks the wrong depth on real tasks, that is the most valuable bug you can file. Bring the tasks, the scores the rubric produced, and what the right gear would have been. Rubric changes are SPEC-level: open an issue first.

### Protocol refinements
Better expert-question patterns, better tripwire definitions, better convergence discipline. Anything that makes the thinking measurably better without making it longer. Show before/after runs.

### Host reports
Run the skill in a host we have not verified and report what degrades. Fixes that improve a degrade path (trace without filesystem, pauses without pages) are very welcome.

### Examples
A worked trace from a real task, with a note on what the protocol caught that a straight answer would have missed. Look at `examples/` for the format.

### Documentation improvements
Typos, clarity, missing edge cases. PRs welcome.

## What we're not looking for

- **More phases.** The protocol earns its keep by stopping. Additions that make every run longer carry a heavy burden of proof.
- **Host-specific features in the core path.** If it only works in one tool, it belongs in a degrade-path note, not in the protocol.
- **Porting the decision gate here, or headless defaults into decision-kit.** Each repo keeps its premise. See SPEC.md's relationship section.
- **A second skill in this repo.** The repo root is the skill. That is a feature.

## How to contribute

1. Open an issue first for anything non-trivial.
2. Fork and branch. One PR, one focused change.
3. Test on real tasks, not toy prompts, in at least two different hosts if your change touches the skill body.
4. If your change came out of real thinking, include the record: a trace, or a `.decisions/` folder if you used decision-kit to decide. This repo was designed that way (see `examples/designing-be-smarter`) and we like the receipts.
5. Explain what the change does, who it serves, and why it fits the spec.

## Style

Plain English. Short sentences. Opinionated. Tables where they help, prose where they don't. No em-dashes anywhere in this repo; use commas, colons, parentheses, or a period. Match the voice of the files around you.

## Conformance check before any skill-body PR

- [ ] No host-specific tool names in SKILL.md
- [ ] Every capability the change uses has a stated degrade path
- [ ] The litmus tests in SPEC.md still pass
- [ ] Trace fields still match `references/trace-schema.md` (and stay decision-kit compatible)
- [ ] No em-dashes

## Code of conduct

Be kind. See [CODE_OF_CONDUCT.md](CODE_OF_CONDUCT.md).
