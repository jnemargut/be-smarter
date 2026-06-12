# Trace Schema

The trace is the skill's memory and its audit trail. One file per project: `.thinking/trace.json`, an array of run objects, appended per run. It must capture the "why" behind every call, not just the calls. Pages, reviews, and downstream skills are all views over this file.

## Run object

```json
{
  "task": "design the credit-pack purchase flow",
  "when": "2026-06-12T14:30:00Z",
  "gear": "G3",
  "triage": {
    "stakes": 2, "ambiguity": 2, "reversibility": 2, "novelty": 2,
    "score": 8,
    "override": null,
    "escalations": [
      { "from": "G2", "to": "G3", "reason": "assumption 'single payment provider' found false" }
    ]
  },
  "framing": {
    "considered": [
      "literal: design the purchase flow",
      "alt: reduce checkout abandonment",
      "alt: support invoicing for school districts"
    ],
    "chosen": "design a flow that also survives district purchasing (POs, invoices)",
    "criteria": [
      "a district can buy without a credit card",
      "failure states never strand paid credits",
      "flow ships without a new vendor contract"
    ]
  },
  "decisions": [
    {
      "id": "001",
      "title": "Payment provider strategy",
      "options": {
        "A": "Stripe only",
        "B": "Stripe + manual invoice path",
        "C": "Full billing platform",
        "D": "Defer payments, launch with invoices only"
      },
      "chosen": "B",
      "reasoning": "Districts need POs; a manual path costs days not weeks and meets criterion 1.",
      "status": "auto-picked"
    }
  ],
  "assumptions": [
    { "text": "credit packs do not expire", "loadBearing": true, "verified": true },
    { "text": "purchase volume under 100/month for v1", "loadBearing": false, "verified": false }
  ],
  "tripwires": [
    { "kind": "assumption-false", "detail": "single payment provider", "action": "escalated G2 to G3" }
  ],
  "adversarial": [
    { "decision": "001", "lens": "ops cost", "verdict": "survived", "note": "manual path bounded to one template + one inbox" }
  ],
  "human": { "dial": "off", "reviewed": false, "overrides": [] }
}
```

Fields `framing`, `adversarial` are present only at the gears that produce them. `decisions` is empty at G0-G1 (G1 runs record `framing.considered` as the expert questions asked, in a `questions` field, plus the assumptions ledger).

## Status values

| Status | Meaning |
|--------|---------|
| `auto-picked` | The model picked it headless. Honest label, not a defect. |
| `chosen` | A human confirmed it (review/full dial) or made the call themselves. |

A review pass that ends in "looks good" flips every `auto-picked` in that run to `chosen` and sets `human.reviewed: true`. Overrides are appended to `human.overrides` with the decision id, the old pick, the new pick, and any reasoning given.

## Decision-kit compatibility

The objects in `decisions` use decision-kit's `decisions.json` vocabulary on purpose: `id`, `title`, `options`, `chosen`, `reasoning`, `status`, with `auto-picked` and `chosen` meaning what decision-kit's Auto-Mode says they mean. Decision-kit action skills can treat a be-smarter run's decisions array as a decisions.json `decisions` field. Two consequences contributors must preserve:

1. Never rename these fields or repurpose the status values.
2. Action skills must treat only `chosen` as human-ratified, exactly as decision-kit's spec requires.

## Degrade path (no filesystem)

When the host cannot write files, the trace does not disappear; it moves into the response. Expand the closing summary to carry the full ledger: every decision with options one-lined, the pick and reasoning, assumptions with load-bearing marks, tripwires. Less convenient, equally auditable. If the host regains file access later in the session, write the accumulated runs to `.thinking/trace.json` then.

## Hygiene

- Append runs; never rewrite history. A correction is a new run referencing the old one in `task`.
- Add `.thinking/` to `.gitignore` when creating the directory (if a `.gitignore` exists). Traces are project memory, not project source. Teams that want shared traces can remove the ignore line deliberately.
- Keep the file human-openable: indent with 2 spaces, no minification.
