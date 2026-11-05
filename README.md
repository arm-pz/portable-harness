# portable-harness

An agent skill that sets up CLAUDE.md / AGENTS.md rules and CI guardrails so Claude Code, Codex and Cursor behave the same on your repo. It writes 12 working rules at the top of the instruction file, adds "map" sections only where the agent keeps getting things wrong, and installs guardrails the agent can't talk its way past: a type check that fails only on new errors, live-service tests that run only on request, and tests plus type check before every deploy from main. The rules, map and guardrails live in the repo, so they move with you when you switch harness.

Install: `npx skills add arm-pz/portable-harness --skill portable-harness`

Ask it things like:

- "Set up agent rules for this repo."
- "My CLAUDE.md is 900 lines and Claude still gets env vars wrong."
- "Make the type check fail only on new errors, we have 400 old ones."
- "Claude keeps hitting the real Stripe API when it runs tests."

## What you get

| Layer | What it does |
|---|---|
| Rules | 12 rules at the top of CLAUDE.md or AGENTS.md (think first, minimum code, surgical changes, verified goals, fail loud...). Conflicts with existing rules are listed for you to decide, never averaged |
| Map, not manual | One section per problem area you name, about 180 lines max, with a worked example from your code and a "Do not" list. Nothing else gets documented |
| Type-check baseline | `typecheck_gate.py` records today's errors and fails only on new ones. Line shifts don't count as new; a second copy of an old error does. Works with tsc, mypy, pyright and dotnet build output |
| Live tests opt-in | Tag and default-skip for pytest, Jest/Vitest, xUnit and Go |
| Deploy gate | Push to main runs tests and the type check; deploy depends on both |

Every change is shown as a diff and needs your yes before it's written.

## Not for

Pruning or auditing a setup that already exists (`harness-audit`), reviewing code (`consistent-code-review`, which this skill points you to after the agent's next big change), writing features or fixing the type errors themselves (`production-code`), or turning a skill into a Claude Code plugin (`skill-to-mod`).

## Install

```
npx skills add arm-pz/portable-harness --skill portable-harness
```

Or copy `skills/portable-harness/` into your agent's skills directory (`.claude/skills/`, `.agents/skills/`, `.cursor/skills/` or `.qoder/skills/`). With Claude Code plugins, `.claude-plugin/plugin.json` makes the repo installable as a plugin.

## Structure

```
portable-harness/
├── README.md
├── LICENSE
├── CHANGELOG.md
├── .claude-plugin/
│   └── plugin.json
├── skills/
│   └── portable-harness/
│       ├── SKILL.md
│       ├── references/
│       │   ├── rules.md          # the 12 rules, with credit
│       │   └── guardrails.md     # per-stack recipes
│       └── scripts/
│           ├── typecheck_gate.py
│           └── test_typecheck_gate.py
└── tests/
    ├── evals.json
    └── test-cases.md
```

## Credits

Rules 1-4 come from Jiayuan Chang's write-up of Andrej Karpathy's thread on how coding agents go wrong. Rules 5-12 were collected from posts on X whose authors are unknown. If you know who wrote them, open an issue and they'll be credited.

## Test results

Version 0.1.0: 1 script test pass; 9 trigger cases reviewed-only (judged from the description, not run in a live agent).

## Known limits

The gate's error matching is tested on tsc and mypy output. pyright and dotnet output follow the same `file:line - error` shape but haven't been run against it. Check one run on your repo before trusting it in CI.
