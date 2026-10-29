# Legacy SquadSync — Role and Membership Modeling in Context

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

The earlier design explored roles, approval requests, and permissions that could vary by team or organization. The lasting lesson was simpler: a person's responsibility belongs to their relationship with a team, not to the person everywhere in the system.

## Key Outcomes

- Explored contextual responsibilities rather than treating roles as global user properties.
- Encountered the cost of modeling extensibility before a narrow workflow existed.
- Applied that lesson in the public MVP through explicit team memberships and constrained roles.

## Engineering Reflections

### A role needs a context

The question “what role does this user have?” is incomplete without “for which team or organization?” A person can participate in distinct settings without having a different fundamental identity in each.

The historical design explored how to represent that flexibility, including role assignment and approval concepts. It was useful domain exploration, but also introduced more persistence and authorization complexity than the initial soccer workflow could justify.

### Team membership solved the immediate problem without a general role system

The public SquadSync rebuild uses `Person`, `Team`, and `TeamMembership` with one constrained `TeamRole` per membership. `PlayerProfile` holds person-level soccer information, while `RosterEntry` holds details for that person's place on a particular team. The first version of the rebuilt model called represented people `User`; Phase 3 renamed that entity to `Person` to avoid confusing a rostered person with a login account.

I learned to model the relationship a coach needs today, then add flexibility only when another real workflow requires it. A general role system would have meant more database relationships, validation, and tests before the MVP needed them.

## Significance

This changed how I approach domain modeling: start with the team relationships the product actually uses, not a role system designed for every possible future organization.
