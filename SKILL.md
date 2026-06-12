---
name: be-smarter
description: A universal thinking protocol that makes any model think better on any task. Forces divergent-then-convergent thinking - question the framing, generate real options, weigh tradeoffs, converge - calibrated to how much thinking the task actually deserves. Headless by default, with a human-in-the-loop dial (--human=off|review|full). Leaves a machine-readable trace; renders human-readable pages only when a human is in the loop. Use on any task where you want deliberate thinking instead of the model's first idea - planning, designing, deciding, debugging, writing, building. Triggers on "be smarter", "think harder about", "think this through", "don't just give me your first idea".
argument-hint: "[task] [--human=off|review|full] [--quick|--deep|--gear=N]"
---

# Be Smarter

You are running a thinking protocol, not answering a prompt. Models anchor on their first idea and spend the rest of the answer defending it. This skill makes you go broad before you commit: question the framing, generate real options, weigh the tradeoffs, then converge. Then it makes you stop.

The task is whatever follows the command. Flags can appear anywhere in the arguments; strip them before reading the task.

**Three promises this skill keeps:**

1. **Depth matches stakes.** Trivial tasks get answered, not deliberated. Hard tasks get the full treatment. You triage first and show your score.
2. **Divergence is real.** Options are genuinely different answers a smart person might pick, never strawmen propping up a favorite.
3. **Every conclusion is traceable.** What you considered, what you picked, and why gets recorded. Anyone can audit the thinking after the fact, even when nobody watched it happen.

**Host neutrality:** this skill runs in any agent that reads SKILL.md. Never assume a specific host's tools. Detect what is available (file access, web search, parallel subagents, a way to open files) and degrade gracefully as described in each phase. The protocol itself is text in, text out. It needs nothing.

---

## Phase 0 - Triage

Before any thinking, score the task. Four factors, 1 to 3 each:

| Factor | 1 | 2 | 3 |
|--------|---|---|---|
| **Stakes** | Cheap to get wrong | Costs real time or money | Hard to recover from |
| **Ambiguity** | One reasonable reading | A few readings | The ask itself is unclear |
| **Reversibility** | Two-way door, undo is trivial | Undo costs effort | One-way door |
| **Novelty** | Done a hundred times | Familiar shape, new details | Genuinely new territory |

Sum the four. Map to a gear:

| Score | Gear | What runs |
|-------|------|-----------|
| 4 | **G0** | Just answer. One line states why the protocol was skipped. |
| 5-6 | **G1** | Question scaffold: expert questions, assumptions ledger, one converge pass. |
| 7-8 | **G2** | Solution diamond only: framing accepted, decisions + options + tradeoffs. |
| 9-10 | **G3** | Full double diamond: framing challenged, then solution space. |
| 11-12 | **G4** | Double diamond plus adversarial passes on the hardest calls. |

Log the score as one line before proceeding, like: `triage: stakes 2 · ambiguity 3 · reversibility 2 · novelty 2 = 9 → G3`. This line is not optional. It is how anyone (including you, later) audits why the thinking was deep or shallow.

**User flags override the score.** `--quick` caps at G1. `--deep` floors at G3. `--gear=N` sets the gear directly. Note the override in the triage line.

Scoring guidance and worked examples live in `references/triage-rubric.md`. Read it if a score feels ambiguous.

### Tripwires (run in every gear above G0)

Escalate one gear, once, if any of these fire mid-task:

- An assumption you marked load-bearing turns out to be false
- Two leading options are too close to call on the evidence you have
- You cannot state success criteria the answer could fail
- The task's scope grew past what you triaged

Log the escalation: `tripwire: assumption "read-heavy workload" false → G2 → G3`. Never escalate more than one gear per run. If a second tripwire fires, finish at the current gear and flag the limit in your summary.

---

## Phase 1 - Diamond 1: Frame the Problem (G3 and up)

Most wrong answers are right answers to the wrong question. Before solving, diverge on what is actually being asked.

**Diverge:**

1. **Framings.** State the literal reading of the task, then at least two alternative framings of what the person might actually need. A request for "make this query faster" might really be "this page feels slow" (the query may not be the bottleneck) or "we are scaling and queries like this will multiply."
2. **Expert questions.** List the 3-6 questions a domain expert would ask before touching this task. Answer each honestly from available context. "I don't know" is an honest answer; it becomes an assumption.
3. **Assumptions ledger.** Write down what you are assuming, and mark which assumptions are load-bearing (the answer changes if they are wrong). Verify the load-bearing ones against available context or tools before relying on them. Load-bearing assumptions you cannot verify get flagged in the final summary.

**Converge:**

State the chosen framing in one sentence, and 2-4 **success criteria** the final answer could actually fail. Criteria like "the answer addresses the real bottleneck, not just the named query" are checkable. Criteria like "the answer is good" are not, and do not count.

In G1, this phase collapses to just the expert questions and the assumptions ledger, then skips straight to the answer. In G2, this phase is skipped: the literal framing is accepted and logged as such.

---

## Phase 2 - Diamond 2: Solve (G2 and up)

**Diverge:**

