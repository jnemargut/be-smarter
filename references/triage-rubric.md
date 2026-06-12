# Triage Rubric

How to score a task before thinking about it. The four factors, what each level means, worked examples, and the tuning notes contributors need.

The point of triage: the marginal value of thinking is query-dependent. Easy inputs degrade under deliberation; hard inputs benefit disproportionately. A skill that thinks the same amount about everything is wrong twice.

## The factors

### Stakes: what does being wrong cost?

| Score | Meaning | Examples |
|-------|---------|----------|
| 1 | Cheap. Wrong answer wastes minutes. | Rename suggestion, draft wording, a one-off script |
| 2 | Real. Wrong answer costs hours, money, or a bad commit. | API design used by two teams, a pricing page, a migration |
| 3 | Painful. Wrong answer is hard to walk back or hurts people. | Data model for a live product, security posture, public commitments |

### Ambiguity: how many reasonable readings does the ask have?

| Score | Meaning | Examples |
|-------|---------|----------|
| 1 | One. The ask is mechanical. | "Add a retry to this call" |
| 2 | A few. Intent is mostly clear, scope is not. | "Clean up this module" |
| 3 | Many. The ask names a symptom, not a problem. | "Make the app feel faster", "improve onboarding" |

### Reversibility: how hard is undo?

| Score | Meaning | Examples |
|-------|---------|----------|
| 1 | Two-way door. Revert, regenerate, rewrite freely. | Local refactor, a draft, anything behind a flag |
| 2 | Costly door. Undo means real rework or coordination. | Published API shape, schema change with data in it |
| 3 | One-way door. Practically irreversible. | Sent emails, public launches, deleted data, signed contracts |

### Novelty: how new is the territory?

| Score | Meaning | Examples |
|-------|---------|----------|
| 1 | Done a hundred times. Pattern is settled. | CRUD endpoint, standard auth flow |
| 2 | Familiar shape, new details. | Known pattern in an unfamiliar codebase or domain |
| 3 | Genuinely new. No settled pattern to lean on. | Novel architecture, first-of-its-kind feature, unexplored tradeoff space |

## The gear map

| Score | Gear | Shape of the run |
|-------|------|------------------|
| 4 | G0 | Answer directly. One line says why. |
| 5-6 | G1 | Expert questions + assumptions ledger, one converge pass. |
| 7-8 | G2 | Solution diamond only. Literal framing accepted and logged. |
| 9-10 | G3 | Full double diamond. |
| 11-12 | G4 | Double diamond + adversarial pass on the closest calls. |

## Worked examples

- **"Rename `usrMgr` to something clearer."** Stakes 1, ambiguity 1, reversibility 1, novelty 1 = 4 → G0. Answer it. The triage line is the only ceremony.
- **"Clean up the utils module."** Stakes 1, ambiguity 2, reversibility 1, novelty 1 = 5 → G1. The expert questions ("what hurts about it today? is anything else importing these?") do the useful work; a double diamond would be theater.
- **"Design the credit-pack purchase flow."** Stakes 2, ambiguity 2, reversibility 2, novelty 2 = 8 → G2. The framing is clear enough; the decisions (provider, failure handling, receipt model) deserve real options.
- **"Make the app feel faster."** Stakes 2, ambiguity 3, reversibility 1, novelty 2 = 8 → G2, but expect the framing tripwire: if early evidence shows the named symptom is not the real problem, that is a load-bearing assumption failing, and the run escalates to G3 where Diamond 1 belongs.
- **"Pick our data model for multi-tenancy."** Stakes 3, ambiguity 2, reversibility 3, novelty 2 = 10 → G3. Framing matters (tenancy for whom? isolation or cost?), options matter, criteria matter.
- **"Should we open-source the core engine?"** Stakes 3, ambiguity 3, reversibility 3, novelty 3 = 12 → G4. One-way door, contested framings, genuinely novel for this team. The adversarial pass earns its cost here.

## Scoring honestly

- Score the task in front of you, not the worst case it could become. Tripwires exist for the case where it grows.
- When torn between two scores on a factor, take the lower one. Tripwires escalate; nothing de-escalates. Starting low and stepping up beats starting high and wasting the run.
- Ambiguity is about the ASK, not the work. A hard task stated precisely can be ambiguity 1.
- Reversibility is about the deliverable's blast radius, not your edit history.

## Tuning notes (for contributors)

The weights are deliberately flat (every factor 1-3, simple sum) because flat is auditable. If you propose new weights or factors, bring evidence: a set of real tasks where the current map picked the wrong gear, and the outcomes that show it. Calibration changes are SPEC-level changes; open an issue first.

Known pressure points worth testing:

- Tasks with stakes 3 but ambiguity 1 (precise high-stakes asks) currently land G2-G3. Arguably correct; verify with real cases.
- Novelty is the factor models self-assess worst. If miscalibration shows up, it will likely be here.
