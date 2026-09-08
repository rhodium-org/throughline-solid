# throughline-solid

The **SOLID principles** of object-oriented design expressed as a
[throughline](https://pypi.org/project/throughline/) **source** — a standalone,
grounded requirements graph that a consuming project composes with
[throughline-compose](https://github.com/rhodium-org/throughline-compose).

This repository holds no code. It is a directory of small YAML items with permanent
UIDs, validated by `tl check`. Consumers import it under a namespace and reference
its principles as `solid:UR-0001` or its rules as `solid:SR-0007`.

It is a **design** concern source. Compose it orthogonally alongside a language source
(e.g. `throughline-java`, `throughline-python`) and a runtime concern source (e.g.
`throughline-backend`): the language source governs how the code is written, the
runtime source governs how a service is configured and run, and this source governs
how modules divide responsibility and depend on one another.

## Why the rules are written the way they are

A principle stated as a slogan ("a class should have one reason to change") does not
change what gets built. Each rule here therefore states three things:

- **what to do**, in terms concrete enough to act on while writing code;
- **how to check it** — a `Check.` paragraph naming the observable that marks a
  violation (an import, a type test, a degenerate override, a diff that touched more
  than the new file);
- **why**, in a `rationale`, so that a consumer can tell when the rule does not apply.

A consumer that links its own requirement to `solid:SR-0021` with `satisfies` inherits
a test it can run against its code, not just a name to invoke.

## What's in the graph

<!-- tl:count type == 'user_requirement' -->
6
<!-- tl:end --> principles as `user_requirement`s, each `derives_from` the root
intent, and
<!-- tl:count type == 'system_requirement' -->
43
<!-- tl:end --> concrete rules as `system_requirement`s, each `implements` its
principle, published to [`docs/spec.md`](docs/spec.md):

| UID | Principle | Tag |
|---|---|---|
| `UR-0001` | Single Responsibility Principle | `SRP` |
| `UR-0002` | Open-Closed Principle | `OCP` |
| `UR-0003` | Liskov Substitution Principle | `LSP` |
| `UR-0004` | Interface Segregation Principle | `ISP` |
| `UR-0005` | Dependency Inversion Principle | `DIP` |
| `UR-0006` | Applying the principles together | `APPLY` |

- `INT-0001` — the root intent (software stays cheap and safe to change),
  `normative: false`.
- Every item carries `attrs.principle` (the tag above) and `attrs.source_ref` (the
  publication and chapter it is drawn from).

The counts above are rendered from the live graph by the `tl:count` directive, so
they cannot drift.

## Status of the items

Every item was authored by an AI agent from the cited publications and carries
`attrs.origin: ai`. Items enter at `proposed` and stay there until a human ratifies
them with `tl ratify`; an unratified item is still a valid link target, but its
authority is that of a proposal. See the `by_status` counts in the
[iddn.uk catalogue](https://iddn.uk) for the current state.

## Editions — dated tags

A material revision is cut as a dated tag on this repo (e.g. `v2026-09`); a consumer
pins the ref it wants.

## Composing it

```toml
[[sources]]
namespace = "solid"
url = "https://github.com/rhodium-org/throughline-solid"
ref = "v2026-09"
```

Then reference a rule from your own items:

```yaml
links:
- target: solid:SR-0032           # keep high-level policy free of imports from low-level detail
  type: satisfies
```

`tl-compose check` resolves the reference; bare `tl check` fails fast and points you
at `tl-compose`.

## Local checks

```sh
pip install throughline
tl check --strict     # the graph must stay sound
tl docs --check       # docs/spec.md and README.md must match the graph
```

## Provenance

The wording here is original and is Apache-2.0 like the rest of the repository. The
principles are the work of Robert C. Martin (SRP, OCP as named, ISP, DIP, and the
collection), Bertrand Meyer (OCP), and Barbara Liskov with Jeannette Wing (LSP); the
acronym is Michael Feathers's. See [NOTICE](NOTICE). Each item's `attrs.source_ref`
names the publication and chapter it is drawn from.
