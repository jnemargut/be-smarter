# The Be-Smarter Protocol

This is the paste-anywhere version. Drop it into any chat with any model, then give it your task. No agent host, no files, no flags required. (If your tool reads SKILL.md, install the full skill instead; you get tracing and the human dial.)

---

Before answering my next task, run this thinking protocol:

**1. Triage.** Score the task 1-3 on each: stakes (cost of being wrong), ambiguity (how many readings it has), reversibility (how hard to undo), novelty (how new the territory is). Sum them and show the line. 4: just answer it and say why you skipped the protocol. 5-6: do step 2 only, then answer. 7-8: do step 3 only. 9 or more: do steps 2 and 3.

**2. Frame.** State the literal reading of my task, plus at least two alternative framings of what I might actually need. List the 3-6 questions an expert would ask first, and answer them honestly ("I don't know" is honest; it becomes an assumption). Keep an assumptions list and mark which ones are load-bearing. Then commit: one sentence naming the real problem, and 2-4 success criteria the answer could actually fail.

**3. Solve.** Find the 2-5 decisions hiding inside the task that actually shape the outcome. For each, give 3-4 genuinely different options (if you cannot argue for an option sincerely, cut it), with what each gives up. Pick one per decision with a sentence of reasoning, and check the picks against the success criteria. Then stop: do not reopen a settled pick, and do not restate options as if it were thinking.

**4. Answer.** Do the task using your picks. End with a 3-5 line summary: the triage line, the framing you chose, the picks with one-word alternatives in parentheses, and any load-bearing assumption you could not verify (tell me to check it).

Rules: depth must match the triage score (running the whole protocol on a trivial ask is a failure, not diligence). Options must be real alternatives, never strawmen. If a load-bearing assumption turns out false midway, or two options are too close to call, step up one level of depth, once, and say so.

---

That is the whole protocol. The full skill (github.com/jnemargut/be-smarter) adds calibrated gears, a machine-readable trace, and a human-in-the-loop dial on top of these same four steps.
