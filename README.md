# testsmith

Adversarial, high-value test generation for Claude Code. Reproduce the bug with a failing test first, then run a test-vs-mutant loop that yields FEW tests that actually catch defects, instead of an explosion of shallow ones.

## Why

AdverTest (a test generator vs a mutant generator, guided by coverage + mutation score) reached 66.63% fault detection on Defects4J: +8.56% over the best prior LLM method and +63.3% over EvoSuite, at comparable coverage ([arXiv:2602.08146](https://arxiv.org/html/2602.08146v2)). The problem it targets is real: 66% of devs say AI code is "almost right, but not quite," 45% find debugging it slower, and agents tend to flood PRs with low-value tests. Fault detection is the goal, not coverage.

## What you get

`/testsmith` (skill): the loop, reproduce (failing test first) -> generate -> mutate -> kill survivors -> stop on mutation score. Plus an output contract so every test earns its place by killing a mutant or pinning a spec.

Skill-only by design: no always-on overlay, zero per-session cost.

## Install (Claude Code, local directory marketplace)

```jsonc
// ~/.claude/settings.json
"extraKnownMarketplaces": {
  "testsmith": { "source": { "source": "directory", "path": "C:\\Users\\<you>\\testsmith" } }
},
"enabledPlugins": { "testsmith@testsmith": true }
```

Or after publishing: `"source": { "source": "github", "repo": "<you>/testsmith" }`.

## Usage

Run `/testsmith` (or "test this properly") when fixing a bug or hardening a function. For larger scope, it orchestrates a test-gen / mutant-gen loop via Workflow; if the project has a mutation tool (Stryker, mutmut, PIT, cosmic-ray), it scores with that.

## Limits

Mutation testing approximates real faults; it does not prove correctness. The AdverTest numbers are on Java/Defects4J; measure mutants-killed, not lines-covered.

## License

MIT
