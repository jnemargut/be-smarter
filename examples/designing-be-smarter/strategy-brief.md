# Strategy Brief: /be-smarter — a universal thinking protocol

## Elevator Pitch

> "Models anchor on their first idea and spend the rest of the answer defending it. /be-smarter makes any model go broad before it commits: question the framing, generate real options, weigh the tradeoffs, then converge. It calibrates how much thinking each task deserves, runs headless by default, and has a dial that puts you in the loop when you want to be one of the thinkers."

(Cross-link line for both READMEs: "Decision-kit puts you in the loop. /be-smarter thinks that way even when you're not.")

## The Situation

Decision-kit makes models smarter even when humans rubber-stamp every recommendation, because forcing divergent-then-convergent thinking improves output regardless. But it taxes adoption twice: users must pick the right domain skill, and must sit through human-in-the-loop pages. /be-smarter extracts the thinking engine into one standalone, headless-by-default skill that works in any of the 32 tools reading the SKILL.md open standard, with human-readable pages rendered only when a human steps into the loop.

## Key Decisions

| # | Decision | Choice | Category |
|---|----------|--------|----------|
| 1 | Core thinking protocol | Option B: Double diamond — frame, then solve (decision-option-tradeoff engine inside diamond 2; adversarial debate as escalation) | Method |
| 2 | Depth calibration | Option C: Scored triage (stakes × ambiguity × reversibility × novelty → gears G0–G4) + mid-task escalation tripwires; user flags override | Throttle |
| 3 | Human-in-the-loop dial | Option B: `--human=off\|review\|full` — headless default; review = batch-review at end (/autodecide pattern); full = pause at both converge points. `--human=auto` reserved for v2 adaptive interrupts | Interaction |
| 4 | Thinking artifacts | Option C: Machine trace always (`.thinking/trace.json`, decisions.json-compatible schema) + 3-line response summary; HTML pages rendered lazily from the trace only when a human asks | Record |
| 5 | Portability | Option B: Standard-conformant, host-neutral SKILL.md (32 tools) + PROTOCOL.md paste-anywhere fallback; feature-detect with graceful degradation | Reach |
| 6 | Repo & positioning | Option B: Sibling repo `jnemargut/be-smarter`, root = installable skill, decision-kit governance ported verbatim (Apache 2.0, CONTRIBUTING, SPEC pattern, examples/) | Home |
| 7 | Elevator pitch | Option A: Method-first (the anchoring story) | Positioning |

**Implementation constraints (user-specified):** write the repo in a tone similar to decision-kit (plain English, short, opinionated), and do not use em-dashes anywhere in the repo prose.

## Key Research Findings

- Divergent→convergent decomposition measurably improves LLM problem-solving (self-reflect −9.7% infeasible solutions, div-con +6.5% efficient solutions on GPT-4) — [arXiv 2512.23601](https://arxiv.org/html/2512.23601v1); multi-agent debate breaks first-idea anchoring — [arXiv 2305.19118](https://arxiv.org/html/2305.19118v4).
- More thinking is not monotonically better: accuracy follows an inverted-U with reasoning length, and easy inputs degrade under deliberation — [arXiv 2502.07266](https://arxiv.org/html/2502.07266v1), [arXiv 2410.21333](https://arxiv.org/html/2410.21333v1), [OptimalThinkingBench](https://arxiv.org/html/2508.13141v1). Cheap triage then allocate is the validated pattern — [Reasoning on a Budget survey](https://arxiv.org/html/2507.02076v1), [BEST-Route](https://arxiv.org/html/2506.22716v1).
- Brute-force divergence (self-consistency voting) shows diminishing returns on frontier models (0.4–1.6% gains at linear cost) — structured divergence beats more samples — [arXiv 2511.00751](https://arxiv.org/html/2511.00751).
- Production HITL practice needs three modes (approval gates, escalation, audit trails), and reviewer fatigue is the documented failure of always-on approval — [understandingdata.com](https://understandingdata.com/posts/human-in-the-loop-patterns/), [strata.io](https://www.strata.io/blog/agentic-identity/practicing-the-human-in-the-loop/). Interrupts must checkpoint and resume, never re-run — [Redis HITL patterns](https://redis.io/blog/ai-human-in-the-loop/).
- Agent audit trails must capture the "why" (options weighed, rationale), not just actions — [IETF draft-sharif-agent-audit-trail](https://datatracker.ietf.org/doc/draft-sharif-agent-audit-trail/), [Streamkap decision traces](https://streamkap.com/resources-and-guides/decision-traces-ai-agents).
- SKILL.md became an open standard (Dec 2025): adopted by OpenAI and Microsoft within 48 hours, 32 tools by March 2026, Linux Foundation governance — one host-neutral skill is inherently cross-tool — [interoperability guide](https://www.paperclipped.de/en/blog/agent-skills-open-standard-interoperability/), [agents.md](https://agents.md/), [Codex skills docs](https://developers.openai.com/codex/skills).
- Superpowers' brainstorming skill proved practitioners value the framing phase most, and that host-agnostic skills work but must degrade gracefully where host features are missing — [Superpowers](https://claude.com/plugins/superpowers), [marcnuri.com](https://blog.marcnuri.com/superpowers-claude-code-skills-framework).

## The Design in One Paragraph

`/be-smarter [task]` runs a fixed-cost triage (stakes × ambiguity × reversibility × novelty) that picks a gear: G0 just answer, G1 question scaffold, G2 solution diamond, G3 full double diamond, G4 plus adversarial escalation. The double diamond first diverges on framings and converges on the real problem with success criteria, then diverges on options with tradeoffs and converges on picks checked against those criteria. Tripwires escalate gears mid-task. Every non-G0 run appends to `.thinking/trace.json` (decisions.json-compatible) and ends with a 3-line summary; `--human=review` adds one batch-review pause at the end, `--human=full` pauses at both converge points, and pages are rendered from the trace only then. The SKILL.md is host-neutral per the open Agent Skills spec; PROTOCOL.md is the paste-anywhere fallback.

## Risks and Assumptions

- **The triage rubric is load-bearing.** Bad weights = systematic over/underthinking. Ship it in the SPEC where contributors can tune it; validate on a spread of trivial-to-hard tasks before v1.
- **Host-neutral wording is a discipline.** One Claude-specific tool name breaks the 32-tool story. Add a conformance check to CONTRIBUTING.
- **Headless-by-default inverts decision-kit's philosophy.** The family split line handles positioning, but the SPEC must own it explicitly (this is not a decision-gate skill; traces make judgment auditable instead of mandatory).
- **Trace schema stability matters from day one** — decision-kit action skills consuming it is the integration story; breaking it breaks the family.
- **Validate first:** dogfood the protocol on 3-5 real tasks (one trivial, one hard) before writing the repo around it.

## Next Steps

Run `/game-plan` for the build roadmap, or implement directly: scaffold `jnemargut/be-smarter` (governance ported from decision-kit, decision-kit tone, no em-dashes), write SKILL.md + PROTOCOL.md + references/ (triage rubric, trace schema, dial spec), include this `.decisions/` folder in examples/, and cross-link both READMEs.