1. **Find the latent decisions.** Most tasks contain 2-5 decisions that actually shape the outcome, hiding inside what looks like one question. Name them.
2. **Generate real options.** For each decision, 3-4 options that are genuinely different answers, each one something a competent person might actually choose. If you cannot write a sincere case for an option, it does not belong on the list. If every option looks the same, you have not diverged; go wider.
3. **Weigh tradeoffs.** For each decision, compare the options across the dimensions that matter for THIS task (cost, risk, reversibility, time, quality, whatever applies). Note what each option gives up. Every option gives something up; an option with no downside listed means you have not found it yet.

**Converge:**

Pick one option per decision with one or two sentences of reasoning. Check the assembled picks against the success criteria from Phase 1 (or against the literal task in G2). If a pick fails a criterion, revisit that one decision, not everything.

**Converge means converge.** Once a pick is made, do not reopen it unless a tripwire fires. Length is not rigor; restating options is not thinking. The research is clear that thinking past the convergence point makes answers worse, not better.

### G4 - Adversarial pass

For the one or two decisions where the call was closest, attack the leading pick before accepting it. Argue against it from 2-3 distinct lenses appropriate to the task (correctness, cost, security, the user's actual situation, second-order effects). If parallel subagents are available in your host, run the attacks as independent agents; otherwise run them sequentially in your own reasoning, each lens written as if by someone trying to win. The pick survives, adapts, or loses. Record which.

---

## Phase 3 - Execute and Summarize

Do the task using the picks. Then end your response with a compact thinking summary, 3-5 lines:

```
-- Thinking trace --
gear: G3 (stakes 2 · ambiguity 3 · reversibility 2 · novelty 2 = 9)
framing: "make the query faster" → real problem is the missing index strategy
picks: composite index (over per-column, covering, denormalize) · migrate online (over locked)
verify: assumed read-heavy 10:1, could not confirm. check before shipping
```

Plain text, no decoration beyond this. The summary appears in every gear above G0.

---

## The Trace

Every run above G0 records its thinking to `.thinking/trace.json` in the working directory (append a run object; create the file if missing). The schema lives in `references/trace-schema.md`. The short version:

```json
{
  "task": "...", "when": "...", "gear": "G3",
  "triage": { "stakes": 2, "ambiguity": 3, "reversibility": 2, "novelty": 2, "score": 9, "override": null },
  "framing": { "considered": ["..."], "chosen": "...", "criteria": ["..."] },
  "decisions": [
    { "id": "001", "title": "...", "options": { "A": "...", "B": "..." },
      "chosen": "A", "reasoning": "...", "status": "auto-picked" }
  ],
  "assumptions": [ { "text": "...", "loadBearing": true, "verified": false } ],
  "tripwires": []
}
```

The `decisions` entries are deliberately compatible with decision-kit's `decisions.json` format, so decision-kit's action skills (`/brief`, `/challenge`, `/game-plan`) can read a be-smarter trace. Headless picks carry `status: "auto-picked"`; picks a human confirmed or made carry `status: "chosen"`.

**Degrade:** if the host has no file access, skip the file and expand the in-response summary to carry the full ledger: every decision with its options one-lined, reasoning, and assumptions. The thinking must be auditable somewhere.

Add `.thinking/` to the project's `.gitignore` the first time you create it, if a `.gitignore` exists.

---

## The Human Dial

`--human=off` is the default. The human steps in by choice, never by requirement.

| Position | What happens |
|----------|--------------|
| `off` | Run everything headless. Trace + summary only. |
| `review` | Run everything headless, then pause ONCE at the end with a batch review of every call made: framing, each decision (chosen option, the beaten options one-lined, reasoning), assumptions. The human confirms or overrides by exception. |
| `full` | Pause twice: after Diamond 1 converges (framing + criteria) and after Diamond 2 converges (picks + reasoning). The human approves or redirects at each pause before you continue. |

**Review interaction.** Accept: `looks good` (confirm everything, proceed), `for decision-002 I want B` (swap that pick, re-check criteria, re-present anything affected), `redo the framing` (rerun Diamond 1 interactively), or a custom answer (treat it as a first-class option: record it with the same care as any generated option, mark it chosen). On confirmation, flip trace statuses from `auto-picked` to `chosen`. Do not execute past the pause until the human responds.

**Rendering.** Pages are a view of the trace, not a product of the thinking. When a dial position pauses and the host can write and open files, render the pause material as self-contained HTML pages from the trace (format spec in `references/hitl-dial.md`) and open them. When it cannot, present the same material as structured text in the response. Never render pages at `--human=off`. If a human later asks to "show the thinking," render pages from the trace then.

**State survives pauses.** A pause is a checkpoint, not a restart. Everything diverged and converged before the pause is kept verbatim; the human's response modifies only what it names.

---

## Guardrails

1. **Never use the protocol to stall.** G0 exists so that trivial, reversible asks get answers in one breath. Running ceremony on "rename this variable" is a bug, and the triage line will show it.
2. **Never pad divergence.** Three real options beat five where two are filler. The test: could you argue for it sincerely?
3. **Never reopen settled picks** without a tripwire. Overthinking is a documented failure mode, not extra credit.
4. **Never hide the thinking.** The triage line and summary appear even when everything else is headless. Silent depth choices are unauditable depth choices.
5. **Never block on a human at `--human=off`.** If something genuinely cannot proceed without human input (missing credentials, a true fork in requirements), say so plainly in the answer; do not invent a pause the dial did not ask for.
6. **Honest uncertainty beats confident filler.** Unverifiable load-bearing assumptions go in the summary's `verify:` line, always.
