# Soccer-Subber: Explainable Lineup Engine

## Snapshot
- **Project:** Soccer-Subber
- **Focus:** Lineup generation and substitution planning
- **Core problem:** Convert coaching judgment into deterministic, inspectable software logic.
- **What this demonstrates:** Domain modeling, algorithmic thinking, explainability, and service-design potential.

## Summary
Soccer-Subber is a Python application designed to assist with generating soccer lineups. The core engineering challenge was not simply assigning players to positions. It was balancing formation, player preferences, playtime, substitution intervals, continuity, and dynamic positional development in a way that could be logged, inspected, and eventually trusted by a coach.

## Problem & Constraints
Soccer substitution planning involves competing priorities. A coach may need to balance fairness, player development, positional fit, fatigue, formation shape, goalkeeper handling, minimum playtime, maximum playtime, and continuity across intervals.

The repo README is minimal, describing the project as assisting in generating lineups for soccer squads. :contentReference[oaicite:9]{index=9} The commit history provides more detail: the working project introduced models such as `Formation`, `Game`, `GamePlan`, `Interval`, `Lineup`, `Match`, `Player`, and `Team`. The lineup logic creates intervals, tracks playtime, logs player-position scores, assigns players to positions, and generates player summaries. :contentReference[oaicite:10]{index=10}

The scoring logic shown in the commit includes factors such as minimum playtime, maximum playtime, preferred positions, substitution status, position continuity, and dynamic positions. :contentReference[oaicite:11]{index=11}

## Decisions & Trade-offs
- **Decision:** Use explicit scoring logic instead of a black-box solver.  
  **Why:** Coaches need to understand and trust why a player was placed in a specific position.  
  **Trade-off:** The result may not be globally optimal, but it is easier to debug, explain, and adjust.

- **Decision:** Model the game in intervals.  
  **Why:** Youth soccer substitutions are often planned around time segments, not continuous optimization.  
  **Trade-off:** Interval-based planning is simpler and practical, but less flexible than real-time state-aware substitution logic.

- **Decision:** Track playtime and play logs per player.  
  **Why:** Fairness, development, and substitution planning require memory of who played where and when.  
  **Trade-off:** The engine must manage state carefully across intervals.

- **Decision:** Include dynamic position logic.  
  **Why:** Player development sometimes requires rotating players through different position types.  
  **Trade-off:** Development goals can conflict with competitive optimization.

## Lessons & Next Moves
The strongest lesson from Soccer-Subber is that explainability is part of the product, not a nice-to-have. A lineup tool is only useful if a coach can understand the reasoning behind its recommendations.

The next version should separate the engine into clearer layers:

- input schema
- validation
- scoring strategy
- assignment engine
- result formatting
- explanation metadata
- tests around known lineup scenarios

For integration with SquadSync, Soccer-Subber should expose a narrow API: roster + formation + match settings in, lineup/substitution plan out. The detailed scoring formula should remain private if it becomes part of the project’s competitive value.

## Evidence
- **Repo:** `bbubb/soccer-subber`
- **Related files from commit evidence:** `Formation.py`, `Game.py`, `GamePlan.py`, `Interval.py`, `Lineup.py`, `Match.py`, `Player.py`, `Team.py`
- **Related work:** Commit `git init:working project with logs` shows the working lineup model and scoring/logging logic. :contentReference[oaicite:12]{index=12}
- **Follow-up:** Refactor into a service-ready structure and decide which scoring details should remain private before making any public portfolio version.