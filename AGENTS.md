# Repository Guidelines

## Project Structure & Module Organization
- Keep the root lean; place Go packages at the root or under `x/` for experiments—avoid `internal/` and `pkg/`.
- `x/` currently holds OCR/navlog prototypes (e.g., paired `.html`, `.md`, `.png` assets). Keep related artifacts together in subfolders.
- Do not commit generated state (`.grok`), editor swap files, core dumps, or built binaries; ignore them when they appear.
- Track work in `TODO/`; `TODO.md` should index other TODO files and small tasks.

## Build, Test, and Development Commands
- Standard Go toolchain; initialize modules where needed.
- Run `go test ./...` for the full suite; keep tests runnable offline.
- Format before committing: `gofmt -w .`; sanity-check with `go vet ./...` when Go code exists.
- Prefer fast search via `rg "pattern"`; use `go doc ./...` to review APIs.

## Coding Style & Naming Conventions
- All Go code must be `gofmt`-clean; package names short, lower-case, no underscores.
- Exported types, funcs, and any non-obvious logic need concise, explanatory comments; improve weak or missing comments when you touch code.
- Use table-driven tests in `*_test.go`; extract helpers to avoid duplication.
- Keep Markdown concise; use fenced blocks for commands or samples.

## Testing Guidelines
- Tests rely on the standard `testing` package and should remain deterministic and offline.
- Add coverage with new features; include representative OCR/navlog cases where applicable.
- Run `go test ./...` before pushing and note notable failures in TODO items.

## TODO Tracking
- `TODO/TODO.md` lists open items and links to other TODO files; number tasks with zero-padded IDs (001, 002, ...).
- Never renumber existing entries; mark completion with `[x]` and mirror numbering for subtasks (e.g., `005.1`).

## Commit & Pull Request Guidelines
- Commit messages are short, imperative, and capitalized (e.g., "Add OCR parser"); bodies contain per-file bullet summaries.
- Stage explicitly (`git add path/to/file`), never `git add .` or `git add -A`.
- PRs include a concise description, tests run, and before/after notes if behavior or outputs change; link relevant issues.
- Prompt for a commit after significant edits or green test runs; avoid bundling unrelated changes.

## Currency & Workflow
- Before work, skim `~/.codex/AGENTS.md` for updated global rules; capture cross-repo insights in `~/.codex/meta-context.md`.
- After finishing a task, suggest immediate next steps and request a commit once tests are green.
