# `@ebarahona/loopback-*` Plugin Roadmap

Last updated: 2026-05-14

This document tracks the modern LoopBack 4 plugin ecosystem published under the `@ebarahona/loopback-*` npm scope. Each package is enterprise-grade by default (CI, release automation, typed errors, stability tags, docs) and composable. Pick the pieces you need.

## Vision

LoopBack 4 has the right primitives (IoC, DI, components, lifecycle observers) but the ecosystem around it is thin, partly stale, and weak on modern patterns. This portfolio fills the most important gaps with packages that:

- Ship on the current major of every upstream dep (`mongodb` 7.x, `rxjs` 7.x, Node 20.19+).
- Use TypeScript-first APIs with strict stability tags (`@public`, `@experimental`, `@internal`).
- Are set-and-forget: low-maintenance, fully automated CI / release, no manual hand-holding between releases.
- Compose via standard LB4 DI rather than ad-hoc glue.

## Plugin status

| Package | Latest | State | Repo |
|---|---|---|---|
| `@ebarahona/loopback-connector-mongodb` | `1.0.0` | Published | https://github.com/ebarahona/loopback-connector-mongodb |
| `@ebarahona/loopback-transport-core` | `1.0.0` ready | Built, awaiting first publish | https://github.com/ebarahona/loopback-transport-core |
| `@ebarahona/loopback-graphql` | — | Planned | — |

## Near-term (next 1–2 releases per plugin)

### `loopback-transport-core`
- Publish `v1.0.0` to npm (workflows already wired, NPM_TOKEN already set).
- **CloudEvents serializer** (`CloudEventsSerializer` / `CloudEventsDeserializer`, `@experimental`). Wraps payloads in the CNCF CloudEvents 1.0 envelope so transport-core apps interoperate with Knative, Dapr, Azure Event Grid, AWS EventBridge, NATS JetStream out of the box. Slots into the existing `Serializer` / `Deserializer` interface. Peer-dep on `cloudevents` npm package.
- **First concrete transport adapter** — likely Kafka (`kafkajs`) or NATS, both with native CloudEvents support. Uses the `new-transport-adapter` skill we authored.
- Multi-tenant routing example documented in README.

### `loopback-connector-mongodb`
- Tune the leak-detection thresholds and benchmark baselines listed in `HELP_WANTED.md`.
- Publish a baseline `bench/baseline.json` so CI can detect regressions.
- First-class Atlas Vector Search index management methods on `MongoService` (`createSearchIndex`, `listSearchIndexes`, `updateSearchIndex`, `dropSearchIndex`). Currently reachable via `getCollection().createSearchIndex()` as documented.

## Mid-term (next 3–6 months)

### `loopback-transport-core`
- Concrete adapters: `loopback-transport-kafka`, `loopback-transport-amqp`, `loopback-transport-mqtt`, `loopback-transport-nats`, `loopback-transport-redis`. Each as a separate sibling package, peer-dep on the broker library.
- Subscription support over WebSocket / SSE for browser clients.
- Schema registry integration helpers (Confluent, Azure Schema Registry) on top of the CloudEvents `dataschema` attribute.
- W3C Trace Context / OpenTelemetry integration via CloudEvents extensions.

### `loopback-connector-mongodb`
- `MongoConfigError`-style ergonomic config field for Atlas Vector Search settings (top-level `vectorSearch` config block that maps to `clientOptions`).
- Documented patterns for change-stream consumer apps.

### `loopback-graphql` (NEW)

LoopBack 4 has no actively-maintained modern GraphQL solution. `@loopback/graphql` is stuck on TypeGraphQL 1.x and unmaintained since 2023. This package fills that gap.

- Built on `graphql-yoga` v5 (or `@apollo/server` v4 — picked at design time based on the LB4 integration story).
- Schema-first or code-first via decorators on LB4 controllers.
- Full LB4 DI for resolvers (`@inject` into resolver classes, lifecycle observers control schema (re)load).
- Subscriptions delegated to `loopback-transport-core` adapters (Redis Pub/Sub or NATS), keeping the GraphQL plugin transport-agnostic.
- TypeScript-first: `@ObjectType` / `@Resolver` / `@Query` / `@Mutation` / `@Subscription` with full type inference, no runtime schema string parsing.
- Apollo Federation v2 compatible from day one.

