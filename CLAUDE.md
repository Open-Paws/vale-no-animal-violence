# vale-no-animal-violence

Vale style package for detecting speciesist language in prose. Part of the Open Paws speciesist language detection suite. Distributes two style packages: `NoAnimalViolence` (primary) and `Speciesism` (legacy).

## Quick Start

```bash
# Install Vale
brew install vale   # or see https://vale.sh/docs/install

# Test locally with the included config
vale --config .vale.ini some-file.md
```

Users install via `.vale.ini`:
```ini
Packages = https://github.com/Open-Paws/vale-no-animal-violence/releases/latest/download/NoAnimalViolence.zip
```
Then run `vale sync`.

## Architecture

Each style package is a directory of Vale YAML rule files plus a `meta.json`. Vale loads them via `BasedOnStyles` in `.vale.ini`. The two packages (`NoAnimalViolence` and `Speciesism`) contain overlapping but distinct rule sets.

## Key Files

| File | Description |
|------|-------------|
| `NoAnimalViolence/AnimalIdioms.yml` | Violent animal idiom detection rules |
| `NoAnimalViolence/AnimalMetaphors.yml` | Animal-as-object metaphor detection rules |
| `NoAnimalViolence/TechTerminology.yml` | Speciesist technical term detection rules |
| `NoAnimalViolence/meta.json` | Package metadata (name, homepage, source) |
| `Speciesism/AnimalIdioms.yml` | Legacy package: animal idiom rules |
| `Speciesism/AnimalMetaphors.yml` | Legacy package: metaphor rules |
| `Speciesism/TechTerminology.yml` | Legacy package: tech term rules |
| `Speciesism/meta.json` | Legacy package metadata |
| `.vale.ini` | Local test config |

## Development Commands

```bash
# Lint a file with the local styles
vale --config .vale.ini README.md

# Package for release (zip the style directory)
zip -r NoAnimalViolence.zip NoAnimalViolence/
```

## Organizational Context

**Strategic role (Lever 1 + Lever 3):** Vale style package for prose/docs linting — the primary speciesist language check for strategy documents, README files, and written documentation across the org. Upstream dependency: `no-animal-violence` (canonical rule dictionary).

**Current org priorities relevant to this repo:**
- Should be integrated into `open-paws-strategy` repo CI and `open-paws-platform` documentation workflows. See `ecosystem/integration-todos.md` §27a.
- Bootcamp integration: include Vale + this package in bootcamp writing setup instructions.
- Suite maintenance has **no named owner** as of 2026-04-02. The `feat/add-industry-euphemisms-rule` branch exists in remote — evaluate and merge if ready.
- `NoAnimalViolence` is the primary package; `Speciesism` is legacy. New rules go in `NoAnimalViolence` only.

**Decisions affecting this repo:**
- 2026-04-01: Rule content should stay in sync with `no-animal-violence` canonical dictionary.
- 2026-03-25: All `.md` files in org repos must pass speciesist language checks — Vale is the primary tool for prose.

## Related Repos

- [no-animal-violence](https://github.com/Open-Paws/no-animal-violence) — Canonical rule dictionary (upstream dependency, includes `vale/Speciesism/` package)
- [eslint-plugin-no-animal-violence](https://github.com/Open-Paws/eslint-plugin-no-animal-violence) — ESLint plugin for JS/TS
- [semgrep-rules-no-animal-violence](https://github.com/Open-Paws/semgrep-rules-no-animal-violence) — Multi-language CI scanning

## Development Standards

### 10-Point Review Checklist (ranked by AI violation frequency)

1. **DRY** — `NoAnimalViolence` and `Speciesism` packages overlap. New rules go in `NoAnimalViolence` only. Don't duplicate across packages.
2. **Deep modules** — Each YAML rule file covers one category. Keep `AnimalIdioms`, `AnimalMetaphors`, and `TechTerminology` separate.
3. **Single responsibility** — One rule entry = one pattern with one message and one suggestion.
4. **Error handling** — Run `vale --config .vale.ini README.md` locally before releasing. Vale gives unhelpful errors for malformed YAML.
5. **Information hiding** — The `meta.json` is the package contract. Keep it accurate and consistent.
6. **Ubiquitous language** — "farmed animal" not "livestock," "factory farm" not "farm." Never euphemisms.
7. **Design for change** — Adding a rule requires only one YAML block. No structural changes needed.
8. **Legacy velocity** — Before modifying `Speciesism/`, check whether consumers depend on it. New work goes in `NoAnimalViolence/`.
9. **Over-patterning** — Three rule files per package is the right structure. Don't subdivide further.
10. **Test quality** — Test new rules locally with `vale --config .vale.ini some-file.md` before packaging and releasing.

### Quality Gates

- **Local lint test**: `vale --config .vale.ini README.md`
- **Package release**: `zip -r NoAnimalViolence.zip NoAnimalViolence/` — verify zip contents before release.
- **Desloppify**: `desloppify scan --path .` — minimum score ≥85.
- **Two-failure rule**: After two failed fixes on the same problem, stop and restart.

### Testing Methodology

- Test new rules against a sample document with both flagged and clean phrases.
- Three questions per rule: (1) Does Vale flag the target phrase? (2) Does it provide a useful suggestion? (3) Does it avoid false positives in technical contexts?

### Seven Concerns — Repo-Specific Notes

1. **Testing** — Manual testing only. Automated Vale rule testing is a known gap.
2. **Security** — Low risk. Static YAML files.
3. **Privacy** — Not applicable.
4. **Cost optimization** — Static files, no compute cost. Vale is free and open source.
5. **Advocacy domain** — Movement-standard language throughout. "farmed animal," "factory farm," "slaughterhouse." Never euphemisms.
6. **Accessibility** — Rule messages must be clear and actionable for writers unfamiliar with advocacy context.
7. **Emotional safety** — Suggestions guide writers toward better language without requiring engagement with graphic content.

### Structured Coding Reference

For tool-specific AI coding instructions (Claude Code rules, Cursor MDC, Copilot, Windsurf, etc.), copy the corresponding directory from `structured-coding-with-ai` into this project root.

## MCP Integrations (live 2026-04-09)

The broader NAV suite this package belongs to now has live MCP infrastructure:

- **mcp-server-nav-language** — Pure regex MCP server (sub-10ms) covering the same pattern space as the Vale rules but for real-time agent use. This Vale package handles prose linting at write time; the MCP server handles runtime enforcement across the agent swarm via Gary MCP hub Phase 3.
- **lbr8-mcp-constraints** — Bundles 12 offline NAV patterns from this suite as `StaticConstraintSource` middleware for any MCP tool handler.
- **mcp-server-aha-evaluation** — Uses NAV rules as Stage 1 of a two-stage content evaluation pipeline.
- **Audit-to-dispatch (decision #37, 2026-04-11)** — NAV violations found during ecosystem audits now auto-dispatch as agent fix tasks.

## Decisions Reviewed

Last reviewed: 2026-04-11 (decisions #37 audit-to-dispatch, mcp-server-nav-language live)
