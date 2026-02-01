# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

Superpowers is a Claude Code plugin that provides a skills library for software development workflows. Skills are markdown documents (with YAML frontmatter) that guide Claude through processes like TDD, debugging, code review, and planning.

## Architecture

```
superpowers/
├── skills/                    # Core skills library (14 skills)
│   └── <skill-name>/SKILL.md  # Each skill in its own directory
├── commands/                  # User-invocable slash commands
├── agents/                    # Agent templates (e.g., code-reviewer)
├── hooks/                     # Session lifecycle hooks
│   ├── hooks.json             # Hook configuration
│   └── session-start.sh       # Injects using-superpowers at session start
├── lib/skills-core.js         # Skill discovery and metadata utilities
└── .claude-plugin/plugin.json # Plugin metadata (version, author, etc.)
```

### How Skills Work

1. `hooks/session-start.sh` runs at session start and injects the `using-superpowers` skill content
2. `using-superpowers` teaches Claude to check for relevant skills before any task
3. Claude uses the `Skill` tool to load other skills as needed
4. Skills have YAML frontmatter (`name`, `description`) for discovery

### Skill Structure

```markdown
---
name: skill-name
description: Use when [triggering conditions] - never summarize workflow here
---

# Skill Name

## Overview
Core principle in 1-2 sentences.

## When to Use
Symptoms and use cases.

[Flowchart if decision is non-obvious]

## Core Pattern / Quick Reference
Tables or bullets for scanning.
```

## Testing

Integration tests run real Claude Code sessions in headless mode:

```bash
cd tests/claude-code
./test-subagent-driven-development-integration.sh
```

Tests verify behavior by parsing session transcripts (`.jsonl` files). Token usage analysis available via `analyze-token-usage.py`.

**Requirements:**
- Run from the superpowers plugin directory
- Enable local dev marketplace in `~/.claude/settings.json`: `"superpowers@superpowers-dev": true`

## Key Files

- `skills/using-superpowers/SKILL.md` - Bootstrap skill, injected at every session start
- `skills/writing-skills/SKILL.md` - How to create and test new skills (TDD for documentation)
- `hooks/session-start.sh` - Session hook that injects context
- `lib/skills-core.js` - ES module for skill discovery (`extractFrontmatter`, `findSkillsInDir`, `resolveSkillPath`)

## Skill Descriptions

Descriptions are critical for discovery. They must:
- Start with "Use when..."
- Describe triggering conditions only
- NEVER summarize the skill's workflow (Claude will follow the description instead of reading the skill)
