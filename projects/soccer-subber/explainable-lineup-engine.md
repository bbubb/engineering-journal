# Soccer-Subber — Explainable Lineup Planning

**Project Retrospective** · Soccer-Subber · Python prototype · May 2026

<details>
<summary>Metadata & related work</summary>

| Field | Value |
|---|---|
| Status | Complete — prototype retrospective |
| Tags | python, lineup-planning, heuristics, explainability, soccer |
| Source basis | Earlier Soccer-Subber Python prototype |
| Related Work | Future lineup-assistance integration with https://github.com/bbubb/squadsync |

</details>

## Summary

I started Soccer-Subber to make youth soccer lineup and substitution planning more manageable. The challenge was not simply filling positions; it was balancing formations, player preferences, playing-time goals, rotation, and development while keeping the recommendations understandable to a coach.

The Python prototype generated interval-based lineups using inspectable scoring rules. It became a practical exercise in translating coaching judgment into an algorithm—and recognizing where a plausible recommendation is not necessarily a fair or optimal one.

## At a Glance

- Modeled players, formations, game plans, and match intervals.
- Generated lineups using player-position scores and tracked minutes and position history.
- Used logging to make recommendations easier to inspect and troubleshoot.

## Key Themes / Reflections

### Coaching decisions became explicit trade-offs

The algorithm considered preferred positions, playing-time targets, substitution rotation, continuity, and opportunities to play different roles. Turning those priorities into scoring rules made their interactions easier to examine than they had been when planning substitutions informally.

The trade-off was that improving one outcome could affect another. A lineup that made sense in one interval did not automatically produce the best balance over the whole match.

### Explainability mattered as much as the recommendation

The prototype ranked player-position options, assigned available players greedily, and logged how scores contributed to the choices. That made it possible to investigate *why* a lineup was suggested.

It also exposed a limitation: scoring playing-time goals does not guarantee they will be met, and locally sensible assignments are not necessarily globally optimal. A coach-facing tool needs both inspectable decisions and stronger scenario-based validation before its recommendations can be relied on.

## Why It Matters

Soccer-Subber connected my coaching experience to algorithm design and software modeling. The lasting lesson was that explainability is part of the product, not an optional feature. The prototype also established a useful starting point for a future bounded lineup-assistance service, where I can explore API integration and cloud deployment without expanding SquadSync's core responsibilities prematurely.
