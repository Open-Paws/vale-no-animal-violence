# No Animal Violence — Vale Style Package

> **Status: Active Development**
> Part of the [Open Paws](https://openpaws.ai) speciesist language detection suite: [no-animal-violence](https://github.com/Open-Paws/no-animal-violence) · [eslint-plugin](https://github.com/Open-Paws/eslint-plugin-no-animal-violence) · [semgrep-rules](https://github.com/Open-Paws/semgrep-rules-no-animal-violence) · **vale-no-animal-violence** · [pre-commit](https://github.com/Open-Paws/no-animal-violence-pre-commit) · [github-action](https://github.com/Open-Paws/no-animal-violence-action)

A [Vale](https://vale.sh) style package that detects speciesist language in prose and documentation, then suggests clearer, more accurate alternatives. Vale is an open-source, fast, customizable prose linter that runs in CI or locally alongside any text editor.

This package enforces the [no-animal-violence canonical rule dictionary](https://github.com/Open-Paws/no-animal-violence) at the prose layer — catching idioms, metaphors, industry euphemisms, and technical terms that normalize harm toward animals.

---

## Contents

- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [Rules Reference](#rules-reference)
- [CI Integration](#ci-integration)
- [Relationship to the Canonical Rules](#relationship-to-the-canonical-rules)
- [Legacy: Speciesism Package](#legacy-speciesism-package)
- [Contributing](#contributing)
- [Academic Foundation](#academic-foundation)

---

## Installation

### 1. Install Vale

```bash
# macOS
brew install vale

# Linux (via script)
curl -sfL https://install.goreleaser.com/github.com/errata-ai/vale.sh | sh

# Windows (via Scoop)
scoop install vale
```

Full install options: [vale.sh/docs/install](https://vale.sh/docs/install)

### 2. Add the package to `.vale.ini`

```ini
StylesPath = styles
MinAlertLevel = suggestion

Packages = https://github.com/Open-Paws/vale-no-animal-violence/releases/latest/download/NoAnimalViolence.zip

[*]
BasedOnStyles = NoAnimalViolence
```

### 3. Sync packages

```bash
vale sync
```

Vale downloads and unpacks the `NoAnimalViolence` style into your `StylesPath` directory.

---

## Configuration

### Enable all rules (recommended)

```ini
[*]
BasedOnStyles = NoAnimalViolence
```

### Enable specific rules only

```ini
[*]
NoAnimalViolence.AnimalIdioms = YES
NoAnimalViolence.AnimalMetaphors = YES
NoAnimalViolence.IndustryEuphemisms = YES
NoAnimalViolence.TechTerminology = NO
```

### Scope to specific file types

```ini
[*.md]
BasedOnStyles = NoAnimalViolence

[*.txt]
NoAnimalViolence.AnimalIdioms = YES
```

### Combine with other styles

```ini
Packages = https://github.com/Open-Paws/vale-no-animal-violence/releases/latest/download/NoAnimalViolence.zip,
           https://github.com/errata-ai/Google/releases/latest/download/Google.zip

[*]
BasedOnStyles = NoAnimalViolence, Google
```

---

## Usage

### Lint a single file

```bash
vale README.md
```

### Lint an entire directory

```bash
vale docs/
```

### Sample output

```
README.md
 14:5  warning  Consider using 'accomplish two things at once'    NoAnimalViolence.AnimalIdioms
                instead of 'kill two birds with one stone'. This
                phrase normalizes violence toward animals.
 22:1  warning  Consider using 'slaughterhouse' instead of        NoAnimalViolence.IndustryEuphemisms
                'processing plant'. This is an industry euphemism
                that sanitizes animal agriculture practices.
 31:3  suggestion  Consider using 'primary/replica' instead of   NoAnimalViolence.TechTerminology
                   'master/slave'. This technical term has a more
                   precise alternative.
```

---

## Rules Reference

| Rule | Level | Category | Examples flagged |
|------|-------|----------|-----------------|
| `NoAnimalViolence.AnimalIdioms` | warning | Violent idioms | "kill two birds with one stone", "beat a dead horse", "guinea pig", "like lambs to the slaughter" |
| `NoAnimalViolence.AnimalMetaphors` | warning | Animal-as-object metaphors | "cattle vs. pets", "canary in a coal mine", "sacred cow", "cash cow", "scapegoat" |
| `NoAnimalViolence.IndustryEuphemisms` | warning | Animal agriculture euphemisms | "livestock", "processing plant", "gestation crate", "depopulation", "humane slaughter" |
| `NoAnimalViolence.TechTerminology` | suggestion | Technical terms with precise alternatives | "master/slave", "whitelist/blacklist", "grandfathered" |

All rules use the Vale `substitution` extension — each flagged phrase comes with a concrete suggested replacement.

---

## CI Integration

### GitHub Actions (recommended)

Add `.github/workflows/vale.yml` to your repository:

```yaml
name: Vale Prose Lint
on:
  pull_request:
  push:
    branches: [main]

jobs:
  vale:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Run Vale
        uses: errata-ai/vale-action@reviewdog
        with:
          files: '["docs/", "README.md"]'
          reporter: github-pr-review
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

Requires a `.vale.ini` at the repo root configured as shown in [Installation](#installation).

### Pre-commit hook

Add to `.pre-commit-config.yaml`:

```yaml
repos:
  - repo: https://github.com/errata-ai/vale
    rev: v3.4.2
    hooks:
      - id: vale
        files: \.(md|txt|rst)$
```

### GitLab CI

```yaml
vale:
  image: jdkato/vale:latest
  script:
    - vale sync
    - vale docs/ README.md
  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"
```

---

## Relationship to the Canonical Rules

This Vale package is a **downstream consumer** of the [no-animal-violence](https://github.com/Open-Paws/no-animal-violence) canonical rule dictionary. The canonical repo defines the source of truth for which patterns are speciesist and what replacements are appropriate.

Rule files in `NoAnimalViolence/` that carry the comment `# AUTO-GENERATED from project-compassionate-code. Do not edit directly.` are generated from the canonical dictionary via [project-compassionate-code](https://github.com/Open-Paws/project-compassionate-code). Do not edit those files by hand — submit changes upstream to `no-animal-violence` instead.

The `NoAnimalViolence.IndustryEuphemisms` rule is maintained directly in this repo and is not auto-generated.

The broader speciesist language detection suite enforces the same patterns across different layers of the development workflow:

| Layer | Tool |
|-------|------|
| Prose / documentation | **This package (Vale)** |
| JavaScript / TypeScript | [eslint-plugin-no-animal-violence](https://github.com/Open-Paws/eslint-plugin-no-animal-violence) |
| Python, Go, multi-language | [semgrep-rules-no-animal-violence](https://github.com/Open-Paws/semgrep-rules-no-animal-violence) |
| Pre-commit hooks | [no-animal-violence-pre-commit](https://github.com/Open-Paws/no-animal-violence-pre-commit) |
| GitHub Actions | [no-animal-violence-action](https://github.com/Open-Paws/no-animal-violence-action) |
| Real-time agent enforcement | mcp-server-nav-language (MCP hub) |

---

## Legacy: Speciesism Package

This repository also distributes a `Speciesism` style package (in the `Speciesism/` directory). The `Speciesism` package is a legacy predecessor to `NoAnimalViolence`.

- **New projects** should use `NoAnimalViolence`.
- The `Speciesism` package is kept for backwards compatibility with existing consumer configurations.
- New rules are added to `NoAnimalViolence` only.

To use the legacy package:

```ini
Packages = https://github.com/Open-Paws/vale-no-animal-violence/releases/latest/download/Speciesism.zip

[*]
BasedOnStyles = Speciesism
```

---

## Contributing

1. Fork this repository and create a branch.
2. To add or update a rule in `NoAnimalViolence/`:
   - If the pattern is in the canonical no-animal-violence dictionary, submit the change to [no-animal-violence](https://github.com/Open-Paws/no-animal-violence) first.
   - For industry-specific euphemisms, edit `NoAnimalViolence/IndustryEuphemisms.yml` directly.
   - Each rule entry requires: the flagged phrase, a concrete replacement, a level (`warning` or `suggestion`), and a link to supporting evidence.
3. Test locally before opening a PR:

```bash
# Lint a test document
vale --config .vale.ini README.md

# Validate YAML syntax
yamllint NoAnimalViolence/
```

4. Open a PR against `main`. The auto-merge workflow will handle merging once checks pass.

### Adding a rule entry

Each entry in a Vale `substitution` rule file follows this pattern:

```yaml
extends: substitution
message: "Consider using '%s' instead of '%s'. <reason>"
level: warning
ignorecase: true
swap:
  flagged phrase: suggested replacement
  another phrase: better alternative
```

---

## Academic Foundation

The pattern selection in this package is grounded in peer-reviewed research on speciesist language and AI bias:

- Hagendorff, Bossert, Tse & Singer (2023). "Speciesist bias in AI." *AI and Ethics*. [doi:10.1007/s43681-023-00380-w](https://doi.org/10.1007/s43681-023-00380-w)
- Takeshita, Rzepka & Araki (2022). *Information Processing & Management*.
- Leach et al. (2023). *British Journal of Social Psychology*.

---

## License

MIT — [Open Paws](https://openpaws.ai)