This is the most ambitious near-future plugin and the most likely to drive ecosystem adoption.

## Long-term (6+ months / aspirational)

- Performance baseline and regression CI across all plugins.
- Reference apps demonstrating cross-package composition (mongo + transport-core + graphql).
- Multi-cloud event flow examples (Knative, EventBridge, Event Grid).
- A consolidated `@ebarahona/loopback-toolkit` umbrella package that re-exports the most-used helpers from each plugin.

## Cross-cutting infrastructure

The plugins share a user-level skill ecosystem at `~/.claude/skills/`. Authored once, copied into each plugin's `.claude/skills/` during enterprise rollout.

| Skill | Purpose |
|---|---|
| `loopback-core` | LoopBack's official upstream framework reference (IoC, DI, components, observers, interceptors) |
| `lb4-plugin-review` | Adaptive code review with `{SKILL_DOMAIN}` template, subsumes per-plugin domain reviews |
| `lb4-style-check` | Mechanical STYLE_GUIDE.md compliance check |
| `lb4-public-api-audit` | Stability tag + internal-type-leak detection |
| `pre-pr-check` | Pre-PR readiness gate (lint, build, test, commitlint, DCO) |
| `conventional-commit` | Conventional Commits message author with scope derived from `src/` layout |
| `lb4-plugin-enterprise-rollout` | Wave-based multi-agent enterprise scaffolding orchestrator (explicit-invoke only) |

Each plugin also ships a `STYLE_GUIDE.md` (LoopBack 4 plugin style guide, adopts the Google TypeScript Style Guide as base) and `AGENTS.md` (cross-agent conventions per https://agents.md/).

## Status legend

- **Published**: live on npm.
- **Built, awaiting first publish**: all toolchain gates green; commits / release-please trigger pending.
- **Planned**: scoped but implementation not yet started.
- **Experimental** (in-plugin tag, not a portfolio state): shipped behind `@experimental` JSDoc; API may change before promotion to `@public`.

## How to contribute

Each plugin has its own `CONTRIBUTING.md`. The workflow is uniform across the portfolio:

- Conventional Commits + DCO sign-off (`git commit -s`).
- `npm run lint && npm run build && npm test` must pass before any PR (the `pre-pr-check` skill bundles this).
- Releases via `release-please` — no manual `npm publish`, no manual tagging.
- CI matrix: Node 20.19 / 22 / 24 on `ubuntu-latest` plus `macos-latest` on 20.19 and 24.
- Branch protection enforced on `main`: PR required, CI required, linear history.
- Security: `CodeQL`, `OSSF Scorecard`, `Dependabot`, `lychee` link checker, `typos`, `size-limit` budget all enforced via GitHub Actions.

## Out of scope (for this portfolio)

- Stripe integration: use the existing `stripe-best-practices` skill, or `stripe-node` directly.
- Generic HTTP server: use `@loopback/rest`.
- Authentication: use `@loopback/security` or `@loopback/authentication`.
- ORM for non-Mongo databases: use the corresponding `loopback-connector-*` (Postgres, MySQL, etc.) until or unless those packages also become unmaintained.

## Decision log

- **2026-05-13** — Published `loopback-connector-mongodb@1.0.0`. First package in the portfolio.
- **2026-05-14** — Built `loopback-transport-core@1.0.0` to publish-ready state. Decided CloudEvents serializer is in-scope for transport-core; GraphQL is out-of-scope (separate sibling plugin to come).
- **2026-05-14** — Adopted user-level canonical skills at `~/.claude/skills/`. Per-plugin `.claude/skills/` syncs from canonical; domain-specific reviews subsumed by the `lb4-plugin-review` template with `{SKILL_DOMAIN}`.
