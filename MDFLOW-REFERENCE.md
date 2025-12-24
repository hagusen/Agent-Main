# MDFlow Reference

Complete reference for all MDFlow (`md`) CLI features, flags, and syntax patterns.

## Table of Contents

- [File Naming Conventions](#file-naming-conventions)
- [Frontmatter Options](#frontmatter-options)
- [Template Variables](#template-variables)
- [File Imports](#file-imports)
- [Command Inlines](#command-inlines)
- [Conditionals (Jinja2)](#conditionals-jinja2)
- [CLI Usage](#cli-usage)
- [Piping & Multi-Agent Patterns](#piping--multi-agent-patterns)

---

## File Naming Conventions

The file extension determines which AI provider is used:

| Extension | Provider | Description |
|-----------|----------|-------------|
| `.claude.md` | Claude API | Anthropic's Claude models |
| `.copilot.md` | GitHub Copilot | Microsoft's Copilot |
| `.codex.md` | Codex | OpenAI's Codex |
| `.i.claude.md` | Claude (Interactive) | Multi-turn conversation mode |

The `.i.` prefix enables interactive mode, which keeps the session open for follow-up messages instead of one-shot execution.

---

## Frontmatter Options

YAML frontmatter at the top of the file configures execution behavior.

### Model Selection

```yaml
model: sonnet    # Claude Sonnet
model: opus      # Claude Opus
model: haiku     # Claude Haiku
model: gpt-4.1   # GPT-4.1 (for Copilot)
```

### Output Control

```yaml
print: true      # Print AI response to stdout (one-shot mode)
silent: true     # Suppress all output
```

### Environment Variables

Set environment variables accessible to inline commands:

```yaml
env:
  BASE_URL: https://dev.build
  DEBUG: true
  API_KEY: $SYSTEM_API_KEY
```

### Body-to-Flag Mapping

Map the markdown body content to a specific CLI flag:

```yaml
$1: prompt       # Body becomes --prompt <body>
```

### Subcommands

Prepend a subcommand to the CLI invocation:

```yaml
_subcommand: exec     # Runs: codex exec ...
full-auto: true       # Additional flags become CLI args: --full-auto
```

### Template Variables with Defaults

Define variables with default values that can be overridden via CLI:

```yaml
_feature_name: Authentication
_target_dir: src/features
_mode: brief
```

Override at runtime: `md file.claude.md --_feature_name "User Management"`

---

## Template Variables

### Positional Arguments

Access CLI positional arguments by index:

```
{{ _1 }}     # First argument
{{ _2 }}     # Second argument
{{ _N }}     # Nth argument
```

Example usage:
```markdown
Translate "{{ _1 }}" to {{ _2 }}.
```

Run: `md translate.claude.md "Hello" "French"`

### All Arguments as List

Get all positional arguments as a numbered list:

```
{{ _args }}
```

Example output:
```
1. first argument
2. second argument
3. third argument
```

### Standard Input

Capture piped input:

```
{{ _stdin }}
```

Example: `git diff | md commit.claude.md` makes the diff available as `{{ _stdin }}`

### Custom Variables

Any variable defined in frontmatter with `_` prefix:

```yaml
_feature_name: Authentication
```

```markdown
Create a {{ _feature_name }} component.
```

---

## File Imports

### Full File Import

Include entire file contents:

```
@./src/index.ts
@./path/to/file.js
```

### Line Range Import

Include specific lines (inclusive):

```
@./src/utils.ts:136-160
@./config.json:1-50
```

Format: `@./path:START-END`

### Symbol Extraction

Extract a specific exported symbol (TypeScript/JavaScript):

```
@./src/types.ts#AgentFrontmatter
@./src/models.ts#UserInterface
@./src/utils.ts#calculateTotal
```

Works with interfaces, functions, classes, and type definitions.

### Glob Pattern Import

Import all files matching a pattern:

```
@./src/**/*.ts
@./components/*.tsx
@./tests/**/*.test.js
```

### Dynamic File Paths

Combine with template variables:

```
@./{{ _1 }}
```

Run: `md audit.claude.md "src/db/**/*.ts"` expands the glob at runtime.

---

## Command Inlines

Execute shell commands and embed their output using backtick syntax:

```
!`command`
```

### Basic Commands

```
!`git status --short`
!`ls -la src/`
!`cat package.json`
```

### With Environment Variables

```yaml
env:
  BASE_URL: https://api.example.com
```

```
API endpoint: !`echo $BASE_URL`
```

### With Template Variables

```
!`git log --pretty=format:"%s" {{ _1 }}..HEAD`
!`gh pr diff {{ _1 }}`
!`cat {{ _1 }}`
```

### Multiple Commands

Use multiple inline commands in a single file:

```markdown
## Current Changes
!`git diff --staged`

## Recent Commits
!`git log -5 --oneline`

## Branch Status
!`git status`
```

---

## Conditionals (Jinja2)

Full Jinja2 templating support for conditional logic.

### If/Else

```jinja2
{% if _mode == "detailed" %}
Provide a detailed, comprehensive analysis with examples.
{% else %}
Provide a brief summary.
{% endif %}
```

### If/Elif/Else

```jinja2
{% if _format == "json" %}
Return the response as JSON.
{% elif _format == "yaml" %}
Return the response as YAML.
{% else %}
Return the response as plain text.
{% endif %}
```

### Conditional Sections

```jinja2
{% if _include_tests %}
Also generate unit tests for the implementation.
{% endif %}
```

---

## CLI Usage

### Basic Invocation

```bash
md filename.claude.md                           # Run with defaults
md filename.claude.md "arg1"                    # One positional argument
md filename.claude.md "arg1" "arg2"             # Multiple positional arguments
```

### Override Template Variables

```bash
md file.claude.md --_mode detailed
md file.claude.md --_feature_name "User Auth" --_target_dir "src/auth"
```

### Piping Input

```bash
git diff | md commit.claude.md
cat error.log | md debug.claude.md
echo "some text" | md process.claude.md
```

---

## Piping & Multi-Agent Patterns

Chain multiple agents together using Unix pipes.

### Basic Chaining

```bash
md agent1.claude.md | md agent2.claude.md
```

The output of agent1 becomes `{{ _stdin }}` for agent2.

### Workflow Examples

**Test-Driven Development:**
```bash
md write-test.claude.md "parses cron expressions" | md implement.claude.md
```

**Design Review:**
```bash
md architect.claude.md "User Auth System" | md security-audit.claude.md
```

**Security Patching:**
```bash
md audit.claude.md src/api/user.ts | md patch.claude.md src/api/user.ts
```

**Release Workflow:**
```bash
md changelog.claude.md v1.0.0 | md announcement.claude.md
```

**Git Integration:**
```bash
git diff --staged | md commit.claude.md
```

### Preserving Arguments Through Pipes

Positional arguments are available to all agents in the chain:

```bash
md audit.claude.md src/file.ts | md patch.claude.md src/file.ts
```

Both agents receive `src/file.ts` as `{{ _1 }}`.

---

## Quick Reference

| Feature | Syntax | Example |
|---------|--------|---------|
| Model | `model:` | `model: sonnet` |
| Print output | `print:` | `print: true` |
| Silent mode | `silent:` | `silent: true` |
| Environment | `env:` | `env: { KEY: value }` |
| Body mapping | `$1:` | `$1: prompt` |
| Subcommand | `_subcommand:` | `_subcommand: exec` |
| Default variable | `_name:` | `_mode: brief` |
| Positional arg | `{{ _N }}` | `{{ _1 }}` |
| All args | `{{ _args }}` | `{{ _args }}` |
| Stdin | `{{ _stdin }}` | `{{ _stdin }}` |
| File import | `@./path` | `@./src/index.ts` |
| Line range | `@./path:N-M` | `@./file.ts:10-50` |
| Symbol extract | `@./path#Name` | `@./types.ts#User` |
| Glob import | `@./pattern` | `@./src/**/*.ts` |
| Shell command | `` !`cmd` `` | `` !`git status` `` |
| Conditional | `{% if %}` | `{% if _mode == "x" %}` |
| Interactive | `.i.` suffix | `chat.i.claude.md` |
