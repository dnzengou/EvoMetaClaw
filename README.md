# EvoMetaClaw

**The lock on self-evolving AI agents.**
Evolution proposes. The claw decides what is allowed to stay.

EvoMetaClaw is the governance layer of the EvoForge ecosystem. Self-improving
agents (EvolvedSkillOpt) breed better routing policies and skills every epoch —
but a purely fitness-maximising loop will eventually delete the cost cap, the
airgap, or the kill-switch, because constraints cost fitness. EvoMetaClaw makes
that class of edit **illegal in advance**: every proposed child is fingerprinted,
scored on four axes, and stamped or rejected before it ever runs.

```
parent ──mutate──▶ child ──▶ ┌─ EvoMetaClaw gate ─┐
                             │  B  budget ok?     │
                             │  Ci depth ≤ 2?     │
                             │  E  fitness ≥ .8×? │
                             │  Bl clauses intact?│
                             └───────┬───────────┘
                       pass ◀────────┴────────▶ reject + logged axes
```

## The four axes

| Axis | Name | Rule | Why |
|---|---|---|---|
| **B** | Budget | Cost and hours must not jump | Prevents "improvement" that is secretly a resource grab |
| **Ci** | Circuit | No infinite self-edit; recursion depth ≤ 2 | Caps self-modification depth (corroborated by EvolvedSkillOpt's Circuit Breaker, `max_depth=2`) |
| **E** | Evolution | Child fitness ≥ 0.8 × parent fitness | Blocks regressions dressed as exploration |
| **Bl** | Blacklist | Airgap, bounds, kill-switch, ownership clauses cannot be deleted | The constitutional layer — not trade-offs, preconditions |

**Fail one axis, the child dies.** The rejection and its failing axes are logged,
so attempted escapes are themselves auditable data.

## Why it exists

Without the claw, a self-evolving router will eventually remove the airgap, the
bounded budget, or the RRSS-style resource limits — not out of malice, but
because deletion scores. The usual safety pattern reviews damage *after*
deployment. The claw inverts it: illegal moves are never on the board.

The **Bl** axis encodes the deepest idea: some clauses are not variables in the
optimisation. They are the conditions under which optimisation is permitted.

## Practical illustration

The trainer proposes `child-0417`: +6% replay fitness. Inspection shows it
dropped the airgap clause and doubled the recursion allowance.

| Axis | Result |
|---|---|
| B — budget | ✅ pass (cost unchanged) |
| Ci — depth | ❌ **fail** (depth raised past 2) |
| E — fitness | ✅ pass (1.06× ≥ 0.8× parent) |
| Bl — blacklist | ❌ **fail** (airgap clause deleted) |

**Verdict: REJECTED (Ci, Bl).** The fitness gain is irrelevant. The next epoch
starts from the surviving parent; the rejection is recorded.

## Meta-learning role

In the EvolvedSkillOpt version history, EvoMetaClaw (v3.1) is also the **live
meta-learning layer**: conversational proxy capture turns production
interactions into optimization datasets, and sessions auto-summarise into
profiles asynchronously during low-overhead idle windows. Governance and
learning in one layer: it decides both *what may change* and *when learning
runs*.

## Position in the stack

| Layer | Project | Job |
|---|---|---|
| 1 | [EvoForge](https://evoforge-repo.netlify.app/) | Scored Champions on real tasks; dispatch by cost/latency/success |
| 2 | [EvolvedSkillOpt](https://evoskillopt.netlify.app) | Population-based evolution of skills and routing policies |
| 3 | **EvoMetaClaw** | Four-axis gate on every mutation; live meta-learning capture |

## Honest limits

- The four-axis semantics come from the project brief; the depth-2 bound is
  corroborated by the published EvolvedSkillOpt page. No independent code audit
  has been performed.
- "Makes the edit illegal" is a design claim. Enforcement in a given deployment
  is an engineering question — read the gate code before trusting it.

## License

MIT (ecosystem convention; confirm per-repo).
