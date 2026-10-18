---
name: code-quality-audit
description: How to audit, review, or grade a codebase for quality, maintainability, and agent-friendliness. Use this skill whenever the user asks to review or audit code or a repo for quality problems, find tech debt or code smells, check if a codebase is healthy, assess code before refactoring, identify what's wrong with generated code, or evaluate a codebase's structure — even when they don't use the word "audit". Produce reports only; do not rewrite code unless explicitly asked.
---

# Code Quality Audit

The purpose of an audit is to give the owner an accurate, prioritized picture of their codebase's health — not a lecture on ideals. Find flaws that matter, tie every claim to evidence, and order recommendations by payoff. Read-only by default: nothing is edited unless the user explicitly asks for fixes.

## Process

### 1. Establish the baseline before judging

- Read any existing context files (AGENTS.md, CLAUDE.md, README, docs/, lint/type/test configs). The repo may have declared conventions — audit against those first. A file breaking the repo's own written conventions is a real violation; breaking a generic style preference that the repo never adopted is at most a note.
- Detect the stack (language, framework, test runner, package manager) from config files, not file extension stereotypes.
- Start with structure, not line reading: directory layout, then entry points, then the largest files (size outliers usually mark the deepest problems).

### 2. Evaluate dimensions in priority order

Audit higher-leverage dimensions first. Structure problems dwarf style problems.

1. **Structure & modularity** — Is organization by feature/responsibility or in mixed junk-drawer layers? Do modules reach into each other's internals? Can a feature be understood by reading one folder?
2. **Duplication & dead code** — copy-pasted logic that has already drifted between copies; commented-out code; unused exports/dependencies; console-log debris.
3. **God files & god functions** — files far beyond a few hundred lines and functions beyond a few dozen; one unit serving several unrelated reasons to change.
4. **Error handling & boundaries** — silent catches, swallowed errors, bare logs; unvalidated input at network/user edges; null/empty/offline cases unhandled.
5. **Naming & readability** — names requiring explanation (`data`, `temp`, `x2`), booleans that don't read as questions, misleading names.
6. **Types & contracts** (typed languages) — reliance on `any`, disabled strictness, unchecked casts over overload suppression.
7. **Tests & verification** — which features have any coverage? Do tests assert behavior rather than implementation? Do check scripts exist at all, and would a newcomer know how to run them?
8. **Secrets & hygiene** — committed `.env` files or keys, secrets in history, mixed package managers, contradictory documentation.

### 3. Verify each finding before reporting

Every single finding needs evidence — a file path, ideally with a line or excerpt. Explain the *consequence* a finding causes (a change that breaks in N places, an agent or reviewer that cannot follow this file), not just the rule it violates. If you cannot state the consequence, it is probably a nitpick — drop it or move it to minor. Confirm a "duplicate" is truly drifting, not a justified pair; confirm "dead code" is truly unreferenced.

## Report format

Use exactly this shape:

```
# Codebase Audit: <name>

## Summary
2-3 sentences: overall health of this codebase, its biggest strength, and its biggest risk.

## Scorecard
| Dimension | Grade (A-D) | One-line justification |
(with one row per dimension evaluated)

## Critical issues   (spreading risk: fix before building anything substantial)
### <issue title>
- Evidence: <path:line — excerpt or count>
- Consequence: <what breaks or what it costs>
- Fix: <the smallest change that resolves it>

## Improvements   (slowing the team down, but contained)
<same format, ordered by payoff>

## Nitpicks   (mention only if quick to list)
<bullet list, or "none">

## What's working well   (2-4 bullets — always include this section)
```

## Rules of the audit

- Ask about scope before deep-diving: everything, or one feature? Full-repo audits of large codebases should sample the largest/most-changed files first and say so.
- No rewrites in the report body — recommendations name the smallest concrete change, not an architecture manifesto.
- Quantify where honest: "37 files over 400 lines", "9 identical validation blocks" beats "lots of duplication".
- If the codebase is genuinely in good shape, say so plainly. An audit inventing problems to look thorough is worse than no audit.
- End the report by offering, in one or two lines, to fix the top item or draft the conventions doc — but don't start without the user's go-ahead.
