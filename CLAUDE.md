# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Agent-Main is a collection of example markdown templates for the `md` (mdflow) CLI tool. These templates demonstrate patterns for AI-assisted development workflows using Claude, Copilot, or Codex models. The repository is managed as an Obsidian vault.

## The `md` CLI Tool

Templates use YAML frontmatter for configuration and Jinja2 templating for dynamic content.

### File Naming Conventions
- `.claude.md` - Uses Claude API
- `.copilot.md` - Uses GitHub Copilot
- `.codex.md` - Uses Codex
- `.i.claude.md` - Interactive session (multi-turn conversation)

### Frontmatter Options
- `model`: Model selection (sonnet, opus, haiku, gpt-4.1)
- `print`: Output results to stdout
- `silent`: Suppress output
- `env`: Set environment variables
- `$1`: Map stdin to specific CLI flag
- `_subcommand`: Prepend subcommands for tool execution

### Template Variables
- `{{ _1 }}, {{ _2 }}` - Positional arguments
- `{{ _args }}` - Numbered list of all positional args
- `{{ _varname }}` - Named CLI flags (--_varname)
- `{{ _stdin }}` - Piped input

### File Imports
- `@./path/to/file` - Full file content
- `@./path/file.ts:136-160` - Line ranges
- `@./path/file.ts#SymbolName` - Specific symbols (TypeScript)
- `@./path/**/*.ts` - Glob patterns

### Command Inlines
- `` !`command` `` - Execute shell commands and embed output

## Usage Patterns

Basic execution:
```bash
md hello.claude.md
md positional-vars.claude.md "hello" "French"
md optional-flags.claude.md --_mode detailed
```

Multi-agent chaining (the primary architectural pattern):
```bash
md write-test.claude.md "feature description" | md implement.claude.md
md architect.claude.md "System Name" | md security-audit.claude.md
md audit.claude.md src/file.ts | md patch.claude.md src/file.ts
md changelog.claude.md v1.0.0 | md announcement.claude.md
git diff --staged | md commit.claude.md
```

## Repository Structure

- `examples/` - Single-agent examples demonstrating basic patterns
- `examples/multi-agent/` - Advanced multi-agent workflow templates

## Agent Roles (multi-agent directory)

- **architect** - Designs TypeScript interfaces
- **write-test** - Writes comprehensive test suites
- **implement** - Implements code to pass tests
- **audit** - Security vulnerability scanning
- **patch** - Fixes vulnerabilities from audit
- **security-audit** - Ruthless design review
- **pr-review** - Reviews pull requests using GitHub CLI
- **changelog** - Extracts commits into changelog format
- **announcement** - Transforms changelog to marketing post
- **audit-legacy** - Batch analyzes files for migration
- **scaffold** - Component scaffolding from patterns
