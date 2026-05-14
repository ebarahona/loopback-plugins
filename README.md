# @ebarahona/loopback-* plugin portfolio

Umbrella docs for the LoopBack 4 plugin ecosystem under the
`@ebarahona/loopback-*` npm scope.

See [ROADMAP.md](./ROADMAP.md) for current state, near-term work,
mid-term plans, and longer-term vision.

## Plugins

- [`@ebarahona/loopback-connector-mongodb`](https://github.com/ebarahona/loopback-connector-mongodb) — published, v1.0.0
- [`@ebarahona/loopback-transport-core`](https://github.com/ebarahona/loopback-transport-core) — published `v1.0.0`, `v1.1.0` ready (pluggable `HandlerDiscoverer` / `DiscoveryService` extension points)
- `@ebarahona/loopback-graphql` — planned (fills the unmaintained `@loopback/graphql` gap)

Each plugin is enterprise-grade by default: full CI matrix, release-please
release automation, typed errors, stability tags, TypeDoc docs site, hooks,
CodeQL, Scorecard, Dependabot. The shared infrastructure is described in
ROADMAP.md under "Cross-cutting infrastructure."

## Contributing

Each plugin has its own `CONTRIBUTING.md`. The workflow is uniform across
the portfolio: Conventional Commits, DCO sign-off, release-please, npm
publish via OIDC or NPM_TOKEN.
