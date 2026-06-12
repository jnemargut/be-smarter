# The Human Dial

Full specification for `--human=off|review|full`: pause points, interaction grammar, page rendering, and state rules.

The premise inverts decision-kit. There, the human gate is the point and autonomy is the opt-in. Here, autonomous thinking is the point and the human steps in by choice. Both are legitimate; they are different tools. (Decision-kit puts you in the loop. Be-smarter thinks that way even when you're not.)

## Positions

### off (default)

No pauses, ever. The run produces the trace and the closing summary. If something truly cannot proceed without human input, the answer says so plainly; the skill never invents a pause the dial did not ask for.

### review

The whole protocol runs headless, then pauses once before final execution is considered settled. The pause presents every call made in this run:

- The triage line and any escalations
- The framing chosen, with the framings it beat (one line each) and the success criteria
- Each decision: chosen option, the beaten options one-lined, the reasoning
- The assumptions ledger, load-bearing marks visible
- Any adversarial verdicts

Then it waits. This is the only pause. Accepted responses:

| Response | Effect |
|----------|--------|
| `looks good` (or any clear confirmation) | All `auto-picked` statuses flip to `chosen`, `human.reviewed: true`, done. |
| `for decision-002 I want B` | Swap that pick, re-check the success criteria, re-present anything the swap invalidates, ask again for the rest. |
| `redo the framing` | Rerun Diamond 1 interactively (full-style pause), then return to review for the rest. |
| A custom answer | First-class option: record it alongside the generated options with the same care, mark it `chosen`, note any reasoning given. |

Repeat until confirmed. Do not flip statuses or call the run settled before confirmation.

### full

Two pauses, at the two convergence points:

1. **After Diamond 1 converges:** present the framings considered, the chosen framing, and the success criteria. The human approves or redirects ("the second framing is right", "add a criterion about cost"). Do not enter Diamond 2 until they respond.
2. **After Diamond 2 converges:** present decisions, options, picks, reasoning, criteria check. Same response grammar as review. Do not execute until they respond.

Gears below G3 have no Diamond 1; in those, full has a single pause at the Diamond 2 (or G1 ledger) convergence.

## Rendering

Pages are views over the trace, generated at pause time, never during headless runs.

**When the host can write files and open them:** render each pause as a self-contained HTML page and open it. Follow decision-kit's visual conventions so the family feels like a family: dark theme (background `#0a0a0f`, card `#13131c`, accent `#6c63ff`), one card per decision with option letters, a recommendation badge on the model's pick, yellow `auto-picked` and green `chosen` status chips, and a footer stating the response grammar. The review pause renders one batch page (decision-kit's `auto-review.html` pattern); full's pauses render one page each. Write pages next to the trace in `.thinking/` and link them from a small `index.html` if more than one exists.

**When it cannot:** present exactly the same material as structured text in the response. Same order, same one-liners, same footer grammar. The dial works in a bare chat window; pages are an upgrade, not a dependency.

**On demand:** if a human ever asks to "show the thinking" after a headless run, render pages from the trace then, for whichever runs they name (default: the latest).

## State rules

1. **A pause is a checkpoint, not a restart.** Everything diverged and converged before the pause survives the pause verbatim. Resume from recorded state; never re-derive it.
2. **A human response modifies only what it names.** "For decision-002 I want B" does not license rethinking decision-001.
3. **Ripple effects are surfaced, not silently absorbed.** If an override invalidates another pick or fails a criterion, say which, propose the smallest consistent adjustment, and let the human confirm it.
4. **Every override is recorded** in `human.overrides` with old pick, new pick, and any reasoning offered. Reasoning is captured when volunteered, never demanded (decision-kit's rule, kept).

## Future position (reserved, not implemented)

`--human=auto`: adaptive interrupts, where the model pulls the human in only when the triage score or a tripwire crosses a threshold. Deliberately deferred until the triage rubric has earned trust in the field. The dial's argument grammar reserves the name; proposals to implement it go through an issue first and must define the threshold, the interrupt budget, and the failure story for a misfiring threshold.
