# Legacy SquadSync Initial Scope, Stack, and Infrastructure

## Entry Metadata

| Field | Value |
|---|---|
| Title | Legacy SquadSync Initial Scope, Stack, and Infrastructure |
| Type | Project Retrospective |
| Subject | SquadSync |
| Date | 2026-05-19 |
| Status | Complete |
| Phase | N/A |
| Sprint | N/A |
| Issue | N/A |
| Tags | legacy-architecture, aspnet-core, infrastructure, observability, mvp-scope |
| Related Repo | bbubb/squadsync_csharp |
| Related Work | Archived SquadSync C# implementation |

## Summary

The earlier SquadSync implementation began as a soccer tactical management system for coaches, managers, players, and parents. It moved beyond a simple CRUD prototype by establishing a layered ASP.NET Core backend, MySQL persistence, DTO boundaries, API versioning, centralized middleware, and structured logging.

The main architectural tension was balancing a long-term platform vision against the need for a clear, buildable MVP.

## Context

The product idea had immediate domain complexity: users, teams, coaches, players, parents, roles, communication, and tactical planning.

Even at the early stage, the backend needed enough structure to support future expansion without becoming too abstract too soon. The implementation used controllers, models, repositories, DTOs, middleware, services, utilities, and tests rather than a single controller-heavy prototype.

## Key Decisions

### Used a layered ASP.NET Core backend

The layered structure separated controller concerns, service logic, repository access, DTO mapping, and persistence concerns.

This increased upfront structure but made the codebase more realistic and easier to reason about as the domain grew.

### Used MySQL with Entity Framework Core

Relational persistence made the app behave more like a real multi-entity backend instead of a demo-only prototype.

The trade-off was more setup complexity around migrations, configuration, and relational modeling.

### Added observability early

Serilog, Seq, correlation IDs, and exception middleware were added early because debugging matters once requests cross controller, service, repository, and database boundaries.

### Added API versioning early

API versioning reflected the long-term platform concept, but it also added complexity before multiple client versions existed.

## Lessons Learned

The biggest lesson was that architecture should support the next real workflow, not every possible future workflow.

The early infrastructure choices were directionally strong: layered backend, relational persistence, logging, middleware, and versioned APIs. The scope, however, needed to narrow around a concrete vertical slice.

## Current Relevance

This legacy work informed the current SquadSync rebuild. The new public version keeps the seriousness of the earlier architecture but narrows scope around a cleaner MVP and more deliberate repo-owned workflow.

## Next Steps

Use the current SquadSync rebuild to prove one focused vertical slice before reintroducing broader platform features.
