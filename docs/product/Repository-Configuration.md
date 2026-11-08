# Repository Configuration

## Authorization event receiver

The organization workflow evaluates technical evidence directly. Repository setup does not
require an authorization event receiver or approval-comment configuration.
See [Refactor Authorization](Refactor-Authorization.md) for the owner's role and retained proof.

Each enrolled repository commits three fixed inputs. The contract establishes supported source
scope; review evidence describes the actual change; characterization declares executable proof.
Neither evidence file is a waiver.

## Contract examples

These fictional examples use production root `src` and real high-risk source files that a prepared
application would contain. Change the paths to your repository's actual governed responsibilities.
The maximum is fixed at 10 and adapter identifiers are closed, not user-defined command names.

### Python contract

```toml
schema_version = "1.0"
language = "python"
production_paths = ["src"]
high_risk_paths = ["src/application/pricing.py"]

[[gates]]
adapter = "python.c901-touched.v1"
paths = ["src"]

[[gates]]
adapter = "python.import-linter.v1"
paths = ["src"]

[[gates]]
adapter = "python.mypy-strict.v1"
paths = ["src"]

[[gates]]
adapter = "python.pytest.v1"
paths = ["src"]

[[gates]]
adapter = "python.ruff-lint.v1"
paths = ["src"]

[complexity]
adapter = "python.c901-touched.v1"
maximum = 10
```

Place governed Python source inside at least one importable package directory with `__init__.py`
under `production_paths` (such as `src/order_service/__init__.py`) so `import-linter` discovers the
root package. Keep `__init__.py` empty or docstring-only unless unit tests execute its statements
past line 1.

### TypeScript contract

```toml
schema_version = "1.0"
language = "typescript"
production_paths = ["src"]
high_risk_paths = ["src/domain/pricing.ts"]

[[gates]]
adapter = "typescript.c901-equivalent-touched.v1"
paths = ["src"]

[[gates]]
adapter = "typescript.import-boundaries.v1"
paths = ["src"]

[complexity]
adapter = "typescript.c901-equivalent-touched.v1"
maximum = 10
```

The TypeScript contract lists two policy adapters; the hosted profile also provides its fixed
format, lint, type, test, build, and asset-validation responsibilities.

The same profile admits `.js` and `.jsx` source alongside `.ts`, `.tsx`, `.mts` and `.cts`.
JavaScript remains subject to the fixed strict type checker (`allowJs` and `checkJs`), lint,
complexity, import boundaries, runtime test coverage, build evidence and behavior obligations.
A separate TypeScript rewrite or application build step is not required by source admission;
the Gate still performs its own fixed quality commands. Keep browser source in production scope.
Explicit JavaScript imports resolve to a committed matching file first; the existing TypeScript
extension rewrite applies when that file is absent.

HTML assets must be UTF-8 with explicit balanced element tags. Executable code belongs in external
scripts that resolve to assessed production source in the same repository. Inline scripts, event
handlers, executable URLs, embedded documents, foreign markup and base-URL redirection fail the
bounded asset validator. This is an admission boundary, not full browser rendering or security
certification. ICO admission validates a contiguous directory of PNG-backed images, their bounds,
dimensions and PNG checksums; other ICO encodings remain unsupported. Asset receipts bind exact
committed bytes and do not replace behavior evidence.

### Mixed contract

```toml
schema_version = "1.1"
languages = ["python", "typescript"]
production_paths = ["src"]
high_risk_paths = ["src/application/pricing.py", "src/domain/pricing.ts"]

[[gates]]
adapter = "python.c901-touched.v1"
paths = ["src"]

[[gates]]
adapter = "python.import-linter.v1"
paths = ["src"]

[[gates]]
adapter = "python.mypy-strict.v1"
paths = ["src"]

[[gates]]
adapter = "python.pytest.v1"
paths = ["src"]

[[gates]]
adapter = "python.ruff-lint.v1"
paths = ["src"]

[[gates]]
adapter = "typescript.c901-equivalent-touched.v1"
paths = ["src"]

[[gates]]
adapter = "typescript.import-boundaries.v1"
paths = ["src"]

[complexity]
maximum = 10
```

