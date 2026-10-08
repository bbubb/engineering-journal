# Soccer-Subber — Making Lineup Decisions Explainable

**Project Retrospective** · Soccer-Subber · Early Python prototype · May 2026

<details>
<summary>Metadata & related work</summary>

| Field | Value |
|---|---|
| Status | Complete — prototype retrospective |
| Tags | python, lineup-planning, algorithms, explainability, soccer |
| Related Work | Early Soccer-Subber prototype; [SquadSync](https://github.com/bbubb/squadsync) integration direction |

</details>

## Summary

Soccer-Subber began with a familiar coaching problem: planning lineups and substitutions while balancing player development, position preferences, playing time, and team continuity. I wanted to see how much of that judgment I could express as repeatable, inspectable software logic.

The Python prototype generated lineups across match intervals using a scoring approach. It was a useful working experiment, not yet a finished coaching tool or integrated service.

## At a Glance

- Modeled players, formations, game plans, and timed lineup intervals.
- Built a scoring-based assignment routine that tracked minutes and positional history.
- Learned why an explainable recommendation still needs tests and constraint checks before a coach can rely on it.

## Key Themes / Reflections

### Coaching priorities became competing software rules

The difficult part was not filling positions; it was balancing fairness, preferred roles, continuity, and opportunities for players to develop in different areas of the field. The prototype modeled those inputs and generated a new assignment for each interval.

Turning coaching judgment into explicit data and scoring rules made the trade-offs easier to inspect. It also exposed the limits of informal assumptions about what a "fair" lineup would look like.

### Explainable scoring was useful, but not a guarantee

The algorithm scored player-position combinations, selected assignments greedily, tracked playing time and position history, and logged the reasoning behind scores. That made a recommendation easier to understand and troubleshoot than an opaque result.

The trade-off is that a locally attractive assignment is not necessarily best for the entire match. Playing-time targets influenced scores but were not all enforced as hard constraints. A more dependable tool would need scenario-based tests, clearer distinction between requirements and preferences, and validation of the final schedule.

### A future service needs a clearer boundary

The prototype helped demonstrate that lineup recommendation logic could be treated separately from team and roster management. Moving toward SquadSync integration would require a defined input/output contract, stronger validation, and a testable service interface.

That remains an architectural direction rather than a capability of the historical script. The immediate value of the prototype was learning how to translate a real coaching problem into explainable, testable algorithmic decisions.

## Why It Matters

Soccer-Subber connected my on-field experience with practical Python modeling and algorithm design. Its most durable lesson was that explainability helps a coach understand a recommendation, while reliability requires independently verifying that the recommendation respects the constraints that matter.
