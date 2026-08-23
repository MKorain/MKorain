# Mohamed Korain

**Backend engineer — ASP.NET Core, distributed system design.** Egypt · Arabic / English

![.NET](https://img.shields.io/badge/.NET%2010-512BD4?style=flat-square&logo=dotnet&logoColor=white)
![C#](https://img.shields.io/badge/C%23%2014-239120?style=flat-square&logo=csharp&logoColor=white)
![ASP.NET Core](https://img.shields.io/badge/ASP.NET%20Core-5C2D91?style=flat-square&logo=dotnet&logoColor=white)
![gRPC](https://img.shields.io/badge/gRPC-244c5a?style=flat-square&logo=grpc&logoColor=white)
![EF Core](https://img.shields.io/badge/EF%20Core-512BD4?style=flat-square&logo=nuget&logoColor=white)
![SQL Server](https://img.shields.io/badge/SQL%20Server-CC2927?style=flat-square&logo=microsoftsqlserver&logoColor=white)
![Oracle](https://img.shields.io/badge/Oracle-F80000?style=flat-square&logo=oracle&logoColor=white)
![Blazor](https://img.shields.io/badge/Blazor-512BD4?style=flat-square&logo=blazor&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)
![OpenTelemetry](https://img.shields.io/badge/OpenTelemetry-000000?style=flat-square&logo=opentelemetry&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-2088FF?style=flat-square&logo=githubactions&logoColor=white)

---

Most of what I build is closed-source — on-premise banking and identity systems that
cannot be published. So rather than linking repositories I can't show, this page
describes **the engineering decisions I make and why**, which is the part of the work
that actually transfers.

## What I work on

**Enterprise remittance platform** *(private, active)* — a .NET 10 modular monolith for a
bank data centre. Three bounded-context modules (Identity, Approval, Configuration)
across five hosts: a gRPC business-logic Engine, a queue- and schedule-driven Worker,
two Razor portals, and a YARP gateway that is the only process in the DMZ.

The shape follows from one hard constraint: **no database credential may exist outside
the business tier.** The portals therefore have no `DbContext` and no connection string —
they reach the Engine over gRPC with mutual TLS. That rule is enforced by a test that
walks the project reference graph and fails the build if a portal acquires a path to the
infrastructure project, directly or transitively. A boundary nobody can accidentally
cross is worth more than a boundary written in a wiki.

**Identity engine + admin portal** *(private)* — a gRPC-only authentication and
authorization core (TOTP 2FA, refresh tokens, roles and permissions, LDAP/Active
Directory) with a Razor/MVC administration front end, over a reusable tag-helper and
asset-pipeline layer shared across screens.

**Smaller things** — an Oracle import/export utility (in progress) with encrypted
connection profiles, targeting Oracle, SQL Server and SQLite; a network-camera discovery
service on background workers; desktop tooling carried across three .NET generations,
from .NET Framework 4.8 with EF6 to modern .NET.

## Decisions I can defend

I keep architecture decision records, so these are documented rather than remembered.
A few that were genuinely contested:

- **A transactional SQL outbox, and no message broker.** The first design specified
  RabbitMQ. Re-examined against reality — one consumer, single-digit events per day — the
  broker meant new HA infrastructure to own for no extra guarantee: the outbox is what
  makes delivery durable, a broker only moves messages. Removing it removed nothing.
- **Dispatch chosen per project, not by habit.** MediatR where a request pipeline is the
  requirement — validation and audit behaviours around every handler. Wolverine on the
  remittance platform, where its local bus doubles as the transactional outbox. A test
  asserts every command resolves to exactly one handler over the real handler graph.
- **Rejecting EF Identity for an owned user model.** The stores didn't fit the provenance
  and approval requirements; the password hasher was worth keeping on its own.
- **Arabic as a first-class culture from the walking skeleton**, not a later localization
  pass. `InvariantGlobalization` stays off with a written reason, because switching it on
  is a routine container optimization that breaks `ar-*` formatting at runtime rather
  than at build time.
- **Sensitive changes require maker-checker approval** — the requester is never the
  approver, enforced in the domain rather than in the UI.

## How I work

**Documentation is part of the build.** Numbered ADRs, a per-file code map, and status and
decision logs, with front-matter recording when each document was last verified against
the code. The governing rule is that the code is the truth: where a document disagrees
with the code, the contradiction gets written down rather than quietly fixed. One test
fails the build when a public contract type is missing XML documentation, which is what
stops the policy being switched off to quiet a warning.

**Warnings are errors.** Nullable reference types on, code style enforced in the build,
central package management so versions can't drift between projects. Transitive
vulnerable dependencies are lifted to visible top-level pins rather than suppressed, so
the CI vulnerability scan keeps working instead of being silenced.

**Tests target the seams that actually break.** Real hosts started over real sockets with
certificates generated in-process for the mTLS paths; an external SQL Server as the
primary integration target with a container fallback — and a test asserting which of the
two modes actually ran, so the unused path can't rot unnoticed.

## Stack

Everything below is in code I've written, not just read about.

| | |
|---|---|
| **Platform** | .NET 10 · C# 14 · .NET 9 / 8 · .NET Framework 4.8 · ASP.NET Core · Razor / MVC · Razor Class Libraries · Blazor WebAssembly · WinForms |
| **Service design** | Modular monolith · Clean Architecture · CQRS · DDD · bounded-context modules · vertical slices |
| **Dispatch & messaging** | MediatR (pipeline behaviours: validation, audit) · Wolverine (local bus, transactional SQL outbox, EF Core integration) · Hangfire · `BackgroundService` workers · `Channel<T>` |
| **Inter-service** | gRPC · Protobuf · gRPC JSON transcoding · YARP reverse proxy · mutual TLS · `HttpClientFactory` · Polly |
| **Data** | EF Core (SQL Server · SQLite · migrations · interceptors) · **Oracle** (`Oracle.ManagedDataAccess.Core`) · Dapper · `Microsoft.Data.SqlClient` · Entity Framework 6 (legacy) |
| **Caching** | HybridCache · `IDistributedCache` (SQL Server) · `IMemoryCache` · LazyCache |
| **Identity & security** | JWT bearer · cookie auth · ASP.NET Core Data Protection · TOTP two-factor (Otp.NET + QRCoder) · **LDAP / Active Directory** · X.509 certificates · role & permission models · maker-checker approval · CVE-pinned dependencies |
| **API** | API versioning (`Asp.Versioning`) · FluentValidation · AutoMapper · Mapperly (source-generated) · Mapster · Scrutor |
| **Testing** | xUnit v3 · Testcontainers · `WebApplicationFactory` · Shouldly · NSubstitute · architecture tests over the project reference graph · documentation-drift tests |
| **Observability** | Serilog (Seq · SQLite · file · console; enrichers) · OpenTelemetry (ASP.NET Core · EF Core · gRPC instrumentation) · health checks |
| **Frontend** | Bootstrap · jQuery · DataTables · jQuery Unobtrusive Validation · SweetAlert · WebOptimizer bundling · WebMarkupMin · scoped CSS · custom tag helpers |
| **Documents & reporting** | ClosedXML (Excel) · CsvHelper · SkiaSharp · Scriban templating · MailKit · Spectre.Console |
| **Delivery** | Docker · nginx · GitHub Actions (service containers, least-privilege permissions) · central package management · `.slnx` · warnings-as-errors |
| **Practice** | Architecture decision records · TDD · spec-driven development · Arabic / RTL localization · SQL Server, Oracle & SQLite schema design |

## Activity

<img src="./profile/stats.svg" alt="GitHub stats" height="170"> <img src="./profile/top-langs.svg" alt="Top languages" height="170">

## Public code

**[FluentValidation.AspNetCore.TagHelpers](https://github.com/MKorain/FluentValidation.AspNetCore.TagHelpers)**
— generates jQuery Unobtrusive Validation attributes from FluentValidation rules, so
client-side validation stops drifting from the server-side rules it mirrors. Handles
nested complex types and caches validator descriptors. MIT.

---

**Open to backend and architecture work — permanent or consulting.**
[lazzez22@gmail.com](mailto:lazzez22@gmail.com)