Schema `1.1` uses `languages` and a `complexity` table containing only `maximum`; the language
order and complete adapter sets must match the fixed parser. It does not permit arbitrary
combinations of languages. Use `schema_version = "1.1"` only when tracked production source files
exist for both Python and TypeScript/JavaScript; a single-language repository uses
`schema_version = "1.0"` with `language = "python"` or `language = "typescript"`, because the mixed
profile executes both fixed adapter sets and blocks when a declared language has no production
source files.

Mixed adapter scope follows the admitted source language: Python adapters must cover changed
and high-risk `.py`/`.pyi` files; TypeScript adapters must cover `.ts`, `.tsx`, `.mts`, and `.cts`
files. Separate roots, such as Python `src` and TypeScript `web`, may therefore use corresponding
adapter paths. Both old and new paths remain checked for renames and deletions. Files outside
these admitted language suffixes retain the full declared coverage obligations; this does not
exempt unsupported source or production assets. Candidate scope narrowing still blocks.

The fixed TypeScript test manifest admits files under `tests` ending in `.test.js`, `.test.mjs`,
`.test.cjs`, `.test.ts`, `.test.mts`, or `.test.cts`; Python admits `.py` and `.pyi` tests.
Mixed command arguments select each language's exact source and test manifests. Evidence must
match the executed arguments, including module tests; omitted, substituted, extra, or
other-language test arguments fail binding validation.

## Base authority and supported transitions

The evaluator reads the contract from the immutable **base** commit. Reading a head contract to
detect weakening does not let a pull request redefine its own authority. The fixed policy permits:

- supported expansion to the complete mixed profile with preserved coverage and thresholds;
- removal of an exact high-risk path only when its tracked file is deleted in that diff;
- retirement of one mixed language profile when the corresponding language product is deleted,
  the surviving fixed adapter set is complete, and production roots remain unchanged.

Other threshold relaxation, adapter loss, scope narrowing, exclusions, command overrides, executable
paths, environment controls, and unknown keys fail closed. Moving code outside coverage is not a
way to obtain PASS.

## Architecture layers and module ownership

### Layer classification and dependency direction

Static architecture evaluation classifies each production file by the first matching directory
segment under its declared `production_paths` root:

| Layer | Classified directory segments | Permitted internal target layers |
|---|---|---|
| `domain` | `domain` | `domain` |
| `application` | `application`, `workflow` | `application`, `domain`, unlayered root |
| `infrastructure` (outer) | `infrastructure`, `persistence`, `database`, `external`, `framework`, `package` | `infrastructure`, `application`, `domain`, unlayered root |
| `presentation` (outer) | `presentation`, `cli`, `ui`, `api` | `presentation`, `application`, `domain`, unlayered root |
| Unlayered root | Package root files outside the layer segments (such as `src/order_service/__init__.py` or `src/order_service/main.py`) | `presentation`, `infrastructure`, `application`, `domain`, unlayered root |

Dependencies flow inward without static cycles (`CYCLIC_DEPENDENCY`). `presentation` and
`infrastructure` are peer outer layers and cannot import each other (`DEPENDENCY_INVERSION`);
cross-layer composition wiring belongs in an unlayered package root module.

Python `domain` modules may import only internal `domain` modules and the 21 pure standard-library
modules (`__future__`, `abc`, `collections`, `contextlib`, `copy`, `dataclasses`, `datetime`,
`decimal`, `enum`, `fractions`, `functools`, `heapq`, `itertools`, `math`, `numbers`, `operator`,
`re`, `statistics`, `string`, `types`, `typing`). TypeScript and JavaScript `domain` modules may
import only internal `domain` modules (`FORBIDDEN_DOMAIN_DEPENDENCY` blocks external packages and
`node:*` built-ins). Root `tsconfig.json` files must be valid JSON without `extends`,
`compilerOptions.plugins`, non-string `baseUrl`, or multi-wildcard path aliases.

