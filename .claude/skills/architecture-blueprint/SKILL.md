---
name: architecture-blueprint
description: Design or review software architecture for new and existing applications, substantial features, service and module boundaries, APIs, data stores, security, reliability, scaling, and migrations. Use when choosing architecture or technology, planning a major feature or refactor, or diagnosing structural coupling; not for routine small code edits.
---

# Architecture Blueprint

Design the best-fit architecture for the actual product and constraints—not the most fashionable or complicated one. Make the important boundaries, data flows, trade-offs, failure modes, and build path explicit. Prefer the simplest design that meets stated quality goals and can evolve without a rewrite.

## Process

1. **Classify the task.** Determine whether this is a greenfield system, a feature, an architecture review, or a migration. For an existing codebase, inspect its entrypoints, module/dependency graph, persistence, external integrations, tests, deployment, and current conventions before proposing changes. Use targeted searches and files; do not ingest the whole repository.
2. **Establish decision drivers.** Extract the users and core workflows, hard constraints, team/runtime/deployment limits, data sensitivity, and the few quality attributes that can change the design: availability, latency, consistency, scale, privacy/security, operability, delivery speed, and cost. Make measurable targets only when evidence exists. If a missing fact would change the architecture materially, ask up to three concise questions together; otherwise state assumptions and continue.
3. **Map the system.** Identify responsibilities, trust boundaries, data ownership/source of truth, external dependencies, and the critical read/write flows. For existing systems, distinguish observed facts from proposed improvements. Do not recommend a rewrite without concrete evidence that incremental change cannot meet the goal.
4. **Choose proportionately.** Compare at most 2–3 viable options for decisions with meaningful long-term impact. State the chosen option, why it fits, what it costs, and what evidence would trigger revisiting it. Default to a modular monolith for one product/team/deployment unless independent scaling, release cadence, security isolation, ownership, or resilience needs justify distributed services.
5. **Design the seams.** Define module/service responsibilities and allowed dependencies; API/event contracts; data ownership, consistency and migration strategy; authentication/authorization and secret boundaries; timeout/retry/idempotency behavior; observability; testing; deployment, rollback, backup and recovery. Detail only areas relevant to the system's risks.
6. **Deliver an actionable blueprint.** Give the user a decision and a path to implement it. If asked to code, make a small staged change that respects the approved boundaries; do not start a broad refactor just because the design suggests one.

## Architecture quality bar

- **Fit before scale.** Do not invent traffic, availability, or team-size requirements. Avoid microservices, Kubernetes, event sourcing, CQRS, extra queues/caches, and framework changes unless a stated need outweighs their operational and cognitive cost.
- **Boundaries must carry meaning.** Organize by capabilities/domain where that clarifies ownership. Keep high-change areas isolated; avoid circular dependencies, shared mutable state, and “common” modules that become a dumping ground. State who owns each important piece of data.
- **Make failure behavior explicit.** For network calls and background work, define timeouts, bounded retries/backoff, duplicate handling, idempotency, dead-letter or recovery behavior, and user-visible failure states as appropriate. Never retry non-idempotent operations blindly.
- **Design data changes for reality.** Specify source of truth, transaction boundaries, consistency expectations, retention and backup needs, and an expand/migrate/contract sequence for risky schema changes. Preserve existing user data; ask before destructive migration or deletion.
- **Treat security and operations as architecture.** Mark trust boundaries and sensitive data. Include least privilege, input validation, secret handling, auditability, and threat cases proportionate to risk. Name the logs/metrics/alerts needed to detect failures and the rollback path for a release.
- **Prove the design.** Define the highest-risk assumptions and how to validate them with a prototype, load test, migration rehearsal, failure injection, or representative integration test. Do not use a diagram as a substitute for an executable verification plan.
- **Respect the current system.** Prefer incremental migration with compatibility and rollback over big-bang replacement. Preserve working behavior and established conventions unless evidence or the user justifies a change.

## Default response shape

Adapt this to the request; do not force a full design document for a small question.

1. **Decision:** one paragraph with the proposed shape and the core reason.
2. **Diagram:** one small Mermaid context/container/sequence diagram when it improves clarity; omit for trivial systems.
3. **Boundaries:** concise component responsibilities, dependencies, and data ownership.
4. **Critical behavior:** important API/data flow, consistency, security, and failure choices.
5. **Trade-offs and assumptions:** only decisions that materially affect implementation; separate facts from assumptions.
6. **Delivery plan:** ordered implementation slices with a verification step and rollback/migration note where relevant.

For an explicit architecture document, include an ADR for consequential decisions using `templates/adr.md`. Keep the main explanation concise; add detail only where a risk or unresolved choice warrants it. Answer in the user's language, explain jargon on first use, and do not present guesses as requirements.

## Token and change discipline

- Start with the decision drivers; do not produce a generic checklist or tour of every architecture pattern.
- Inspect only relevant files and logs. Search for call sites and data flows before proposing a boundary. Reuse prior findings rather than re-reading them.
- Research current vendor/API facts only when they can alter a decision; use authoritative sources and avoid broad framework comparisons unless requested.
- Use one diagram at most by default and compare alternatives only for consequential choices. Avoid duplicating the diagram in prose.
- Separate **must-have requirements**, **assumptions**, **decisions**, and **future options**. State what is intentionally not being built.
- Keep routine code changes out of scope. Do not modify files, migrate data, introduce infrastructure, commit, publish, or deploy unless the user asked for implementation and the action is within that request.
