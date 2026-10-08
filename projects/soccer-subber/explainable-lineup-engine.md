# Soccer-Subber — Formalizing Soccer Constraints as Explainable Logic

**Project Retrospective** · Soccer-Subber · Historical Python prototype · May 2026

<details>
<summary>Metadata & related work</summary>

| Field | Value |
|---|---|
| Status | Complete — prototype retrospective |
| Tags | python, algorithms, lineup-planning, explainability, domain-modeling |
| Source basis | Private historical Soccer-Subber Python prototype |
| Public follow-up | Planned service-ready integration boundary in https://github.com/bbubb/squadsync |

</details>

## Summary

Soccer-Subber began as a Python prototype for helping coaches generate soccer lineups and substitution plans. Its challenge was to translate coaching priorities—formation shape, positional fit, playing-time goals, and rotation—into explicit rules an algorithm could evaluate.

The historical code used a deterministic greedy scoring-and-assignment approach over time intervals, tracked player minutes and position history, and logged scores behind recommendations. It was a working experiment, not yet a validated, API-ready service.

## At a Glance

- Translated competing coaching goals into data models and inspectable assignment criteria.
- Implemented interval-based greedy assignment with player/position scoring and playtime tracking.
- Learned that an understandable recommendation is not the same as a guaranteed fair or globally optimal plan.

## Key Themes / Reflections

### Formalizing coaching judgment exposed competing constraints

A coach must consider more than filling every position. Players may have preferred positions, playing-time limits or targets, developmental needs, and continuity from one interval to the next.

The prototype represented formations, players, game plans, and intervals, then evaluated candidate assignments against several competing factors. That was a useful exercise in moving from informal domain judgment to software behavior. It also showed how quickly a scoring rule can produce trade-offs that are difficult to reason about informally.

### Greedy assignment made decisions inspectable, not automatically correct

The prototype ranked player-position candidates and assigned available pairs while tracking playing time. Logs exposed score contributions and resulting assignments, making it possible to inspect why a particular choice was made.

The important limitation is that greedy local choices do not guarantee global optimality or satisfaction of every soft playing-time target. This distinction motivates scenario-based tests and explicit validation of hard constraints. Explainability improves trust only if the explanation accurately describes what the engine did.

### A service boundary requires more than a functioning script

The original prototype combined scoring, assignment, logging, and presentation concerns more closely than an API-oriented service should. A future bounded service would need validated input contracts, clear separation of algorithm components, deterministic result shapes, meaningful failure handling, and repeatable tests.

That direction is related to SquadSync's planned lineup-suggestion interface, but integration and cloud deployment should not be mistaken for completed historical work.

## Why It Matters

Soccer-Subber demonstrates my effort to turn deep domain familiarity into testable software decisions, including their limitations. The journal preserves the algorithmic reasoning at a conceptual level; the historical prototype remains private, without publishing detailed scoring weights or proprietary extensions.
