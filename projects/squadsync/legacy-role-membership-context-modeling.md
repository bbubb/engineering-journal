# Legacy SquadSync Role, Membership, and Context Modeling

## Entry Metadata

| Field | Value |
|---|---|
| Title | Legacy SquadSync Role, Membership, and Context Modeling |
| Type | Architecture Reflection |
| Subject | SquadSync |
| Date | 2026-05-19 |
| Status | Complete |
| Phase | N/A |
| Sprint | N/A |
| Issue | N/A |
| Tags | domain-modeling, authorization, membership, roles, mvp-scope |
| Related Repo | bbubb/squadsync_csharp |
| Related Work | Archived SquadSync C# implementation |

## Summary

One of the hardest SquadSync design problems was separating identity from responsibility. A user is not always just a player, coach, parent, or admin. The same person can hold different roles in different team or organization contexts.

The earlier implementation explored this by modeling role-bearing entities, organizational units, role requests, and permissions.

## Context

The domain pushed beyond simple user roles. A basic enum-style role system would not be enough if a user could be a coach for one team, a parent for another, and an admin in a specific organization context.

The deeper question was how much of that complexity belonged in the first public MVP.

## Key Decisions

### Explored shared role-bearing abstractions

The earlier model explored an `IRoleBearer` style abstraction so more than one type of entity could carry roles.

This increased flexibility but introduced persistence and relationship complexity.

### Scoped roles to context

Roles were treated as contextual rather than global. A responsibility only makes sense in relation to a team, organization, or other bounded context.

This was more accurate, but it required more careful modeling.

### Modeled role requests and permissions separately

Role requests created a path for approval/status workflows. Separate permissions created a path for more granular authorization.

Both were useful but likely too heavy for the first public MVP slice.

## Trade-offs

The deeper model was architecturally interesting but risked distracting from the first usable vertical slice.

The current rebuild should favor explicit `User`, `Team`, `Membership`, and role assignment concepts first. Deeper polymorphic role-bearing abstractions can remain a private or later-stage design exploration.

## Lessons Learned

Access control is not simply `user has role`. It is contextual: a user has a responsibility in relation to a team, organization, activity, or platform scope.

The MVP should preserve that insight without overbuilding the generalized authorization model too early.

## Current Relevance

This entry informs the current SquadSync domain model and helps explain why the public MVP should prefer clear membership modeling over broad abstraction.

## Next Steps

Use the current domain model to implement the immediate membership and role needs first. Revisit broader authorization patterns only after the MVP workflow is stable.
