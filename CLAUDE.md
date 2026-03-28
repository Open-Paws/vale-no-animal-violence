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

## Related Repos

- [no-animal-violence](https://github.com/Open-Paws/no-animal-violence) — Multi-tool rule definitions (woke, alex, Vale)
- [eslint-plugin-no-animal-violence](https://github.com/Open-Paws/eslint-plugin-no-animal-violence) — ESLint plugin for JS/TS
