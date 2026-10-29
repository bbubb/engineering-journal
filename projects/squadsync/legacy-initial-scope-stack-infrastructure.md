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

That work taught me how the pieces of a backend fit together. It also showed how much time I could spend preparing for future features before delivering one useful coach-facing workflow.

## Key Outcomes

- Built an ASP.NET Core API with controllers, services, repositories, and MySQL persistence.
- Introduced observability with Serilog, Seq, and correlation IDs.
- Learned that technically reasonable foundations can still outpace validated product scope.

## Engineering Reflections

### Layers clarified responsibilities but delayed the first feature

Controllers, services, repositories, DTOs, and persistence responsibilities were separated rather than concentrated in endpoints. MySQL and EF Core made entity relationships and migrations part of the design. These choices made responsibilities clearer, but required more code and configuration before a coach could use the application.

The lasting lesson was not that every small application needs several layers. It was that boundaries should make a concrete change easier to understand or test.

### Structured logs made backend requests easier to trace

Serilog produced structured logs that I could inspect in Seq. Correlation IDs helped connect messages from the same request, while exception middleware gave the API a consistent way to handle failures.

Even a small backend benefits from being able to trace a request through its layers when something fails. API versioning, by contrast, introduced complexity before multiple public API versions were necessary.

### The next coach workflow mattered more than future features

The historical project considered many entities and capabilities before one complete coach-facing workflow had been established. The subsequent public rebuild kept the lessons of layering and observability while narrowing its initial feature set and simplifying the domain.

## Significance

I learned to ask whether each layer or tool helps the next real feature, rather than adding it because a larger platform might need it someday. The public SquadSync rebuild applies that narrower approach while keeping the useful lessons about separation of responsibilities and request tracing.
