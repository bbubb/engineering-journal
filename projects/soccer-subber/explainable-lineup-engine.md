# Soccer-Subber Explainable Lineup Engine

## Entry Metadata

| Field | Value |
|---|---|
| Title | Soccer-Subber Explainable Lineup Engine |
| Type | Project Retrospective |
| Subject | Soccer-Subber |
| Date | 2026-05-19 |
| Status | Complete |
| Phase | N/A |
| Sprint | N/A |
| Issue | N/A |
| Tags | python, lineup-engine, explainability, service-boundaries, soccer |
| Related Repo | bbubb/soccer-subber |
| Related Work | Soccer-Subber lineup generation work |

## Summary

Soccer-Subber is a Python application designed to assist with generating soccer lineups.

The core engineering challenge was not simply assigning players to positions. It was balancing formation, player preferences, playtime, substitution intervals, continuity, and positional development in a way that could be logged, inspected, and eventually trusted by a coach.

## Context

Soccer substitution planning involves competing priorities: fairness, player development, positional fit, fatigue, formation shape, goalkeeper handling, minimum playtime, maximum playtime, and continuity across intervals.

The project explored explicit models such as formations, matches, intervals, lineups, players, teams, and game plans.

## Key Decisions

### Used explicit scoring logic

The engine favored inspectable scoring logic instead of a black-box solver.

This makes the result easier to debug and explain, even if it may not always produce a mathematically global optimum.

### Modeled the game in intervals

Youth soccer substitutions are often planned around time segments. Interval-based planning is practical and easier to reason about than fully continuous substitution logic.

### Tracked playtime and position history

Fairness, development, and continuity require memory of who played where and when.

### Preserved explainability as product value

A lineup tool is only useful if a coach can understand why a recommendation was made.

## Trade-offs

Explicit heuristics are easier to explain and adjust, but they require careful testing and may miss some optimal combinations.

A more advanced optimization model could be explored later, but the first valuable version should be deterministic, testable, and explainable.

## Lessons Learned

Explainability is part of the product, not a nice-to-have. A coach-facing decision tool must produce recommendations that can be inspected and trusted.

## Current Relevance

Soccer-Subber is planned as a separate service boundary for SquadSync rather than logic embedded directly inside the core platform.

## Next Steps

Refactor Soccer-Subber into a service-ready structure with clear input schema, validation, scoring strategy, assignment engine, result formatting, explanation metadata, and tests around known scenarios.
