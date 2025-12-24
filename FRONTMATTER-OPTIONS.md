# MDFlow Frontmatter Options

All available YAML frontmatter options for MDFlow templates.

---

## Model Selection

```yaml
model: sonnet      # Claude Sonnet
model: opus        # Claude Opus
model: haiku       # Claude Haiku
model: gpt-4.1     # GPT-4.1 (Copilot)
```

---

## Output Control

```yaml
print: true        # Print response to stdout
silent: true       # Suppress all output
```

---

## Environment Variables

```yaml
env:
  BASE_URL: https://api.example.com
  DEBUG: true
  API_KEY: $SYSTEM_ENV_VAR
```

Variables are accessible in inline commands via `!`echo $BASE_URL``.

---

## Body-to-Flag Mapping

```yaml
$1: prompt         # Maps markdown body to --prompt flag
```

---

## Subcommand Prefix

```yaml
_subcommand: exec  # Prepends subcommand: `codex exec ...`
```

---

## Additional CLI Flags

Any other key becomes a CLI flag passed to the underlying tool:

```yaml
full-auto: true    # Becomes --full-auto
temperature: 0.7   # Becomes --temperature 0.7
```

---

## Template Variables with Defaults

Prefix with `_` to define template variables:

```yaml
_feature_name: Authentication
_target_dir: src/features
_mode: brief
_include_tests: false
```

Override at runtime: `md file.claude.md --_mode detailed`

Access in template: `{{ _feature_name }}`

---

## Quick Reference

| Option | Type | Description |
|--------|------|-------------|
| `model` | string | AI model to use |
| `print` | boolean | Output response to stdout |
| `silent` | boolean | Suppress all output |
| `env` | object | Environment variables for commands |
| `$1` | string | Map body content to named flag |
| `_subcommand` | string | Prepend subcommand to CLI |
| `_*` | any | Template variable with default value |
| `*` | any | Passed as CLI flag to underlying tool |
