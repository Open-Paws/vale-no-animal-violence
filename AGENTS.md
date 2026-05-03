# AGENTS.md — vale-no-animal-violence

## Summary

`vale-no-animal-violence` distributes two [Vale](https://vale.sh) prose-linting style packages — `NoAnimalViolence` (primary, actively maintained) and `Speciesism` (legacy, kept for backwards compatibility) — that detect speciesist language in documentation, README files, and written prose. It is the prose layer of the Open Paws speciesist language detection suite, consuming patterns from the [no-animal-violence](https://github.com/Open-Paws/no-animal-violence) canonical rule dictionary and translating them into Vale YAML substitution rules. The repository has no runtime code: it is a collection of static YAML rule files packaged as a GitHub Releases ZIP artifact and installed by end users via `vale sync`.

---

## Status

**Active Development** — receiving rule updates and CI fixes as of April 2026. No named owner as of 2026-04-02. The `feat/add-industry-euphemisms-rule` remote branch may contain pending work worth evaluating.

**Change implications:**
- Rule changes take effect for consumers only after a new GitHub Release is published and users run `vale sync`.
- The `.vale.ini` in this repo points at `Speciesism` for local testing — it is a developer convenience config, not the canonical consumer config.
- Most rule files under `NoAnimalViolence/` are auto-generated. Editing them directly will be overwritten on the next generation cycle.

---

## Key Files

| Path | Purpose |
|------|---------|
| `NoAnimalViolence/AnimalIdioms.yml` | Primary rule: violent animal idioms (auto-generated) |
| `NoAnimalViolence/AnimalMetaphors.yml` | Primary rule: animal-as-object metaphors (auto-generated) |
| `NoAnimalViolence/IndustryEuphemisms.yml` | Primary rule: animal agriculture euphemisms (hand-maintained) |
| `NoAnimalViolence/TechTerminology.yml` | Primary rule: speciesist technical terms (auto-generated) |
| `NoAnimalViolence/meta.json` | Package metadata: name, homepage, source |
| `Speciesism/AnimalIdioms.yml` | Legacy rule: animal idioms |
| `Speciesism/AnimalMetaphors.yml` | Legacy rule: animal metaphors |
| `Speciesism/TechTerminology.yml` | Legacy rule: tech terms |
| `Speciesism/meta.json` | Legacy package metadata |
| `.vale.ini` | Local developer test config (not for distribution) |
| `.pre-commit-config.yaml` | Pre-commit hooks: yamllint + no-animal-violence check |
| `.github/workflows/no-animal-violence.yml` | PR check: runs the no-animal-violence GitHub Action |
| `.github/workflows/auto-merge.yml` | Bot-driven auto-merge workflow |

---

## Install and Test Commands

```bash
# Install Vale
brew install vale   # macOS; see https://vale.sh/docs/install for other platforms

# Lint the repo's own README with the local developer config
vale --config .vale.ini README.md

# Lint any document
vale --config .vale.ini path/to/file.md

# Package for release (run from repo root)
zip -r NoAnimalViolence.zip NoAnimalViolence/
zip -r Speciesism.zip Speciesism/

# Validate YAML syntax
yamllint NoAnimalViolence/
yamllint Speciesism/

# Run pre-commit hooks locally
pre-commit run --all-files
```

There is no automated test suite. Rule validation is manual: lint a document containing both flagged and clean phrases and confirm that Vale reports only the expected findings.

---

## Architecture

### Vale rule format

Each rule file is a YAML document following the Vale `substitution` extension format:

```yaml
extends: substitution
message: "Consider using '%s' instead of '%s'. <reason>"
level: warning | suggestion
ignorecase: true
swap:
  flagged phrase: suggested replacement
```

Vale loads rules by looking for a style directory (e.g., `NoAnimalViolence/`) on the `StylesPath`, then applying every `.yml` file in that directory to the target prose. Rules are identified in output as `StyleName.RuleName` (e.g., `NoAnimalViolence.AnimalIdioms`).

### Two packages

| Package | Status | Rule files | Notes |
|---------|--------|-----------|-------|
| `NoAnimalViolence` | Primary | AnimalIdioms, AnimalMetaphors, IndustryEuphemisms, TechTerminology | New rules go here only |
| `Speciesism` | Legacy | AnimalIdioms, AnimalMetaphors, TechTerminology | No IndustryEuphemisms; kept for backwards compat |

### Auto-generation pipeline

Files marked `# AUTO-GENERATED from project-compassionate-code. Do not edit directly.` are produced by [project-compassionate-code](https://github.com/Open-Paws/project-compassionate-code), which consumes the [no-animal-violence](https://github.com/Open-Paws/no-animal-violence) canonical dictionary and outputs Vale-formatted YAML. The generation cycle is not automated in this repo's CI — it is a manual sync step.

### Distribution model

Consumers reference the package via a GitHub Releases ZIP URL in their `.vale.ini`:

```ini
Packages = https://github.com/Open-Paws/vale-no-animal-violence/releases/latest/download/NoAnimalViolence.zip
```

Running `vale sync` downloads and unpacks the ZIP into the consumer's `StylesPath`. There is no package registry (e.g., Vale Hub) involved — the release artifact is the distribution mechanism.

---

## Integration Points

| System | How it connects |
|--------|----------------|
| [no-animal-violence](https://github.com/Open-Paws/no-animal-violence) | Upstream canonical rule source. Auto-generated files in this repo track it. |
| [project-compassionate-code](https://github.com/Open-Paws/project-compassionate-code) | Generation pipeline that produces the auto-generated rule files. |
| [no-animal-violence-action](https://github.com/Open-Paws/no-animal-violence-action) | GitHub Action used in this repo's own CI to check PR content. |
| [no-animal-violence-pre-commit](https://github.com/Open-Paws/no-animal-violence-pre-commit) | Pre-commit hook used in this repo's `.pre-commit-config.yaml`. |
| mcp-server-nav-language | Runtime MCP server (live 2026-04-09) covering the same pattern space for real-time agent enforcement. This Vale package handles write-time prose linting; the MCP server handles runtime enforcement. |
| context (planned) | Per CLAUDE.md §27a, this package should be integrated into the strategy repo's CI for document linting. |
| open-paws-platform (planned) | Documentation workflow integration is a pending TODO. |

---

## Safe vs. Risky Changes

### Safe

- Adding a new swap entry to `NoAnimalViolence/IndustryEuphemisms.yml` (the only hand-maintained rule file in the primary package).
- Updating `NoAnimalViolence/meta.json` metadata fields (name, description, homepage).
- Editing CI workflow files (`.github/workflows/`).
- Updating `.pre-commit-config.yaml` dependency versions.

### Requires upstream coordination

- Any change to auto-generated rule files (`AnimalIdioms.yml`, `AnimalMetaphors.yml`, `TechTerminology.yml` in either package) — these are owned by `project-compassionate-code` and `no-animal-violence`. Submit changes there first, then regenerate.

### Risky

- Restructuring the `NoAnimalViolence/` directory layout — any change to the directory name or file names breaks all consumers using the `releases/latest` ZIP URL until they re-sync.
- Modifying `Speciesism/` rules — unknown downstream consumers may depend on the exact rule names and behavior. Check before changing.
- Publishing a release with malformed YAML — Vale will silently skip or error on bad rule files; consumers will lose coverage with no clear error message. Always run `yamllint` before releasing.

---

## TODOs (from CLAUDE.md, as of 2026-04-11)

- Evaluate and potentially merge the `feat/add-industry-euphemisms-rule` remote branch.
- Integrate into `context` repo CI for document linting (issue §27a).
- Integrate into `open-paws-platform` documentation workflows.
- Add bootcamp setup instructions referencing this package.
- Establish a named owner/maintainer for the suite.
- Address the automated testing gap — currently all rule validation is manual.
- Automate the sync from `no-animal-violence` canonical dictionary to keep auto-generated files current.
