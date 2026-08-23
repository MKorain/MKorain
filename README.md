# Mohamed Korain

**Backend engineer — ASP.NET Core, distributed systems, banking integration.**
Egypt · Arabic / English · Open to permanent and consulting work

![.NET](https://img.shields.io/badge/.NET%2010-512BD4?style=flat-square&logo=dotnet&logoColor=white)
![C#](https://img.shields.io/badge/C%23%2014-239120?style=flat-square&logo=csharp&logoColor=white)
![ASP.NET Core](https://img.shields.io/badge/ASP.NET%20Core-5C2D91?style=flat-square&logo=dotnet&logoColor=white)
![gRPC](https://img.shields.io/badge/gRPC-244c5a?style=flat-square&logo=grpc&logoColor=white)
![EF Core](https://img.shields.io/badge/EF%20Core-512BD4?style=flat-square&logo=nuget&logoColor=white)
![Java](https://img.shields.io/badge/Java-ED8B00?style=flat-square&logo=openjdk&logoColor=white)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-6DB33F?style=flat-square&logo=springboot&logoColor=white)
![Oracle](https://img.shields.io/badge/Oracle%20%2F%20PL%2FSQL-F80000?style=flat-square&logo=oracle&logoColor=white)
![SQL Server](https://img.shields.io/badge/SQL%20Server-CC2927?style=flat-square&logo=microsoftsqlserver&logoColor=white)
![Blazor](https://img.shields.io/badge/Blazor-512BD4?style=flat-square&logo=blazor&logoColor=white)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=flat-square&logo=docker&logoColor=white)
![OpenTelemetry](https://img.shields.io/badge/OpenTelemetry-000000?style=flat-square&logo=opentelemetry&logoColor=white)
![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-2088FF?style=flat-square&logo=githubactions&logoColor=white)

---

I build back-office systems for Egyptian and Gulf banks — payment switches, clearing and
settlement, credit bureau integration, and identity governance. The software runs inside
the customer's own data centre, the auditors ask questions, and "we'll fix it next sprint"
is not an acceptable answer.

Almost all of it is closed-source. So rather than link repositories I can't show, this
page documents **what I've built and how I work** — the part that transfers between jobs.

## What I work on

### Banking & payments — *TIT (current)*

Delivery for a long list of named institutions: the **Agricultural Bank of Egypt (ABE)**,
**Mashreq**, **Suez Canal Bank**, **Banque du Caire**, **Citibank**, **ADCB**, **AAIB**,
**Baraka**, **Eskan**, **SAIB**, **The United Bank**, and the **Central Bank of Egypt**
(CBE) — plus ACH work for the Ugandan market.

- **Cross-border remittance platform** — a .NET 10 modular monolith: three bounded-context
  modules (Identity, Approval, Configuration), five deployable hosts, a Wolverine local bus
  with a transactional SQL outbox, maker-checker approval enforced in the domain, and
  mutual TLS between tiers. 12 architecture decision records.
- **ISO 8583 payment switch interface** — **Java / Spring Boot**, built on Spring
  Integration TCP for the socket layer and Spring Data JPA for persistence. Card switch
  traffic is a protocol problem, not a REST problem, and Spring Integration is the right
  tool for it.
- **InstaPay integration** for Egypt's instant payment network — API, worker service,
  bank-facing web UI, a gateway, and a health-check monitor as separate deployables.
- **ACH / clearing and settlement** — ABE, Baraka and Uganda ACH systems; issuer and
  acquirer reconciliation uploaders; ATM reconciliation; NCR and Diebold Nixdorf **ATM
  journal reader services** that watch, parse and reconcile terminal journals.
- **i-Score credit bureau integration** — the recurring engagement across ADCB, Citibank
  retail, ADBC, KFH, FAB, EBE and others: e-credit submission and retail desktop clients
  against the Egyptian credit bureau.
- **Regulatory & reporting** — Basel III / NBFI reporting, IFRS 9 ECL, financial
  inclusion, **goAML** anti-money-laundering reporting for the Suez Canal authority,
  declarations (*Ekrarat*) systems for several banks, and estate/inheritance processing.
- **AgriTransfer** — an agricultural payment-transfer system for ABE, carried from a
  WebForms original through a .NET Core rewrite, with a companion token-validation service.
- **Oracle data engineering** — **PL/SQL** procedures and schema-merge handling for Suez
  Canal Bank's Oracle database, plus Excel-to-Oracle loaders, connection managers and
  encrypted-credential tooling. IBM **FileNet** document integration and OCR pipelines.
- **HCM / HR platform** — self-service APIs and a company API behind a **Flutter** mobile
  application.

### Identity & access management platform — *private*

A 13-project solution: a gRPC-only system of record with an admin front end. ~538 C#
files, 26 Razor views, 59 documents.

- **Engine (gRPC only, no HTML)** — authentication, TOTP two-factor with QR enrolment,
  refresh-token rotation, roles and permissions, a localization catalogue and an approval
  workflow across **four Protobuf contracts**
- **gRPC JSON transcoding**, so REST clients consume the *same* contract as gRPC clients
- **Admin portal** over a reusable Razor Class Library carrying tag helpers, server-side
  DataTables paging, middleware, scoped CSS, WebOptimizer bundling and WebMarkupMin
- **MediatR pipeline** with validation and audit behaviours around every handler;
  HybridCache over a SQL Server distributed cache; Mapperly; Scrutor
- **LDAP / Active Directory**, JWT and cookie auth, Data Protection with an EF Core key
  store, Scriban-templated mail over MailKit, SkiaSharp CAPTCHA

### Travel & booking platform — *Egypt-Pass*

A multi-tenant travel product: **.NET** booking APIs (EgyptPass, TravoClick, TravoGo) with
**TypeScript / Next.js** admin and supplier dashboards, and a **Python** AI portal built on
FastAPI, LangChain/LangGraph and a Qdrant vector store for retrieval.

### Enterprise & product work — *Microship, KorainDev*

Inventory management and **e-invoicing** backends for Egyptian Tax Authority compliance;
an ERP core. Independently: reusable ASP.NET Core solution templates, a Blazor WebAssembly
PWA, network discovery and camera-scanning services, and desktop utilities carried across
three .NET generations — from .NET Framework 4.8 on EF6, through `net9.0-windows` with
Dapper, to `net10.0-windows`. Legacy migration is unglamorous and I've done it repeatedly.

## How I work

**Agile delivery.** Iterative sprints against a live client backlog, working directly with
bank stakeholders rather than through a spec thrown over a wall. Most of the systems above
run in parallel, so the real skill is sequencing and honest estimates — including saying
when a date is not achievable.

**Decisions get written down.** Numbered ADRs recording context, the options considered
and the consequences accepted. When a decision is later revisited the record is amended
rather than replaced — including where my own earlier reasoning turned out weaker than I
thought. That history is the most useful thing to hand a new engineer.

**Documentation is part of the build, not a byproduct.** A per-file code map, status and
decision logs, and front-matter on every document recording when it was last verified
against the code. The governing rule is that **the code is the truth**: where a document
disagrees with the code, the contradiction gets written down rather than quietly papered
over. One test fails the build when a public contract type is missing XML documentation.

**Quality gates live in the build, not in code review.** Warnings as errors, nullable
reference types on, analyzers and code style enforced at compile time, central package
management so versions can't drift. Transitively vulnerable dependencies are lifted to
visible top-level pins rather than suppressed, so the vulnerability scan keeps doing its
job. A reviewer's attention is too scarce to spend on what a compiler can catch.

**Tests target the seams that actually break.** Real hosts started over real sockets.
Certificates generated in-process so mutual-TLS paths are covered on every CI run. An
external SQL Server as the primary integration target with a container fallback — plus a
test asserting *which of the two modes actually ran*, so the unused path can't rot
unnoticed. Architecture tests over the project reference graph fail the build if a portal
acquires a path to the infrastructure project, directly or transitively.

**Security is a default, not a checklist item.** Least-privilege CI permissions,
full-history secret scanning, credentials hashed and salted even in desktop tooling,
security response headers, approval trails on sensitive changes, and dependency CVEs
pinned in the open where they stay visible.

**Integration work is mostly other people's constraints.** Fixed-width files, ISO 8583
frames, Oracle schemas I don't own, a bank's certificate policy, a regulator's deadline.
The engineering is in making that legible and testable on my side of the boundary.

**Delivery is part of the definition of done.** Multi-stage Dockerfiles, GitHub Actions
with service containers, health checks on every host, structured logging and distributed
tracing wired in from the first commit rather than after the first production incident.

## Stack

| | |
|---|---|
| **Languages** | C# 14 · **Java** · **Python** · SQL — T-SQL & **PL/SQL** · JavaScript · TypeScript · HTML / CSS |
| **.NET** | .NET 10 / 9 / 8 · .NET Framework 4.8 · ASP.NET Core · Razor / MVC · Razor Class Libraries · Web Forms (legacy) · Blazor WebAssembly · WinForms · Worker Services |
| **Java** | **Spring Boot** · Spring Integration (TCP / socket servers) · Spring Data JPA · Maven · Lombok · JUnit |
| **Architecture** | Modular monolith · Clean Architecture · Domain-Driven Design · CQRS · vertical slices · bounded contexts · transactional outbox · maker-checker governance · multi-tier deployment |
| **Messaging & jobs** | Wolverine (local bus, SQL outbox) · MediatR (validation & audit behaviours) · Hangfire · `BackgroundService` · `Channel<T>` · Windows services · file-watcher pipelines |
| **Service communication** | gRPC · Protobuf · gRPC JSON transcoding · **ISO 8583** · YARP reverse proxy · mutual TLS · REST · `HttpClientFactory` · Polly |
| **Data** | **Oracle** (PL/SQL, procedures, schema merges) · **SQL Server** · SQLite · EF Core (migrations, interceptors, owned types) · Dapper · Entity Framework 6 · repository & unit-of-work |
| **Caching** | HybridCache · distributed cache (SQL Server) · `IMemoryCache` · LazyCache · response caching |
| **Identity & security** | JWT · cookie auth · TOTP two-factor with QR enrolment · refresh-token rotation · **LDAP / Active Directory** · Data Protection with EF Core key store · X.509 / mutual TLS · role & permission models · password hashing & salting · CAPTCHA · CVE remediation · secret scanning |
| **Python & AI** | FastAPI · LangChain / LangGraph · Qdrant vector store · Pydantic · OCR and document-extraction pipelines (PyMuPDF, pdfplumber) · pandas · OpenCV |
| **Frontend** | Razor tag helpers · Bootstrap · jQuery · DataTables (server-side paging) · Unobtrusive Validation · SweetAlert · scoped CSS · WebOptimizer · WebMarkupMin · **Next.js / React** dashboards |
| **Mobile** | **Flutter** mobile app backends (HCM self-service APIs) |
| **Testing** | xUnit v3 · Testcontainers · `WebApplicationFactory` · Shouldly · NSubstitute · architecture tests · documentation-drift tests · TDD |
| **Observability** | Serilog (Seq, file, SQLite, console; enrichers) · OpenTelemetry (ASP.NET Core, EF Core, gRPC) · structured logging · health checks |
| **Documents & reporting** | ClosedXML (Excel) · CsvHelper · SkiaSharp · QRCoder · Scriban · MailKit · Spectre.Console · IBM FileNet · OCR |
| **DevOps** | Docker (multi-stage) · GitHub Actions (service containers, least-privilege) · central package management · `.slnx` · warnings-as-errors · on-premise deployment |
| **Practice** | **Agile / iterative delivery** · architecture decision records · specification-driven development · TDD · code review · legacy migration · technical documentation · Arabic / RTL localization |
| **Domains** | Retail & cross-border payments · ACH clearing and settlement · ATM reconciliation · credit bureau (i-Score) integration · AML / goAML reporting · Basel III & IFRS 9 · e-invoicing · identity & access management · approval governance |

## Activity

<img src="./profile/stats.svg" alt="GitHub stats" height="170"> <img src="./profile/top-langs.svg" alt="Top languages" height="170">

## Public code

**[FluentValidation.AspNetCore.TagHelpers](https://github.com/MKorain/FluentValidation.AspNetCore.TagHelpers)**
— an ASP.NET Core tag helper that generates jQuery Unobtrusive Validation attributes
directly from FluentValidation rules, so client-side validation stops drifting from the
server-side rules it mirrors. Handles nested complex types and caches validator
descriptors. MIT licensed.

---

**Open to backend and architecture work — permanent or consulting.**
Reach me at [lazzez22@gmail.com](mailto:lazzez22@gmail.com)
