# Automated Sync

Rule files in this repository are **generated** from the canonical phrase list at [Open-Paws/no-animal-violence](https://github.com/Open-Paws/no-animal-violence).

## Do not edit generated files directly

The following files are overwritten by the `propagate.yml` workflow whenever the canonical repo changes:

- `NoAnimalViolence/AnimalIdioms.yml`
- `NoAnimalViolence/meta.json`

To add, change, or remove a rule: edit `rules.yaml` in the canonical repo, then the propagation workflow will open a PR here automatically.

## PR labels

Automated sync PRs carry the label `automated-sync`. These should be reviewed for correctness and merged promptly.

## Questions

See the [canonical repo](https://github.com/Open-Paws/no-animal-violence) for architecture docs.
