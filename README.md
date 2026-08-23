# Mohamed Korain

**Backend engineer — ASP.NET Core, distributed systems, enterprise integration.**
Egypt · Arabic / English · Open to permanent and consulting work

![.NET](https://img.shields.io/badge/.NET%2010-512BD4?style=flat-square&logo=dotnet&logoColor=white)
![C#](https://img.shields.io/badge/C%23%2014-239120?style=flat-square&logo=csharp&logoColor=white)
![ASP.NET Core](https://img.shields.io/badge/ASP.NET%20Core-5C2D91?style=flat-square&logo=dotnet&logoColor=white)
![gRPC](https://img.shields.io/badge/gRPC-244c5a?style=flat-square&logo=grpc&logoColor=white)
![EF Core](https://img.shields.io/badge/EF%20Core-512BD4?style=flat-square&logo=nuget&logoColor=white)
![SQL Server](https://img.shields.io/badge/SQL%20Server-CC2927?style=flat-square&logo=microsoftsqlserver&logoColor=white)
![SQLite](https://img.shields.io/badge/SQLite-003B57?style=flat-square&logo=sqlite&logoColor=white)
![Blazor](https://img.shields.io/badge/Blazor-512BD4?style=flat-square&logo=blazor&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)
![OpenTelemetry](https://img.shields.io/badge/OpenTelemetry-000000?style=flat-square&logo=opentelemetry&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-2088FF?style=flat-square&logo=githubactions&logoColor=white)

---

I build back-office systems for regulated environments — banking, identity, and approval
governance — where the software runs on the customer's own hardware, the auditors ask
questions, and "we'll fix it in the next sprint" is not an acceptable answer.

Most of that work is closed-source and cannot be published. So rather than link
repositories I can't show, this page documents **what I've built, how I decide, and how I
work** — the parts that transfer between jobs.

## What I work on

### Enterprise remittance platform · *private, active*

A .NET 10 **modular monolith** deployed inside a bank data centre, handling cross-border
remittance operations end to end.

- **Three bounded-context modules** — Identity, Approval, Configuration — each owning its
  schema, its domain model, and its own migration history
- **Five deployable hosts** from one solution: a gRPC business-logic Engine, a Worker
  driven by both queues and schedules, two Razor operator portals (bank and agent), and a
  YARP reverse-proxy gateway as the single public entry point
- **Wolverine local bus with a transactional SQL outbox** — domain events commit in the
  same transaction as the state change they describe, so a crash mid-publish can't lose or
  duplicate an event
- **Maker-checker approval enforced in the domain**: the requester can never be the
  approver, and that invariant lives in the aggregate rather than in a controller
- **Mutual TLS between tiers**, with X.509 certificates generated in-process during tests
  so the secure path is exercised on every CI run rather than only in production
- **Arabic as a first-class culture from day one** — `InvariantGlobalization` stays off
  with a written rationale, because enabling it is a routine container optimization that
  silently breaks `ar-*` date and number formatting at runtime instead of at build time
- 11 projects, 88 C# files of which 28 are tests, **12 architecture decision records**
- CI runs against a real SQL Server service container, scans full history for secrets, and
  pins transitively vulnerable dependencies as visible top-level references

### Identity & access management platform · *private*

A 13-project solution — a gRPC-only system of record with an administrative front end
over it. ~538 C# files, 26 Razor views, 59 documents.

- **Engine (gRPC only, no HTML)** — authentication, TOTP two-factor with QR enrolment,
  refresh-token rotation, role and permission models, a localization catalogue, and an
  approval workflow, exposed over **four Protobuf contracts**
- **gRPC JSON transcoding**, so REST clients consume the *same* contract as gRPC clients
  and the two can't drift apart
- **Admin portal** — ASP.NET Core MVC over a reusable Razor Class Library
  (`WebUi.Framework`) carrying tag helpers, server-side DataTables paging models,
  middleware, scoped CSS, WebOptimizer bundling and WebMarkupMin minification, so screens
  are composed rather than copy-pasted
- **MediatR request pipeline** with validation and audit behaviours wrapping every handler
- **HybridCache** over a SQL Server distributed cache; Mapperly source-generated mapping;
  Scrutor assembly scanning for registration
- **LDAP / Active Directory** integration, JWT and cookie authentication, ASP.NET Core Data
  Protection with an EF Core key store
- Scriban-templated transactional email over MailKit, SkiaSharp CAPTCHA generation,
  ClosedXML data exchange, and a Spectre.Console log sink for local diagnostics

### Data integration & ETL tooling · *private, early*

An Oracle-targeted import/export utility, clean-layered across five projects
(Domain / Application / Infrastructure / Migrations / UI) behind a WinForms shell.

- **Connection-profile management** — an Oracle-shaped domain entity (host, port,
  service name, SID) persisted with **hashed and salted credentials**, never plaintext,
  even in a local desktop tool
- Repository and unit-of-work layer over EF Core with a SQLite catalogue, Serilog to a
  SQLite sink, FluentValidation, Mapster
- Honest status: the layering, configuration and profile store are built; the extraction
  and transform pipelines are **not yet implemented**

### Network discovery & scanning services · *private*

- **Scanning services** (~223 C# files across two solutions) — concurrent TCP and HTTP
  probing over IP ranges, coordinated with `SemaphoreSlim` and `Task.WhenAll` across a
  port-scan service, a unified scan service and a vulnerability-scan pipeline
- **Camera-finder API** on .NET 8 — Hangfire recurring jobs for scheduled sweeps, EF Core
  to SQL Server, response caching, Docker packaging and GitHub Actions delivery
- **Desktop utilities carried across three .NET generations** — from four .NET Framework
  4.8 WinForms applications on Entity Framework 6, through `net9.0-windows` with Dapper,
  to `net10.0-windows` today. Legacy migration is unglamorous and I've done it repeatedly.

### Blazor WebAssembly client · *private*

A PWA with a service worker, JWT-authenticated API access, `Blazored.FluentValidation`
form validation and `Blazored.LocalStorage` session state.

## Decisions I can defend

I keep architecture decision records, so these are documented rather than remembered.
A few that were genuinely contested:

**A transactional SQL outbox, and no message broker.**
The first design specified RabbitMQ. Re-examined against reality — one consumer,
single-digit events per day — the broker meant new highly-available infrastructure to own
for no additional guarantee, because the outbox is what makes delivery durable; a broker
only moves messages. Removing it removed nothing. If throughput ever justifies a broker,
the outbox is exactly the seam you'd attach it to.

**Dispatch chosen per project, not by habit.**
MediatR where a request pipeline is the requirement, with validation and audit behaviours
around every handler. Wolverine on the remittance platform, where its local bus doubles as
the transactional outbox and a second dispatch abstraction would be redundant. A test
asserts every command resolves to exactly one handler over the real handler graph.

**Rejecting EF Identity in favour of an owned user model.**
The stores didn't fit the provenance and approval requirements — every identity change
needs an approval trail and an originating actor. The password hasher was worth keeping on
its own merits, so it stayed while the rest went.

**Tier boundaries enforced by the build, not by convention.**
The presentation tier holds no database credentials and reaches the business tier over
authenticated gRPC. A test walks the project reference graph and fails the build if a
portal acquires a path to the infrastructure project, directly or transitively. It reads
`.csproj` files as XML rather than loading an MSBuild workspace — a deliberate trade of
generality for a test with no heavyweight dependencies. A boundary nobody can accidentally
cross is worth more than a boundary written in a wiki.

**Arabic and RTL as a design constraint, not a translation task.**
Bilingual error presentation is specified in an ADR, culture-aware formatting is verified
in tests, and the invariant-globalization switch stays off deliberately.

## How I work

**Decisions get written down.** Numbered ADRs recording context, the options considered,
and the consequences accepted. When a decision is later revisited, the record is amended
rather than replaced — including the cases where my own earlier reasoning turned out to be
weaker than I thought. That history is the most useful thing to hand a new engineer.

**Documentation is part of the build, not a byproduct.** A per-file code map, status and
decision logs, and front-matter on every document recording when it was last verified
against the code. The governing rule is that **the code is the truth**: where a document
disagrees with the code, the contradiction gets written down rather than quietly papered
over. One test fails the build when a public contract type is missing XML documentation —
which is what stops the policy being switched off to silence a warning.

**Quality gates live in the build, not in code review.** Warnings as errors, nullable
reference types enabled, analyzers and code style enforced at compile time, and central
package management so versions cannot drift between projects. Transitively vulnerable
dependencies are lifted to visible top-level pins rather than suppressed, so the
vulnerability scan keeps doing its job instead of being muted. A reviewer's attention is
too scarce to spend on things a compiler can catch.

**Tests target the seams that actually break.** Real hosts started over real sockets.
Certificates generated in-process so mutual-TLS paths are covered. An external SQL Server
as the primary integration target with a container fallback — plus a test asserting *which
of the two modes actually ran*, so the unused path cannot rot unnoticed. Architecture tests
over the project reference graph. Behaviour first, implementation details left free to
change.

**Security is a default, not a checklist item.** Least-privilege CI permissions,
full-history secret scanning, credentials hashed and salted even in desktop tooling,
security response headers and a documented hardening checklist, approval trails on
sensitive changes, and dependency CVEs pinned in the open where they're visible.

**Specification before implementation.** Numbered acceptance criteria, phase gates, and a
written plan reviewed before code is written — because the expensive mistakes are
requirements mistakes, and no amount of clean code recovers from building the wrong thing.

**Delivery is part of the definition of done.** Multi-stage Dockerfiles, GitHub Actions
pipelines with service containers, health-check endpoints on every host, and structured
logging plus distributed tracing wired in from the first commit rather than added after
the first production incident.

## Stack

Everything below appears in code I've written and shipped.

| | |
|---|---|
| **Languages** | C# 14 · SQL (T-SQL) · JavaScript · HTML / CSS · XML / JSON / YAML |
| **Platforms** | .NET 10 · .NET 9 · .NET 8 · .NET Framework 4.8 · ASP.NET Core · Razor / MVC · Razor Class Libraries · Blazor WebAssembly · WinForms · Worker Services |
| **Architecture** | Modular monolith · Clean Architecture · Domain-Driven Design · CQRS · vertical slices · bounded contexts · transactional outbox · maker-checker governance · multi-tier deployment topologies |
| **Messaging & jobs** | Wolverine (local bus, SQL outbox, EF Core integration) · MediatR (validation & audit pipeline behaviours) · Hangfire · `BackgroundService` · `Channel<T>` · `SemaphoreSlim` concurrency control |
| **Service communication** | gRPC · Protobuf · gRPC JSON transcoding · YARP reverse proxy · mutual TLS · REST · `HttpClientFactory` · Polly resilience policies |
| **Data** | EF Core (migrations, interceptors, owned types, value converters) · **SQL Server** · **SQLite** · Oracle client · Dapper · Entity Framework 6 (legacy) · repository & unit-of-work patterns |
| **Caching** | HybridCache · distributed cache (SQL Server) · `IMemoryCache` · LazyCache · response caching |
| **Identity & security** | JWT bearer · cookie authentication · TOTP two-factor with QR enrolment (Otp.NET + QRCoder) · refresh-token rotation · **LDAP / Active Directory** · ASP.NET Core Data Protection with EF Core key store · X.509 / mutual TLS · role & permission models · password hashing & salting · CAPTCHA · security response headers · CVE remediation · secret scanning |
| **API design** | Contract-first Protobuf · API versioning (`Asp.Versioning`) · FluentValidation · AutoMapper · Mapperly (source-generated) · Mapster · Scrutor assembly scanning |
| **Testing** | xUnit v3 · Testcontainers · `WebApplicationFactory` integration tests · Shouldly · NSubstitute · architecture tests over the project reference graph · documentation-drift tests · TDD |
| **Observability** | Serilog (Seq, file, SQLite, console, Spectre.Console sinks; enrichers) · OpenTelemetry (ASP.NET Core, EF Core, gRPC instrumentation) · structured logging · health checks |
| **Frontend** | Custom Razor tag helpers · Bootstrap · jQuery · DataTables (server-side paging) · jQuery Unobtrusive Validation · Inputmask · SweetAlert · scoped CSS · WebOptimizer bundling · WebMarkupMin minification |
| **Documents & reporting** | ClosedXML (Excel) · CsvHelper · SkiaSharp (imaging) · QRCoder · Scriban templating · MailKit · Spectre.Console |
| **DevOps** | Docker (multi-stage) · GitHub Actions (service containers, least-privilege permissions) · central package management · `.slnx` · warnings-as-errors · on-premise deployment |
| **Practice** | Architecture decision records · specification-driven development · TDD · code review · legacy migration (.NET Framework → modern .NET) · technical documentation · Arabic / RTL localization |
| **Domains** | Banking & cross-border remittance · identity and access management · approval governance & audit trails · network device discovery · ETL and system integration |

## Activity

<img src="./profile/stats.svg" alt="GitHub stats" height="170"> <img src="./profile/top-langs.svg" alt="Top languages" height="170">

## Public code

**[FluentValidation.AspNetCore.TagHelpers](https://github.com/MKorain/FluentValidation.AspNetCore.TagHelpers)**
— an ASP.NET Core tag helper that generates jQuery Unobtrusive Validation attributes
directly from FluentValidation rules, so client-side validation stops drifting from the
server-side rules it's supposed to mirror. Handles nested complex types and caches
validator descriptors. MIT licensed.

---

**Open to backend and architecture work — permanent or consulting.**
Reach me at [lazzez22@gmail.com](mailto:lazzez22@gmail.com)
