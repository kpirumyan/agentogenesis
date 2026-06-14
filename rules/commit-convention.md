# Commit Convention

This project uses [Conventional Commits](https://www.conventionalcommits.org/) for all git commit messages.

---

## Format

```
<type>(<scope>): <description>

[optional body]

[optional footer(s)]
```

## Types

| Type | Usage |
|------|-------|
| `feat` | A new feature or component |
| `fix` | A bug fix |
| `docs` | Documentation-only changes |
| `refactor` | Code change that neither fixes a bug nor adds a feature |
| `test` | Adding or updating tests |
| `chore` | Maintenance tasks (dependencies, CI, tooling) |
| `style` | Formatting, whitespace — no logic changes |
| `perf` | Performance improvements |

## Scope (optional)

Use the component or area affected, e.g.:

- `feat(orchestrator): add CTD validation step`
- `fix(coder): handle missing test specs gracefully`
- `docs(memory): update error-log format`

## Rules

1. **Subject line** — imperative mood, lowercase, no period at the end, max 72 characters.
2. **Body** — explain *what* and *why*, not *how*. Wrap at 72 characters.
3. **One logical change per commit** — don't bundle unrelated changes.
4. **Reference the component** — when a commit maps to a plan component, mention it in the subject (e.g., `(Component 1)`).
