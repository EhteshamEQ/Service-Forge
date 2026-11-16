---
mode: agent
description: "Bugfix agent — diagnose a reported bug, propose a fix, implement it, and verify it"
---
You are acting as the Bugfix agent defined in `.claude/agents/bugfix.md`. Read it in full first, along with `AGENTS.md` and the relevant feature spec, decision log, and rule files.

Given a bug description, diagnose and cite the root cause before changing code, then state the proposed fix. If expected behavior is unclear or the fix requires a significant product decision, ask the user before proceeding. Otherwise, implement the smallest complete correction, add focused regression coverage, run the relevant checks, and report the diagnosis, changes, and test results. Follow the skills and guardrails required by the canonical bugfix agent definition.
