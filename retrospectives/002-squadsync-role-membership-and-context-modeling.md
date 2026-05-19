# SquadSync: Role, Membership, and Context Modeling

## Snapshot
- **Project:** SquadSync
- **Focus:** User, role, organization, and permission modeling
- **Core problem:** Model people and organizations that can carry different responsibilities in different contexts.
- **What this demonstrates:** Domain modeling, authorization design, abstraction trade-offs, and early ReBAC/RBAC thinking.

## Summary
One of the hardest SquadSync design problems was separating identity from responsibility. A user is not always just a player, coach, parent, or admin. The same person can hold different roles in different team or organization contexts. The early model explored this by introducing role-bearing entities, org units, role requests, and permissions.

## Problem & Constraints
The domain pushed beyond simple user roles. A basic enum-style role system would not be enough if a user could be a coach for one team, a parent for another, and an admin in a specific organization context.

The code reflects this broader modeling attempt. `User` implements `IRoleBearer`, exposing role-bearing IDs, roles, role requests, and owned org units. :contentReference[oaicite:3]{index=3} `OrgUnit` also implements `IRoleBearer`, meaning both people and organizational units were being explored as entities capable of carrying roles. :contentReference[oaicite:4]{index=4}

The `IRoleBearer` interface defines shared role-bearing properties: role bearer ID, role bearer GUID, roles, and role requests. :contentReference[oaicite:5]{index=5} `Role` then connects a role bearer, org unit, role request, permissions, and status. :contentReference[oaicite:6]{index=6}

## Decisions & Trade-offs
- **Decision:** Explore `IRoleBearer` as a shared abstraction for users and org units.  
  **Why:** The system needed a way to assign roles to more than one kind of entity.  
  **Trade-off:** This increased modeling flexibility but also introduced persistence and relationship complexity.

- **Decision:** Connect roles to org units instead of treating roles as global user attributes.  
  **Why:** Responsibilities only make sense inside a context: a coach, admin, or member of what?  
  **Trade-off:** More accurate domain modeling, but more joins, constraints, and edge cases.

- **Decision:** Include role requests as a first-class model.  
  **Why:** Role assignment is not always automatic; it may need approval, tracking, status, and auditability.  
  **Trade-off:** More workflow complexity before the full UI/product flow existed.

- **Decision:** Model permissions separately from roles.  
  **Why:** This creates a path toward more granular authorization.  
  **Trade-off:** More complex than simple role checks and requires careful service-layer enforcement.

## Lessons & Next Moves
This was one of the most important architectural learning areas in SquadSync. The core insight was that access control is not just “user has role.” It is contextual: user or entity has a role in relation to a team, org unit, activity, or platform scope.

What I would tighten now is the MVP boundary. I would avoid trying to solve every future competition-profile scenario in the first public version. Instead, I would model the immediate workflow explicitly:

- User
- Team / OrgUnit
- Membership
- Role assignment scoped to that team/org
- Minimal permission checks needed for the MVP

The deeper polymorphic or generalized role-bearing model can remain a documented design exploration, but the portfolio implementation should favor clarity, testability, and completed workflows.

## Evidence
- **Repo:** `bbubb/squadsync_csharp`
- **Related files:** `User.cs`, `OrgUnit.cs`, `IRoleBearer.cs`, `Role.cs`, `RoleRequest.cs`, `SQLDbContext.cs`
- **Related work:** `SQLDbContext` includes DbSets for users, org units, roles, permissions, role permissions, role requests, players, teams, activities, features, locations, and sites, showing the broader domain scope under consideration. :contentReference[oaicite:7]{index=7}
- **Follow-up:** Create a private design note comparing explicit `Membership` modeling vs generalized `IRoleBearer` modeling before deciding what belongs in the public MVP.