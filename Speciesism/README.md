# Speciesism — Vale Style Package

A [Vale](https://vale.sh) style package that detects speciesist language in prose and suggests clearer, more professional alternatives.

## Installation

Add to your `.vale.ini`:

```ini
Packages = https://github.com/Open-Paws/vale-speciesism/releases/latest/download/Speciesism.zip
```

Then run:

```bash
vale sync
```

## Rules

| Rule | Level | Description |
|------|-------|-------------|
| `Speciesism.AnimalIdioms` | warning | Violent animal idioms with professional alternatives |
| `Speciesism.AnimalMetaphors` | warning | Animal-as-object metaphors with clearer alternatives |
| `Speciesism.TechTerminology` | suggestion | Technical terms with more precise alternatives |

## Configuration

Enable all rules:

```ini
[*]
BasedOnStyles = Speciesism
```

Enable specific rules only:

```ini
[*]
Speciesism.AnimalIdioms = YES
Speciesism.TechTerminology = NO
```

## Academic Foundation

- Hagendorff, Bossert, Tse & Singer (2023). "Speciesist bias in AI." *AI and Ethics*.
- Takeshita, Rzepka & Araki (2022). *Information Processing & Management*.
- Leach et al. (2023). *British Journal of Social Psychology*.

## License

MIT — [Open Paws](https://openpaws.ai)
