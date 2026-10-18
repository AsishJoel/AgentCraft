---
name: quality-first-development
description: Rules for writing and modifying application code with quality and AI-agent-friendliness built in. Use this skill whenever implementing features, writing new code, changing existing code, fixing bugs, or building applications of any kind — even if the user doesn't mention quality, structure, conventions, or modularity. Apply it to every coding task, not just large ones.
---

# Quality-First Development

Code exists to be read — by teammates now and by AI agents tomorrow, both of which start every task knowing nothing. Every rule below serves that reality: it makes code cheaper to load, easier to verify, and safer to change. Follow these rules on every task, no matter how small.

## Before writing any code

1. Read the project's existing context files if present (AGENTS.md, CLAUDE.md, README, docs/) and any conventions docs. Existing conventions outrank everything in this skill — if the repo says one thing and this skill says another, follow the repo.
2. Survey the nearby code before adding anything. Search for existing functionality that does the job first. Reinventing an existing helper is the most common and most avoidable failure.
3. For non-trivial work, state a short plan of files to create/modify before writing code. Plans are cheap to correct; code is not.

## Structure

- Organize by feature, not by layer. A self-contained module (screens/logic/data/types together) lets one task be understood by reading one folder. Prefer `features/<name>/` plus a small logic-free `shared/` over `components/ + hooks/ + services/ + utils/` layers.
- Modules may use `shared/`, but `shared/` must not know about any feature. If "shared" code names a feature or its domain, it has become a junk drawer — move it.
- Features must not reach into each other's internals. Interactions go through a feature's public entry point (exported API/surface), keeping the dependency graph shallow.
- Never edit more files than the task requires. If a change touches many unrelated files, stop and reconsider — it usually means responsibilities have gotten tangled.

## Code-level standards

- **Small files, small units.** Aim for files around 100–200 lines and functions around 20–30. Splitting early keeps context loadable and reviews trivial. Reaching for a limit is a smell, not a target.
- **One responsibility per unit.** A file, function, or component should have exactly one reason to change.
- **Names carry the documentation.** Descriptive names (`daysUntilDeadline`, `isLoading`, `canEdit`) beat comments. Functions are verbs; variables are nouns; booleans read as questions. If a name requires an explanation containing "actually", choose a different name.
- **Prefer the boring solution.** Explicit, obvious code over clever one-liners. Complexity must buy something real; delete anything that doesn't.
- **Handle failure explicitly.** Every network call, parse, file access, and query can fail. Validate inputs at boundaries, surface errors to the user (never silent catch or bare log-and-swallow), and ask of every line: what if this is null, empty, offline, or twice as slow?
- **Explain why, not what.** Comments are for non-obvious reasons and constraints only. Commented-out code and debug logs are deleted, never committed.
- **Types over guesswork** (in typed languages): full strictness, no implicit `any`. Types are checkable truth that can be verified without running the app.

## Growth control

- **DRY with the rule of 3**: tolerate duplication until the third occurrence reveals the true pattern. Never merge two things that "look similar" but change for different reasons — wrong abstractions are worse than duplication.
- **YAGNI**: build what is needed now. Decline speculative options, flags, and "we might need this later" flexibility.
- **No dead code.** Unused exports, unreachable branches, placeholder files are removed, not scaffolded-and-forgotten.
- **Dependencies are cost.** Add a package only when the problem is genuinely hard and in-library solutions don't exist. Justify new dependencies to the user.

## Verification

1. After every change, run the project's checks (typecheck, lint, build, tests). Never claim completion without them passing — if no checks exist for the repo, offer to set them up before proceeding with substantial work.
2. Add or update a test covering the behavior just built or fixed. Every bug fix ships with a test that would have caught it.
3. Tests verify behavior, not implementation detail, so they stay valid through refactors.
4. Commit after each coherent, verified slice with a message that states the outcome (`fix: reorder drops filtered items`, not `changes`). Small diffs are reviewable; large diffs are trusted blindly.
5. Report honestly: state what is verified (tested, manually checked) versus what is written-but-unverified.

## When something is unclear

If existing code is inconsistent, requirements are ambiguous, or the change feels risky, ask before acting, and say exactly what is ambiguous. Guessing at conventions multiplies confusion; one good question is cheaper than a wrong direction baked into code.
