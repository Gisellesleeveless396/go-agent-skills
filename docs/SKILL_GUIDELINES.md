# Skill Guidelines

Quality standards for SKILL.md files in this repository. Read this before
creating or modifying any skill.

## Format

### YAML Frontmatter (required)

Every SKILL.md starts with YAML frontmatter between `---` markers:

```yaml
---
name: go-skill-name
description: >
  One sentence explaining what this skill does.
  Use when: "phrase 1", "phrase 2", "phrase 3".
  Do NOT use for: X (use other-skill), Y (use another-skill).
---
```

**name**: Lowercase, hyphens only, matches directory name exactly.

**description**: Under 1024 characters. Must include:
- What it does (first sentence)
- Trigger examples with quoted phrases the user would actually say
- Negative triggers pointing to the correct skill

### Body

Markdown content with instructions for the AI agent.

## Constraints

| Rule | Limit |
|---|---|
| Total lines | ≤ 500 |
| Description | ≤ 1024 characters |
| Skill name | 1-64 characters, `[a-z0-9-]` |
| Code examples | Must specify language tag |
| Headers | ATX style (`#`), max depth `###` |

## Writing Principles

### 1. Write for agents, not humans

Skills are prompts injected into an AI agent's context window. Write them as
instructions the agent can follow step by step.

```markdown
<!-- ✅ Good — imperative, actionable -->
## Error Handling
Check every returned error. Wrap with context using fmt.Errorf and %w.

<!-- ❌ Bad — educational prose -->
## Error Handling
In Go, error handling is an important concept. The language designers
chose to make errors explicit values rather than exceptions...
```

### 2. Show contrast with ✅/❌ examples

Every pattern should show both the right and wrong way. Agents learn
better from contrast than from isolated examples.

```markdown
// ✅ Good — early return
if err != nil {
    return fmt.Errorf("fetch user: %w", err)
}

// ❌ Bad — nested else
if err == nil {
    // happy path deeply nested
} else {
    return err
}
```

### 3. One concept per section

Each numbered section covers one topic. Don't mix error handling with
concurrency in the same section. If topics relate, cross-reference
the correct skill by name.

### 4. End with a verification checklist

Every skill ends with a checklist the agent can use to self-validate:

```markdown
## Verification Checklist

1. All errors checked — no blank `_` discarding errors
2. Error messages are lowercase, contextual
3. `errors.Is` / `errors.As` used instead of `==`
```

### 5. Keep examples minimal but complete

Code blocks should be the minimum code needed to demonstrate the pattern.
No imports unless they're relevant to the point. No boilerplate unless
the boilerplate IS the point.

### 6. Assume competence

The agent (and the engineer using it) understands Go basics. Don't explain
what a goroutine is — explain when to use `errgroup` vs `sync.WaitGroup`
and why.

## Severity Classification

When skills produce findings (reviews, audits), use consistent severity:

| Level | Label | Meaning |
|---|---|---|
| 🔴 | BLOCKER | Must fix. Correctness, data loss, security. |
| 🟡 | WARNING | Should fix. Maintainability, idiomatic Go. |
| 🟢 | SUGGESTION | Consider. Style, naming, documentation. |

## Directory Structure

```
skill-name/
├── SKILL.md              # Required: main instructions (≤500 lines)
├── references/            # Optional: detailed docs loaded on demand
│   ├── error-patterns.md
│   └── migration-guide.md
└── scripts/               # Optional: helper scripts
    └── analyze.sh
```

**SKILL.md** is the brain. Keep it focused on procedures and patterns.

**references/** holds detailed content that the agent loads only when
explicitly directed by SKILL.md (e.g., "See references/error-patterns.md
for the full error hierarchy"). This keeps the main skill lean.

**scripts/** holds executable helpers for tasks where precision matters
more than flexibility (e.g., a linter wrapper, a migration scanner).

## Anti-Patterns

Avoid these in skill content:

- **Hedging language**: "You might want to consider..." → "Use X when Y."
- **Exhaustive lists**: Listing all 40 golangci-lint rules → Focus on the 10 that matter most.
- **Framework worship**: "Always use chi/gin/echo" → Explain the pattern, mention options.
- **Outdated advice**: Check that Go version requirements match current releases.
- **Hallucinated APIs**: If you're not sure a function exists, don't include it.
- **Wall of text**: If a section has no code example, it probably needs one.
