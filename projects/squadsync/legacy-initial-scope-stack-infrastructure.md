# Legacy SquadSync — Initial Scope, Stack, and Infrastructure

**Project Retrospective** · SquadSync · Earlier backend prototype · May 2026

<details>
<summary>Metadata & related work</summary>

| Field | Value |
|---|---|
| Status | Complete — retrospective |
| Tags | aspnet-core, infrastructure, observability, scope-control |
| Source basis | Earlier SquadSync C# implementation |
| Public follow-up | https://github.com/bbubb/squadsync |

</details>

## Summary

The earlier SquadSync prototype was a soccer tactical-management application for coaches, managers, players, and parents. It used a layered ASP.NET Core backend with MySQL, DTOs, services and repositories, API versioning, exception handling, and structured logging.

That work gave me experience building a realistic backend foundation. In retrospect, it also exposed a tension between designing for a broad platform and delivering a small, usable product.

## At a Glance

- Built an early layered API backed by relational persistence.
- Introduced observability with Serilog, Seq, and correlation IDs.
- Learned that technically reasonable foundations can still outpace validated product scope.

## Key Themes / Reflections

### Structure improved separation but came with an upfront cost

Controllers, services, repositories, DTOs, and persistence responsibilities were separated rather than concentrated in endpoints. MySQL and EF Core made entity relationships and migrations part of the design. These choices helped me reason about responsibilities, but also increased the amount of code and configuration needed before a coach-facing workflow was usable.

The lasting lesson was not that every small application needs several layers. It was that boundaries should make a concrete change easier to understand or test.

### Observability was a useful early investment

Serilog and Seq offered structured visibility into application behavior. Correlation-ID and exception middleware addressed the reality that failures can cross several processing steps before surfacing in an API response.

Unlike speculative platform abstractions, this was a practical concern even in a small system: tracing a request through the backend is valuable long before cloud deployment. API versioning, by contrast, introduced complexity before multiple public API versions were necessary.

### Product scope mattered more than architectural breadth

The historical project considered many entities and capabilities before one complete user workflow had been established. The subsequent public rebuild kept the lessons of layering and observability while narrowing its initial feature set and simplifying the domain.

## Why It Matters

This prototype is a useful reference point for how my engineering judgment evolved: from investing broadly in infrastructure and flexibility toward asking which architectural decisions actually reduce risk for the next deliverable. Those lessons inform the narrower, more deliberate implementation in the current SquadSync repository.
