# SquadSync: Initial Scope, Stack, and Infrastructure

## Snapshot
- **Project:** SquadSync
- **Focus:** Initial platform scope, backend architecture, and local infrastructure
- **Core problem:** Build a real team-management foundation without overfitting the first version to every future platform idea.
- **What this demonstrates:** Full-stack architecture judgment, backend layering, local infrastructure setup, and early observability thinking.

## Summary
SquadSync began as a soccer tactical management system for coaches, managers, players, and parents. The early architecture moved beyond a simple CRUD prototype by establishing a .NET backend, MySQL persistence, service/repository layering, DTO boundaries, API versioning, centralized middleware, and structured logging. The main challenge was balancing the long-term platform vision against the need for a clear, buildable MVP.

## Problem & Constraints
The product idea had immediate domain complexity: users, teams, coaches, players, parents, roles, communication, and tactical planning. Even at the early stage, SquadSync needed enough structure to support future expansion without becoming too abstract too soon.

The backend setup reflected that tension. The project included a layered structure with controllers, data models, repositories, DTOs, middleware, services, utilities, and tests. The project tree shows a .NET backend organized around these concerns rather than a single controller-heavy prototype. :contentReference[oaicite:0]{index=0}

The application startup also included MySQL configuration through Entity Framework Core, API versioning, AutoMapper, authentication/authorization registration, Swagger, Serilog, and Seq logging. :contentReference[oaicite:1]{index=1}

## Decisions & Trade-offs
- **Decision:** Use a layered ASP.NET Core backend instead of a minimal CRUD-only structure.  
  **Why:** The domain was likely to grow, and separating controllers, services, repositories, DTOs, and models would make later changes easier to isolate.  
  **Trade-off:** More upfront structure and mapping overhead before the app had many completed workflows.

- **Decision:** Use MySQL with Entity Framework Core instead of in-memory or file-based persistence.  
  **Why:** The system needed to behave like a real multi-entity backend, not just a demo.  
  **Trade-off:** More setup complexity, migrations, connection configuration, and relational modeling decisions.

- **Decision:** Add observability early through Serilog, Seq, correlation IDs, and exception middleware.  
  **Why:** Debuggability matters once requests begin crossing controller, service, repository, and database boundaries.  
  **Trade-off:** More infrastructure and middleware work before the core product was complete.

- **Decision:** Use API versioning early.  
  **Why:** The platform concept implied future API evolution.  
  **Trade-off:** Versioning adds complexity before multiple client versions actually exist.

## Lessons & Next Moves
The biggest lesson was that architecture should support the next real workflow, not every possible future workflow. The early infrastructure choices were directionally strong: layered backend, database persistence, logging, middleware, and versioned APIs. But the product scope needed to narrow around a concrete vertical slice.

The next version of SquadSync should focus on one end-to-end flow: team/roster management → match/event setup → lineup generation request → result returned → notification/event published. That preserves the architectural seriousness while avoiding the trap of modeling the entire sports-management platform at once.

## Evidence
- **Repo:** `bbubb/squadsync_csharp`
- **Related work:** README describes SquadSync as a soccer tactical management system for coaches, managers, players, and parents. :contentReference[oaicite:2]{index=2}
- **Related files:** `Program.cs`, `project-tree.txt`, `CorrelationIdMiddleware.cs`, `ExceptionHandlingMiddleware.cs`
- **Follow-up:** Create a current architecture diagram showing local backend, MySQL, Seq, and planned cloud migration path.