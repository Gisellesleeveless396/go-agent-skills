# go-agent-skills

Curated AI agent skills for Go projects. Works with **Claude Code**, **Cursor**, **Codex**, **GitHub Copilot**, **Windsurf**, and any agent supporting the [Agent Skills format](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/create-skills).

Built on the [Uber Go Style Guide](https://github.com/uber-go/guide), [Effective Go](https://go.dev/doc/effective_go), and hard-won production experience with large-scale Go services.

---

## Why

AI coding agents are as good as the context you give them. Without Go-specific guidance, they'll write Java-flavored Go, ignore idiomatic error handling, create goroutine leaks, and use `interface{}` where generics belong.

These skills teach your agent how to write Go the way experienced Go engineers do — with proper error wrapping, consumer-side interfaces, table-driven tests, and all the idioms that make Go code maintainable at scale.

## Skills Catalog

Skills load automatically based on context. You can also invoke them directly via slash command (e.g., `/go-code-review`).

### Code Quality

| Skill | What it does | Triggers |
|---|---|---|
| [go-coding-standards](skills/(code-quality)/go-coding-standards/) | Style conventions, naming, imports, struct init, formatting | "check Go style", "fix formatting" |
| [go-code-review](skills/(code-quality)/go-code-review/) | Structured review process with severity classification | "review this code", "check this PR" |
| [go-error-handling](skills/(code-quality)/go-error-handling/) | Error wrapping, sentinels, custom types, `errors.Is`/`As` | "handle errors", "error wrapping" |

### Architecture & Design

| Skill | What it does | Triggers |
|---|---|---|
| [go-architecture-review](skills/(architecture)/go-architecture-review/) | Package layout, dependency direction, layering, `internal/` | "review architecture", "project layout" |
| [go-interface-design](skills/(architecture)/go-interface-design/) | Consumer-side interfaces, composition, compliance checks | "design interface", "accept interfaces" |
| [go-api-design](skills/(architecture)/go-api-design/) | REST/gRPC handlers, middleware, graceful shutdown, pagination | "design API", "HTTP handler" |

### Safety & Performance

| Skill | What it does | Triggers |
|---|---|---|
| [go-concurrency-review](skills/(safety)/go-concurrency-review/) | Goroutine lifecycle, channels, mutexes, race detection | "check thread safety", "goroutine leak" |
| [go-security-audit](skills/(safety)/go-security-audit/) | OWASP, SQL injection, auth, secrets, input validation | "security review", "check vulnerabilities" |
| [go-performance-review](skills/(safety)/go-performance-review/) | Allocations, benchmarking, pprof, hot path optimization | "check performance", "reduce allocations" |

### Testing

| Skill | What it does | Triggers |
|---|---|---|
| [go-test-quality](skills/(testing)/go-test-quality/) | Test philosophy, subtests, httptest, golden files, fuzz, testcontainers | "add tests", "improve coverage" |
| [go-test-table-driven](skills/(testing)/go-test-table-driven/) | Deep dive on table-driven tests: when to use, struct design, refactoring | "table-driven test", "test matrix" |

### Workflow

| Skill | What it does | Triggers |
|---|---|---|
| [go-dependency-audit](skills/(workflow)/go-dependency-audit/) | Module hygiene, `govulncheck`, dep evaluation, go.mod review | "check dependencies", "audit deps" |
| [git-commit](skills/(workflow)/git-commit/) | Conventional Commits, atomic commits, pre-commit verification | "commit changes", "commit message" |

## Quick Start

### Install all skills

```bash
# Clone
git clone https://github.com/eduardo-sl/go-agent-skills.git

# Run install script (interactive — picks your agent)
./go-agent-skills/scripts/install.sh /path/to/your-go-project
```

### Manual install per agent

**Claude Code:**
```bash
mkdir -p .claude/skills
cp -r go-agent-skills/skills/*/* .claude/skills/
```

**Cursor:**
```bash
mkdir -p .cursor/skills
cp -r go-agent-skills/skills/*/* .cursor/skills/
```

**GitHub Copilot:**
```bash
mkdir -p .github/skills
cp -r go-agent-skills/skills/*/* .github/skills/
```

**Codex (OpenAI):**
```bash
mkdir -p .agents/skills
cp -r go-agent-skills/skills/*/* .agents/skills/
```

**Global install (all projects):**
```bash
# Claude Code
cp -r go-agent-skills/skills/*/* ~/.claude/skills/

# Cursor
cp -r go-agent-skills/skills/*/* ~/.cursor/skills/
```

### Symlink instead of copy (stays in sync with upstream)

```bash
# Example for Claude Code — project-local
for skill in go-agent-skills/skills/*/*/; do
    ln -sf "$(realpath "$skill")" ".claude/skills/$(basename "$skill")"
done
```

## Repository Structure

```
go-agent-skills/
├── skills/                            # All skill definitions
│   ├── (code-quality)/                # Category: coding standards & review
│   │   ├── _category.json
│   │   ├── go-coding-standards/
│   │   │   └── SKILL.md
│   │   ├── go-code-review/
│   │   │   └── SKILL.md
│   │   └── go-error-handling/
│   │       └── SKILL.md
│   ├── (architecture)/                # Category: design & structure
│   │   └── ...
│   ├── (safety)/                      # Category: concurrency, security, perf
│   │   └── ...
│   ├── (testing)/                     # Category: test patterns & coverage
│   │   └── ...
│   └── (workflow)/                    # Category: git, deps, automation
│       └── ...
├── scripts/
│   ├── install.sh                     # Install skills to any agent
│   └── validate.sh                    # CI: validate SKILL.md format
├── docs/
│   ├── CONTRIBUTING.md                # How to contribute new skills
│   └── SKILL_GUIDELINES.md           # Quality standards for skills
├── AGENTS.md                          # Instructions for agents working on THIS repo
├── README.md
└── LICENSE
```

## Design Principles

**Each skill is self-contained.** No cross-references or imports between skills. The agent loads only what's relevant, consuming minimal tokens.

**Instructions are for agents, not humans.** Step-by-step, imperative, with concrete ✅/❌ code examples. Agents learn better from contrast than from prose.

**Negative triggers prevent false activation.** Each skill explicitly states what it does NOT cover, pointing to the correct skill instead.

**Verification checklists close every skill.** The agent self-validates its output before presenting results.

**Under 500 lines per SKILL.md.** Detailed reference material goes in `references/` subdirectories, loaded on demand.

## Customization

These skills encode opinionated defaults. To adapt for your team:

1. Fork the repo
2. Edit the SKILL.md files to match your conventions
3. Add project-specific patterns to the code examples
4. Install from your fork

Common customizations: linter config (golangci-lint rules), import ordering (with internal packages), preferred libraries (zap vs slog, chi vs stdlib), test frameworks (testify vs stdlib).

## Sources & Acknowledgments

These skills stand on the shoulders of:

- [Uber Go Style Guide](https://github.com/uber-go/guide/blob/master/style.md) — the foundation for most coding conventions
- [Effective Go](https://go.dev/doc/effective_go) — official Go team guidance
- [Go Code Review Comments](https://go.dev/wiki/CodeReviewComments) — community review standards
- [Tech Leads Club Agent Skills](https://github.com/tech-leads-club/agent-skills) — quality standards and format conventions
- [Anthropic Skills](https://github.com/anthropics/skills) — patterns for production-grade skills

## Contributing

See [CONTRIBUTING.md](docs/CONTRIBUTING.md) for guidelines on creating new skills, the review process, and quality standards.

## License

[MIT](LICENSE)
