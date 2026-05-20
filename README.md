# skill-linter-action

GitHub Action wrapper for [skill-linter](https://github.com/aicatalyst-team/skill-linter) — a static analysis tool that validates AI agent skill files against the [Agent Skills specification](https://github.com/aicatalyst-team/skill-linter) across 47 rules spanning structural, frontmatter, content, security, and best-practice categories.

## Usage

### Basic — GitHub Annotations

```yaml
- uses: aipcc-cicd/skill-linter-action@v1
  with:
    path: ./skills
```

The `github` format (default) emits inline annotations directly on the PR diff.

### SARIF Upload to GitHub Security tab

Set `upload-sarif: true` and the action handles everything in one step.
The calling job must have `security-events: write` permission.

```yaml
- uses: aipcc-cicd/skill-linter-action@v1
  with:
    path: ./skills
    format: sarif
    upload-sarif: true
```

### Deep Analysis with Anthropic

```yaml
- uses: aipcc-cicd/skill-linter-action@v1
  with:
    path: ./skills
    deep: true
    anthropic-api-key: ${{ secrets.ANTHROPIC_API_KEY }}
```

### Run Only Specific Categories

```yaml
- uses: aipcc-cicd/skill-linter-action@v1
  with:
    path: ./skills
    category: security,best-practices
    strict: true
```

## Inputs

| Input | Description | Default |
|---|---|---|
| `path` | Path(s) to lint. Space-separated for multiple. | `.` |
| `format` | Output format: `text`, `json`, `sarif`, or `github` | `github` |
| `fix` | Automatically fix fixable violations | `false` |
| `deep` | Enable LLM-powered semantic analysis | `false` |
| `deep-provider` | LLM provider for deep analysis: `anthropic` or `vertex` | _(auto-detected)_ |
| `deep-model` | Specific LLM model for deep analysis | _(provider default)_ |
| `strict` | Treat warnings as errors (exit 1 on warnings) | `false` |
| `rule` | Comma-separated rule ID(s) to run exclusively | _(all rules)_ |
| `category` | Comma-separated category name(s) to run exclusively | _(all categories)_ |
| `config` | Path to a `.skill-linterrc.json` configuration file | _(auto-discovered)_ |
| `no-config` | Bypass config files and use built-in defaults | `false` |
| `quiet` | Show only errors; suppress warnings and info | `false` |
| `verbose` | Show all skills, including those with no issues | `false` |
| `version` | `skill-linter` npm version to use | `latest` |
| `anthropic-api-key` | Anthropic API key — required when `deep: true` with the Anthropic provider | _(none)_ |
| `sarif-output` | Output file path for SARIF results | `skill-linter-results.sarif` |
| `upload-sarif` | Upload SARIF to GitHub Security tab automatically (requires `format: sarif` and `security-events: write`) | `false` |

## Outputs

| Output | Description |
|---|---|
| `sarif-file` | Absolute path to the generated SARIF file (set only when `format: sarif`) |

## Exit Codes

| Code | Meaning |
|---|---|
| `0` | All checks passed |
| `1` | Issues detected (or warnings when `strict: true`) |
| `2` | Configuration error |
| `3` | CLI argument error |

## Categories

The `category` input accepts one or more of:

- `structural`
- `frontmatter`
- `content`
- `security`
- `best-practices`

## Complete Workflow Example

```yaml
name: Lint Skills

on:
  pull_request:
    paths:
      - 'skills/**'

jobs:
  lint:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      security-events: write  # required for SARIF upload
    steps:
      - uses: actions/checkout@v4

      - uses: aipcc-cicd/skill-linter-action@v1
        with:
          path: ./skills
          format: sarif
          strict: true
          upload-sarif: true
```
