# SquadSync Phase 3 — HTTP Workflows and Implementation Ownership

**Project Retrospective** · SquadSync · Phase 3 — Roster Management API · October 2026

<details>
<summary>Metadata & related work</summary>

| Field | Value |
|---|---|
| Status | Draft — owner editorial review |
| Sprint | Sprints 7–9 |
| Issue | Sprint trackers [#116](https://github.com/bbubb/squadsync/issues/116), [#129](https://github.com/bbubb/squadsync/issues/129), [#145](https://github.com/bbubb/squadsync/issues/145) |
| Tags | aspnet-core, http-api, clean-architecture, ef-core, postgresql, integration-testing, owner-authored-development, agentic-workflow |
| Related Repo | https://github.com/bbubb/squadsync |
| Related Work | Owner-authored [#133 / PR #140](https://github.com/bbubb/squadsync/pull/140) and [#149 / PR #152](https://github.com/bbubb/squadsync/pull/152); HTTP/PostgreSQL validation [PR #143](https://github.com/bbubb/squadsync/pull/143) and [PR #153](https://github.com/bbubb/squadsync/pull/153); [ADR 0006](https://github.com/bbubb/squadsync/blob/main/docs/adr/0006-represented-person-identity-api-contract.md) |

</details>

## Summary

Phase 3 took SquadSync's persisted team-and-roster model and exposed it through working ASP.NET Core HTTP endpoints. The Development-only API can create and retrieve represented people and teams, create and list team memberships and roster entries, and explicitly create, retrieve, and update a person's PlayerProfile. The final tests exercised the actual HTTP-to-PostgreSQL path and verified that test writes roll back without disturbing existing development data.

The most useful change for me was moving from reviewing layered implementations to writing two complete slices myself: a team-membership collection query in Sprint 8 and a PlayerProfile update in Sprint 9. Those exercises exposed decisions that can look small in a PR—where a missing record is distinguished from an empty list, how an entity changes without losing its identity, and where an EF Core exception should be translated into application behavior.

## Key Outcomes

- Delivered a tested, Development-only HTTP API for the core Person, Team, Membership, RosterEntry, and PlayerProfile workflows; production authentication and full roster editing are **not** part of this milestone.
- Implemented two deliberately owner-authored slices: team-scoped membership listing ([PR #140](https://github.com/bbubb/squadsync/pull/140)) and full-replacement PlayerProfile updates ([PR #152](https://github.com/bbubb/squadsync/pull/152)).
- Established real HTTP-to-PostgreSQL tests across separate request contexts, with opt-in database execution, transaction rollback, and preservation checks for existing rows and migration history.
- Refined the human/Codex workflow: use guided implementation where it teaches a meaningful new boundary, but avoid separate execution plans, comparison exercises, and review commentary that do not add engineering value.

## Engineering Reflections

### HTTP contracts forced the domain boundaries to become explicit

The Phase 3 rename from `User` to `Person` was more than terminology. `Person` represents someone on a team, whether or not that individual can sign into SquadSync. An authentication `Account`, its relationship to represented people, and permission checks remain separate future decisions. A GUID in a route identifies a record; it is not proof that the caller may access it.

The same separation applies to soccer data. A `PlayerProfile` belongs to a Person, while `TeamMembership` and `RosterEntry` describe that person's role and roster state on a particular team. A profile is created explicitly, not automatically when a person joins a roster, and is shared across their team memberships. This let us expose useful workflows without creating a duplicate profile for each team.

The security trade-off is deliberate but important: the management endpoints are anonymous and mapped only in Development for trusted localhost use. That is enough for this implementation milestone, not a production authorization model. I do not want the existence of working endpoints or passing tests to obscure that boundary.

### Writing the membership query made API semantics and layer responsibilities concrete

For [Issue #133](https://github.com/bbubb/squadsync/issues/133), I implemented `GET /api/teams/{teamId}/memberships` across the Application query, persistence interface, EF Core adapter, controller, and tests. The existing Domain entities did not need to change just to call the work a full vertical slice.

One seemingly minor distinction mattered: an **existing Team with no memberships returns `200 []`**, while a nonexistent Team returns `404`. A collection query that simply returned no rows could not distinguish those situations; the Application workflow had to account for Team existence. Invalid identifiers also had their own `400` behavior. These are API contract decisions, not just controller formatting.

The database query filters memberships by Team and orders them by Person's last name, first name, then membership ID. Doing that in the EF query keeps the ordering deterministic and avoids loading unrelated memberships and sorting them in application memory. The work helped me see why Application owns the query capability it needs while Infrastructure owns how PostgreSQL supplies the results.

We also tried an independent Codex reference implementation for comparison. That was useful as a learning exercise, but it should not become a routine contest between two implementations: a difference in style is not automatically a defect in owner-authored code.

### Updating a PlayerProfile taught a different persistence problem from creating one

The next owner-authored slice, [Issue #149](https://github.com/bbubb/squadsync/issues/149), built on working profile POST/GET endpoints. `PUT` had to **replace all three editable attributes on an existing profile**, preserving `Id` and `PersonId`; it must not create a missing profile. All three JSON property names are required, but an explicit `null` clears an optional value. This makes a missing field different from a deliberate request to clear it.

I added a guarded Domain update behavior rather than unrestricted property setters. The critical detail was validating the proposed values *before* assigning any of them: a failed update must not leave a partially changed object. Working through the failing and then passing Domain tests made that rule more concrete than reading it in an architecture document.

Persistence introduced a second distinction. Creating a new entity and updating an EF-tracked entity are different operations. For an update, Infrastructure loads the existing profile for tracking, applies its Domain behavior, saves it, and preserves identity. I initially had to sort out the difference between general data-concurrency exceptions and EF Core's `DbUpdateConcurrencyException`. The eventual boundary was clearer: Infrastructure recognizes the EF-specific missing-row condition and translates it into an Application-level not-found exception; the API maps that outcome to `404`. Neither the Domain nor the HTTP controller needs to depend on EF Core's exception type.

### Tests and AI assistance were useful when they reduced uncertainty

Controller tests with fake persistence proved request validation and status mapping, but they could not prove that EF Core would materialize and update the profile correctly in PostgreSQL. We therefore kept separate opt-in database tests and ultimately tested the full HTTP workflow through real persistence adapters. The final Sprint 9 evidence recorded **411 ordinary passing tests** and **18 passing opt-in PostgreSQL tests**.

The transactional HTTP harness was particularly valuable: successive requests used fresh EF contexts inside a test-owned transaction; a separate connection checked that writes were not externally visible; and complete-row fingerprints checked that original data and migration history survived both successful and deliberately failed test runs. That is stronger evidence than merely seeing a `200` response. It also has a cost in test infrastructure and review effort, so reusing the harness mattered more than building another one for PlayerProfile.

I found the owner-authored exercises more educational than simply asking Codex to implement every slice, although they took longer. I would repeat them selectively for new concepts. The surrounding workflow also needs restraint: a well-scoped GitHub issue can be enough without a separate ExecPlan or contract PR, and a review comment should identify an actual concern rather than restate the acceptance checklist. The goal is traceable engineering judgment, not extra paperwork.

## Significance

At Phase 3 closeout, I could follow a request from its HTTP contract through Application orchestration, Domain invariants, EF Core persistence, and PostgreSQL validation—and had implemented that path myself for both a read workflow and an update workflow. The stronger lesson was how to choose the boundary that owns each decision, then test the behavior at the level where it could actually fail. I also became more deliberate about when AI implementation, guided hands-on work, and additional documentation each earn their cost.

## Next Steps

Phase 3 ended with a verified **local API milestone**, not a deployable coach-facing application. The next planning conversation will decide how to connect a web interface to these existing workflows while respecting the still-deferred authentication and authorization boundary.
