# testsmith

[![License: MIT](https://img.shields.io/github/license/Zavelinski/testsmith)](LICENSE)
[![Stars](https://img.shields.io/github/stars/Zavelinski/testsmith?style=flat)](https://github.com/Zavelinski/testsmith/stargazers)
[![Last commit](https://img.shields.io/github/last-commit/Zavelinski/testsmith)](https://github.com/Zavelinski/testsmith/commits)
[![Claude Code skill](https://img.shields.io/badge/Claude%20Code-skill-8A2BE2)](https://claude.com/claude-code)

Adversarial, high-value test generation for Claude Code. Reproduce the bug with a failing test first, then run a test-vs-mutant loop that yields FEW tests that actually catch defects, instead of an explosion of shallow ones.

## Why

AdverTest (a test generator vs a mutant generator, guided by coverage + mutation score) reached 66.63% fault detection on Defects4J: +8.56% over the best prior LLM method and +63.3% over EvoSuite, at comparable coverage ([arXiv:2602.08146](https://arxiv.org/html/2602.08146v2)). The problem it targets is real: 66% of devs say AI code is "almost right, but not quite," 45% find debugging it slower, and agents tend to flood PRs with low-value tests. Fault detection is the goal, not coverage.

## What you get

`/testsmith` (skill): the loop, reproduce (failing test first) -> generate -> mutate -> kill survivors -> stop on mutation score. Plus an output contract so every test earns its place by killing a mutant or pinning a spec.

Skill-only by design: no always-on overlay, zero per-session cost.

## Install

### Option 1 — claude-code-skills marketplace (recommended)

```bash
/plugin marketplace add Zavelinski/claude-code-skills
/plugin install testsmith@claude-code-skills
```

Update later with `/plugin marketplace update claude-code-skills`.

### Option 2 — standalone marketplace

```bash
/plugin marketplace add Zavelinski/testsmith
/plugin install testsmith@testsmith
```

## Usage

Run `/testsmith` (or "test this properly") when fixing a bug or hardening a function. For larger scope, it orchestrates a test-gen / mutant-gen loop via Workflow; if the project has a mutation tool (Stryker, mutmut, PIT, cosmic-ray), it scores with that.

## Limits

Mutation testing approximates real faults; it does not prove correctness. The AdverTest numbers are on Java/Defects4J; measure mutants-killed, not lines-covered.

## License

MIT

---

Part of the **[claude-code-skills](https://github.com/Zavelinski/claude-code-skills)** collection: a suite of focused, original Claude Code skills.
