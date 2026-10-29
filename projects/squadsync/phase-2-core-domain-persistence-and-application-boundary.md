# SquadSync Phase 2 — Core Domain, Persistence, and Application Boundary

**Project Retrospective** · SquadSync · Phase 2 — Core Domain and Persistence · October 2026

<details>
<summary>Metadata & related work</summary>

| Field | Value |
|---|---|
| Status | Complete |
| Sprint | Sprints 3–6 |
| Issue | Sprint trackers [#82](https://github.com/bbubb/squadsync/issues/82), [#94](https://github.com/bbubb/squadsync/issues/94), [#102](https://github.com/bbubb/squadsync/issues/102), [#109](https://github.com/bbubb/squadsync/issues/109) |
| Tags | domain-modeling, clean-architecture, ef-core, postgresql, dependency-inversion, integration-testing, agentic-workflow |
| Related Repo | https://github.com/bbubb/squadsync |
| Related Work | SquadSync PRs [#93](https://github.com/bbubb/squadsync/pull/93), [#101](https://github.com/bbubb/squadsync/pull/101), [#104](https://github.com/bbubb/squadsync/pull/104), [#107](https://github.com/bbubb/squadsync/pull/107), [#108](https://github.com/bbubb/squadsync/pull/108), [#112](https://github.com/bbubb/squadsync/pull/112), [#113](https://github.com/bbubb/squadsync/pull/113) |

</details>

## Summary

Phase 2 moved SquadSync from an API foundation into a persisted domain model with its first real Application workflow.

At Phase 2 closeout, the model included `User`, `Team`, `TeamMembership` with a constrained `TeamRole`, person-level `PlayerProfile`, and team-specific `RosterEntry`. These were persisted through EF Core and PostgreSQL, tested both in isolation and against a real database, and exercised through a Development-only demo scenario.

I already knew how to use controllers, services, repositories, and interfaces. Phase 2 showed me how .NET project references and an Application-owned persistence interface can keep database code out of business rules. Building the roster workflow also helped me decide which rules belong to a domain object and which need an Application use case.

## Key Outcomes

- Built the first persisted SquadSync domain model around teams, membership, player profiles, and roster state.
- Kept business rules separate from database code using .NET project references and an Application-owned persistence interface.
- Used focused tests and real PostgreSQL validation to prove each small persistence slice before building on it.
- Saw the issue → Codex implementation → draft PR → human review workflow become a practical development and learning system.

## Engineering Reflections

### Project references made the layer boundaries enforceable

I had used controllers, services, and repositories before. This time I separated Domain, Application, Infrastructure, and API into .NET projects. Domain does not reference Infrastructure or API, so its code cannot call their database or HTTP implementations without changing the project dependencies.

```text
API -> Application
API -> Infrastructure
Infrastructure -> Application
Infrastructure -> Domain
Application -> Domain
Domain -> no outer project
```

Application also does not reference Infrastructure. It defines the persistence interface its use case needs, and Infrastructure implements that interface with EF Core. Crossing that boundary would require an explicit dependency change.

In earlier projects, layers were largely a way to organize responsibilities. Here, the project references also make those rules visible during development and review. I had already thought in domain terms, but this work pushed me to let real business rules shape the code before adding abstractions.

The cost is more projects, interfaces, tests, and files to maintain. I wanted Phase 3's HTTP-to-database workflows to test whether those boundaries were worth the extra work.

### Modeling relationships and lifecycles clarified where rules belong

`TeamMembership` represents the relationship between a `User` and a `Team`, with one constrained `TeamRole`. A membership has its own rules; it is not simply a role stored on the user. **Historical note:** Phase 3 later renamed the represented `User` entity to `Person` to distinguish a rostered person from an authentication account ([ADR 0006](https://github.com/bbubb/squadsync/blob/main/docs/adr/0006-represented-person-identity-api-contract.md)).

A similar distinction emerged between `PlayerProfile` and `RosterEntry`. PlayerProfile contains person-level soccer attributes, while RosterEntry contains state that belongs to that person's membership on a particular team. That made it easier to see which information belongs to a person and which belongs to a particular team roster.

The phase also produced a practical way to think about rule ownership:

- **Domain** owns business concepts, state, and rules intrinsic to those concepts.
- **Application** coordinates use cases and rules that require multiple pieces of domain state.
- **PostgreSQL** protects relational structure and uniqueness.
- **Infrastructure** implements the technical capabilities needed by the application.

The clearest example is roster eligibility. A RosterEntry can validate its own state, but it cannot know whether its related TeamMembership represents a Player. That cross-record rule belongs in the Application workflow:

```text
AddPlayerToRoster
    -> load TeamMembership
    -> require TeamRole.Player
    -> construct RosterEntry
    -> persist through an Application-owned contract
```

This was the first time dependency inversion felt practical rather than just advice to “use an interface”:

```text
AddPlayerToRoster
        -> IRosterPersistence
        <- EfRosterPersistence
```

Application defines what it needs to save a roster entry; Infrastructure supplies the EF Core/PostgreSQL code. I could now see why the interface belongs with the use case rather than the database implementation.

### Small validated slices started to justify their initial overhead

The implementation rhythm felt unusually granular at first. Small contracts were defined, implemented, tested, persisted, and validated before the next piece was added. For simple entities and relationships, that sometimes felt overly formal compared with designing a broader structure up front.

By the end of the phase, the pieces formed a working domain model, persistence layer, Application workflow, and repeatable demo scenario. The process is not strict test-first TDD; it is closer to defining intended behavior, implementing a small slice, adding focused tests, validating the relevant boundary, and merging only when there is evidence that the assumptions hold.

The real PostgreSQL tests were particularly useful because they proved behavior that isolated unit tests cannot: actual materialization, relationships, uniqueness, rollback behavior, and provider-specific persistence assumptions.

I have not concluded that this development rhythm is universally better. Its value so far is that later work builds on code already tested for the behavior I rely on. Phase 3 should be a better test of whether that rigor continues to pay off once the work produces more visible end-to-end features.

### The agentic workflow became a working engineering loop

The issue/PR workflow is probably the most novel part of my development process in this phase.

Issues define bounded intent and acceptance criteria, Codex performs scoped implementation and returns a draft PR, and I remain responsible for understanding the design, reviewing the tests and trade-offs, and deciding whether the work should merge.

That division of labor is beginning to feel less like using AI to generate code and more like an accelerated engineering workflow. Just as importantly, reviewing real implementations has helped architecture terminology become concrete. Concepts such as persistence, application orchestration, domain state, and dependency inversion are easier to understand when I can trace them through a real change and challenge the decisions during review.

## Significance

Phase 2 moved SquadSync beyond scaffolding into real backend design: a persisted domain model, tested relational behavior, an Application workflow, and a repeatable development scenario.

I can now trace the `AddPlayerToRoster` operation through its Application-owned persistence interface to EF Core and PostgreSQL, with tests checking both business rules and database behavior. That taught me more than the names of the patterns: I can explain where a rule belongs, why dependencies point inward, and what the extra structure costs.

## Next Steps

At Phase 2 closeout, the next planned step was to expose the team-and-roster model through an HTTP API.

That would test several questions I was still working through: how Application services and interfaces evolve as the surface grows, whether domain/use-case organization remains intuitive at larger scale, and whether the additional Clean Architecture structure continues to justify its overhead once the project is delivering complete features.
