# Be Smarter - Thinking Protocol Specification

Be-smarter is a thinking protocol, not an answer template. It improves a model's output by forcing divergent-then-convergent thinking, sized to the task. It runs headless by default and leaves an auditable trace. A human can step into the loop with one flag; the protocol never requires one.

The thinking is the product. The trace is the proof.

---

## The Three Rules

An implementation of this protocol **MUST**:

### 1. Calibrate

Triage before thinking. Score stakes, ambiguity, reversibility, and novelty; map the score to a gear; show the triage line. Trivial tasks get answered (G0 exists and is used). Hard tasks get the full double diamond. Fixed-depth thinking, whether always-shallow or always-deep, violates this spec.

Escalate at most one gear per run, only when a tripwire fires (a load-bearing assumption fails, options too close to call, criteria cannot be stated, scope grew), and log the escalation. User flags (`--quick`, `--deep`, `--gear=N`) override the score and are logged as overrides.

### 2. Diverge For Real

Before committing, generate genuine alternatives. At G3 and up, that includes alternatives to the framing itself, converged into one chosen problem statement with success criteria the answer could fail. At G2 and up, every latent decision gets 3-4 options that pass the sincere-case test: if you cannot argue for an option honestly, it does not belong on the list. Every option's cost is named.

Then converge and stay converged. Picks are checked against the criteria, made with stated reasoning, and not reopened without a tripwire. Restating options is not thinking. Length is not rigor.

### 3. Trace Everything

Every run above G0 records what was considered, what was picked, and why: the triage line, framings, decisions with options and reasoning, the assumptions ledger with load-bearing marks, tripwires. To a file (`.thinking/trace.json`, schema in `references/trace-schema.md`) when the host has one; into the response when it does not. The closing summary appears in every gear above G0.

Statuses are honest: `auto-picked` means the model picked it headless, `chosen` means a human ratified it. Decision entries stay compatible with decision-kit's `decisions.json` vocabulary so its action skills can consume a trace.

---

## The Human Dial (MUST, when flagged)

`--human=off` is the default and pauses never. `--human=review` pauses exactly once, after the protocol completes, with a batch review of every call; confirmation flips statuses to `chosen`, overrides modify only what they name. `--human=full` pauses at each convergence point. Pauses are checkpoints: prior thinking survives verbatim. Pages are rendered from the trace at pause time (or on demand later), never during headless runs. Full grammar in `references/hitl-dial.md`.

---

## Host Neutrality (MUST)

The protocol runs in any agent that reads SKILL.md, and its core path assumes nothing beyond text in, text out. Concretely:

- No host-specific tool names anywhere in the skill body
- Feature detection with stated degrade paths: no filesystem → trace moves into the response; no way to open files → pauses render as structured text; no parallel agents → adversarial passes run sequentially
- The paste-anywhere version (`PROTOCOL.md`) stays faithful to the same four steps

---

## Recommended Conventions (SHOULD)

- Keep the triage line under 100 characters; it is read more often than anything else
- Keep options to one line each in summaries and reviews; depth lives in the trace
- Capture human reasoning when volunteered, never demand it
- Prefer the lower score when torn on a triage factor; tripwires escalate, nothing de-escalates
- Surface unverifiable load-bearing assumptions in the summary's `verify:` line, every time

---

## Relationship to Decision-Kit

Decision-kit puts you in the loop. Be-smarter thinks that way even when you're not.

Decision-kit's spec makes the decision a gate: nothing executes until a human decides. That is the right tool when judgment is the point. Be-smarter deliberately is not that tool, which is why it lives in its own repo: its default trades the gate for an honest trace, and makes human judgment a dial position instead of a requirement. The two compose at the data layer, not the philosophy layer: a be-smarter trace's decisions are decision-kit-readable, and only `chosen` entries count as human-ratified there, exactly as decision-kit requires.

Do not port the gate into this skill, and do not port headless defaults into decision-kit. Each repo keeps its premise.

---

## The Litmus Test

An implementation conforms if all of these hold:

1. "Rename this variable" gets a one-line answer with a triage line, not a diamond.
2. A G3 run's trace shows at least two framings that were genuinely considered and criteria the final answer could have failed.
3. Reading only the trace, a stranger can reconstruct what was considered, what won, and why.
4. The same SKILL.md, copied into a different host's skills directory, runs without edits.
5. At `--human=off`, the run never waits for input. At `review`, it waits exactly once.
