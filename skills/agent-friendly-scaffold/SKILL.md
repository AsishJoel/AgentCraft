---
name: agent-friendly-scaffold
description: How to set up a new project or restructure an existing repo so AI agents can load context quickly and work safely. Use whenever starting a fresh project, initializing a repo for app/code building, or when the user mentions AGENTS.md, agent-friendly structure, "set up our codebase", "structure our repo for AI", clean-slate project setup, or making a codebase modular — even as part of a larger request. Also use when adding these properties to an existing messy repo. Focused on structure and context files, not on writing features or reviewing quality.
---

# Agent-Friendly Scaffold

An AI agent entering a repository knows nothing and must rebuild understanding from scratch every session. This skill creates the skeleton that makes that reconstruction take seconds: discoverable structure, written conventions, and checks that make correctness machine-verifiable. Scope discipline: this skill builds the skeleton and container, features come later through normal development.

## First: understand the project

1. Ask the user (or infer and confirm): what the app does, target platform(s), language/framework, team size, and how it will be built (fresh start vs. restructuring an existing repo).
2. Detect existing tooling before creating anything. If the project already has structure or conventions doc, this becomes an incremental reform, not the layout below.
3. Confirm the plan — the full proposed tree and the checks to set up — before writing files.

## The scaffold

### 1. Directory layout (feature-based)

```
<project>/
├── AGENTS.md              <- the agent's brain (below)
├── README.md              <- human overview: what, how to run, how to contribute
├── docs/
│   ├── architecture.md    <- system picture + where data flows
│   └── decisions.md       <- each choice and its reason (see below)
├── .github/workflows/     <- checks (if a remote repo)
├── .env.example           <- every variable the app needs, with safe placeholders
├── <feature-root>/        <- features live here (name it for the stack: src/features, app, packages/...)
│   └── .gitkeep
└── shared/                <- only context-free, feature-agnostic helpers
```

- Keep navigation/glue in its own thin layer and everything else self-contained per feature.
- `shared/` is logic-free and never mentions a feature by name.
- For a monorepo, one AGENTS.md at the root plus a short one per package.

### 2. AGENTS.md (the deliverable agents actually read)

Write it by hand — derivation from package files cannot capture intent. Keep it under ~150 lines and include exactly:

- **Stack section:** language, framework + version, state/style/data libraries, and explicitly the out-of-favor alternatives ("state: X. Do not introduce Y.")
- **Structure section:** the feature-folder rule, what shared/ is for, and the import boundaries ("features never import each other's internals").
- **Commands section:** every check — typecheck, lint, test, build, codegen — with the exact invocation. Agents follow verified commands, not instructions to "run the tests".
- **Conventions section:** error handling pattern, file-size expectations, naming style, where DB/API access lives, comment policy.
- **Gotchas section:** the traps (generated files not to hand-edit, migrations workflow instead of manual schema edits, etc.).

Leave clearly-marked TODO placeholders rather than inventing project-specific facts you don't know; tell the user which placeholders to fill.

### 3. docs/

- **architecture.md:** a short description of the layers, where data lives, and the flows between them. A simple text/ASCII diagram is fine; update it as the system grows.
- **decisions.md:** one entry per real choice: decision, alternatives considered, reason. This prevents future agents (and teammates) from relitigating settled questions. Establish it now even if it starts nearly empty.

### 4. Verification, before features exist

Set up the cheapest possible check pipeline and wire it into CI if there is a remote repo:

- Create the scripts/checks now (typecheck if the language supports it, lint with a standard config, test runner with one example test that passes). An empty test folder with a passing example file prevents the "nothing to run" chicken-and-egg problem.
- Add a CI workflow running those checks on every push. Keep it minimal — green, fast, boring.
- Wire the commands into AGENTS.md's Commands section verbatim.

### 5. Guardrails

- `.env` in `.gitignore` from the first commit, plus `.env.example` documenting every variable with placeholder values. Never fill real secrets anywhere.
- If a database is part of the stack, create the migrations directory and note in AGENTS.md that all schema changes go through migrations — never manual edits.

## Finish by

1. Do a final check of the tree you created against what this skill promises — three self-contained pieces: readable layout, complete AGENTS.md, runnable checks.
2. Summarizing the structure in the chat (tree + what each piece is for) and prompting the user: "Next: pick the first feature to build" — not building the first feature unprompted.
