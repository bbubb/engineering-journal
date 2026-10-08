# Legacy SquadSync — Modeling Roles in Context

**Architecture Reflection** · SquadSync · Earlier domain-model exploration · May 2026

<details>
<summary>Metadata & related work</summary>

| Field | Value |
|---|---|
| Status | Complete — retrospective |
| Tags | domain-modeling, membership, authorization, scope-control |
| Source basis | Earlier SquadSync domain-model work and design discussions |
| Public follow-up | https://github.com/bbubb/squadsync |

</details>

## Summary

An early SquadSync design challenge was separating a person's identity from responsibilities that change across teams and organizations. A global label such as “coach” or “player” could not adequately describe someone who participated in more than one context.

The earlier design explored a generalized role-bearing approach, alongside organizational context, role requests, and permissions. The most durable lesson was not the abstraction itself: it was recognizing that a **relationship can carry business meaning and rules** without becoming a permanent attribute of a person.

## At a Glance

- Explored contextual responsibilities rather than treating roles as global user properties.
- Encountered the cost of modeling extensibility before a narrow workflow existed.
- Carried the relationship-first insight into the simpler public MVP model.

## Key Themes / Reflections

### A role needs a context

The question “what role does this user have?” is incomplete without “for which team or organization?” A person can participate in distinct settings without having a different fundamental identity in each.

The historical design explored how to represent that flexibility, including role assignment and approval concepts. It was useful domain exploration, but also introduced more persistence and authorization complexity than the initial soccer workflow could justify.

### Explicit membership was a better MVP boundary

The public SquadSync rebuild deliberately narrowed the problem to `User`, `Team`, and `TeamMembership` with a constrained `TeamRole`. It also distinguishes person-level `PlayerProfile` data from team-specific `RosterEntry` data. Those decisions preserve the important domain distinction while avoiding a generalized authorization framework.

What changed in my judgment was where to place extensibility: first define the relationship and lifecycle the product actually needs, then expand only when another concrete workflow demonstrates the need. Flexibility has a real cost in schema design, validation, and testability.

## Why It Matters

This was an early example of moving from theoretically broad domain modeling toward explicit, verifiable product behavior. That shift—from modeling broad possible relationships to implementing the relationships a coach actually needs—continues to guide how I approach domain scope.