### New production locations and package ownership

Production directory segments and file stems cannot use vague names (`common`, `helpers`, `misc`,
`stuff`, `utils`). Every newly added production source file requires a `[[module_boundaries]]`
record in `.supportability-review.toml`:

```toml
[[module_boundaries]]
path = "src/order_service/domain/pricing.py"
owner_path = "src/order_service/domain/pricing.py"
basis = "domain"
justification = "Own pure order pricing calculations inside the order_service package."
```

`basis` must be `"domain"` or `"responsibility"`. `owner_path` must share the same first path
segment under `production_paths` (`PARALLEL_PACKAGE` blocks cross-package ownership) and must
already exist at the pull request base commit (`NEW_MODULE_OWNER_NOT_PREEXISTING` blocks pointing
to another file added in the same pull request). When introducing a new top-level package under
`production_paths` (or in a first-application baseline) that has no preexisting file at the base
commit, each new file in that package uses self-ownership (`owner_path = path`).

### Production assets and suppression rules

Non-source files committed under `production_paths` are limited to `.css`, `.html`, `.ico`,
`.json`, `.md`, and `.png`; other non-source files belong outside `production_paths`. Blanket and
region suppressions (`# ruff: noqa`, `# mypy:`, file-level uncoded `# type: ignore`,
`# pragma: no cover`, `/* eslint-disable */`, inline ESLint disables, `// @ts-nocheck`, and
unexplained `// @ts-ignore` or `// @ts-expect-error`) block Gate 7.

## Review evidence

`.supportability-review.toml` uses schema `1.0`. All fixed sections are required:

| Section | Required content |
|---|---|
| `behavior` | Intended behavior and proof |
| `characterization` | Captured behavior and proof |
| `separation_of_concerns` | Before/after explanation and exact changed-boundary records |
| `architecture` | Dependency-direction explanation and reviewed paths |
| `responsibility_boundary` | Path, what it owns, and what it does not own |
| `incremental_refactor` | Target and completed step |
| `review_handoff` | Derived summary and remaining-risk markers |
| `human_review` | Naming, cohesion, intended behavior, and reviewability declarations |
| `module_boundaries` | Records for relevant new production locations; an empty array when none apply |

Refresh the evidence for each full-process change. Boundary records use exact `path`, `kind`, and
`symbol` identities from the changed inventory, with `before` and `after` text. At most one
`(path, "module", path)` entry is permitted per file; keep top-level module imports and constants
contiguous at the top of a file so Gate 6 derives a single module hunk. Missing, duplicate,
invented, or incomplete records block. Nonblank text is structural evidence, not independently
judged truth or automatically authenticated owner authorship.

The handoff fields use the fixed sentinel:

```toml
[review_handoff]
summary = "DERIVED_FROM_AUTHENTICATED_EVIDENCE"
remaining_risks = ["DERIVED_FROM_AUTHENTICATED_EVIDENCE"]
```

The result composer supplies the actual handoff from authenticated sources; do not replace these
markers with an authored claim of completion.

## Characterization manifest and executable proof

See [Characterization Reference](Characterization-Reference.md#characterization-manifest-and-executable-proof).

## Observed Python APIs and genuine introduction

See [Characterization Reference](Characterization-Reference.md#observed-python-apis-and-genuine-introduction).

### Module responsibility witnesses (schema 4)

See [Characterization Reference](Characterization-Reference.md#module-responsibility-witnesses-schema-4).

### Function-only witnesses (schema 3)

See [Characterization Reference](Characterization-Reference.md#function-only-witnesses-schema-3).

## Oracle-first behavior corrections (schema 5)

See [Characterization Reference](Characterization-Reference.md#oracle-first-behavior-corrections-schema-5).

### First application baseline

See [Characterization Reference](Characterization-Reference.md#first-application-baseline).
