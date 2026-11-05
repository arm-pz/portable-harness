# Trigger test cases

| # | Message | Expected skill | Pass condition |
| --- | --- | --- | --- |
| 1 | "Set up agent rules for this repo" | portable-harness | puts the 12 rules at the top of CLAUDE.md or AGENTS.md |
| 2 | "My CLAUDE.md is 900 lines and Claude still gets env vars wrong" | portable-harness | asks where the agent goes wrong, writes only those map sections |
| 3 | "Make the type check fail only on new errors, we have 400 old ones" | portable-harness | records a baseline and gates on it |
| 4 | "Claude keeps hitting the real Stripe API when it runs tests" | portable-harness | tags live tests and skips them by default |
| 5 | "I switch between Codex and Claude Code, I want the same setup in both" | portable-harness | writes AGENTS.md with a CLAUDE.md pointer |
| 6 | "Review what the agent just wrote" | consistent-code-review | portable-harness does not trigger |
| 7 | "Fix the failing type errors in src/" | production-code | portable-harness does not trigger (it gates errors, it does not fix them) |
| 8 | "Turn my skill into a Claude Code plugin with hooks" | skill-to-mod | portable-harness does not trigger |
| 9 | "Write a GitHub Actions workflow to publish my npm package" | production-code | shares "CI" and "deploy" keywords; portable-harness does not trigger |

Boundary pair: #3 (gate old errors, portable-harness) vs #7 (fix errors, production-code).
Boundary pair: #1 (set up rules, portable-harness) vs #6 (review output, consistent-code-review).

Results are recorded in the build report.
