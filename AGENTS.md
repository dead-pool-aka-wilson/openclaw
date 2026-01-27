# Repository Guidelines for AI Agents

## Quick Reference
- **Repo**: https://github.com/moltbot/moltbot
- **Runtime**: Node 22+ (ESM, TypeScript)
- **Package Manager**: pnpm (keep Bun compatibility)
- **Linting**: Oxlint + Oxfmt

## Build, Test, and Development Commands

```bash
# Install & Setup
pnpm install                    # Install dependencies
prek install                    # Pre-commit hooks (same as CI)

# Development
pnpm moltbot ...                # Run CLI in dev mode
pnpm gateway:watch              # Dev loop with auto-reload

# Build & Check
pnpm build                      # TypeScript compile (tsc)
pnpm lint                       # Run oxlint
pnpm format                     # Check formatting (oxfmt)
pnpm lint:fix                   # Auto-fix lint + format issues

# Testing
pnpm test                       # Run all tests (vitest)
pnpm test src/path/file.test.ts # Run single test file
pnpm test:coverage              # With coverage report
pnpm test:e2e                   # E2E tests
LIVE=1 pnpm test:live           # Live tests (requires real keys)

# Full Gate (run before commits)
pnpm lint && pnpm build && pnpm test
```

## Project Structure

```
src/                    # TypeScript source (ESM)
├── cli/               # CLI wiring, progress utilities
├── commands/          # CLI commands
├── gateway/           # WebSocket gateway server
├── infra/             # Infrastructure utilities
├── media/             # Media pipeline
├── telegram/          # Telegram channel
├── discord/           # Discord channel
├── slack/             # Slack channel
├── signal/            # Signal channel
├── imessage/          # iMessage channel
├── web/               # WhatsApp (Baileys)
├── channels/          # Channel abstractions
├── routing/           # Message routing
└── terminal/          # Terminal UI (palette, tables)

extensions/            # Plugin packages (workspace packages)
docs/                  # Mintlify documentation
dist/                  # Build output
apps/                  # Native apps (macos/, ios/, android/)
```

## Code Style & Conventions

### TypeScript
- **Strict mode**: Enabled. Avoid `any` - use proper types.
- **ESM**: Use `.js` extensions in imports (resolves to `.ts` in dev).
- **No type suppressions**: Never use `as any`, `@ts-ignore`, `@ts-expect-error`.

### Formatting (Oxfmt enforced)
- Double quotes for strings
- Semicolons required
- 2-space indentation
- ~100 char line width (soft limit)

### Naming
- **Product/UI**: "Moltbot" (capital M)
- **CLI/paths/config**: `moltbot` (lowercase)
- Files: `kebab-case.ts`
- Tests: `file-name.test.ts` (colocated with source)

### Error Handling
- No empty catch blocks: `catch(e) {}`
- Propagate errors with context
- Use typed error classes where appropriate

### File Size
- Target: ~500 LOC per file (guideline, not hard limit)
- Split/refactor when it improves clarity

### Comments
- Add brief comments for tricky/non-obvious logic only
- Self-documenting code preferred over verbose comments

## Testing Guidelines

- **Framework**: Vitest with V8 coverage (70% threshold)
- **Naming**: `*.test.ts` colocated; e2e in `*.e2e.test.ts`
- **Pure test changes**: No changelog entry needed unless user-facing

## Commit & PR Guidelines

### Commits
- Use `scripts/committer "<msg>" <file...>` (scoped staging)
- Concise, action-oriented messages: `CLI: add verbose flag to send`
- Group related changes; avoid bundling unrelated refactors

### Pull Requests
- **Review mode**: `gh pr view/diff` only - don't switch branches or edit code
- **Landing**: Prefer rebase for clean history, squash for messy history
- Add changelog entry with PR # and contributor thanks
- Run full gate locally before merging: `pnpm lint && pnpm build && pnpm test`

### Changelog
- Latest released version at top (no "Unreleased" section)
- Reference issues/PRs in entries

## Key Utilities

### CLI Progress
Use `src/cli/progress.ts` (osc-progress + @clack/prompts). Don't hand-roll spinners.

### Terminal Tables
Use `src/terminal/table.ts` for ANSI-safe table output.

### Color Palette
Use `src/terminal/palette.ts` - no hardcoded colors.

### Tool Schemas (TypeBox)
- Avoid `Type.Union` - use `stringEnum`/`optionalStringEnum` instead
- Use `Type.Optional(...)` instead of `... | null`
- Avoid `format` as a property name (reserved keyword)

## Multi-Agent Safety

When multiple agents work concurrently:
- **Don't** create/apply/drop git stash entries
- **Don't** switch branches unless explicitly requested
- **Don't** modify `.worktrees/*` or git worktree checkouts
- **Do** scope commits to your changes only
- **Do** continue if you see unrecognized files - focus on your edits

## Dependencies

- Never edit `node_modules` (any install method)
- Patched deps (`pnpm.patchedDependencies`) must use exact versions
- Patching requires explicit approval - don't do by default
- Never update the Carbon dependency

## Channels

When refactoring shared channel logic, consider ALL channels:
- **Core**: telegram, discord, slack, signal, imessage, web (WhatsApp)
- **Extensions**: msteams, matrix, zalo, zalouser, voice-call, bluebubbles

## Docs (Mintlify)

- Internal links: root-relative, no `.md` extension: `[Config](/configuration)`
- Anchors: `[Hooks](/configuration#hooks)`
- Avoid em dashes/apostrophes in headings (breaks anchors)
- README: use absolute URLs (`https://docs.molt.bot/...`)

## Version Locations

- CLI: `package.json`
- iOS: `apps/ios/Sources/Info.plist`
- Android: `apps/android/app/build.gradle.kts`
- macOS: `apps/macos/Sources/Moltbot/Resources/Info.plist`

## Troubleshooting

- Run `moltbot doctor` for migration/config issues
- macOS logs: `./scripts/clawlog.sh`
- Gateway restart: via Moltbot app or `scripts/restart-mac.sh`
