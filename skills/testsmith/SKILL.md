---
name: testsmith
description: Adversarial, high-value test generation. Use when fixing a bug, hardening a function, or when an agent produced many shallow tests. Reproduces the bug with a failing test first, then runs a test-vs-mutant loop to produce FEW tests that actually catch defects, tied to the change. Trigger with /testsmith or "write tests for", "test this properly", "are these tests any good".
version: "0.1.0"
user-invocable: true
metadata:
  emoji: "🧪"
---

# testsmith

Generate tests that catch real defects, not tests that pad coverage. Built on the adversarial pattern: a test generator and a mutant generator push against each other until the suite survives the mutants.

## Why this exists (evidence)

- AdverTest (test-gen agent vs mutant-gen agent, guided by coverage + mutation score) hit 66.63% fault detection on Defects4J: +8.56% over the best prior LLM method and +63.3% over EvoSuite, at comparable coverage. Better probing, not more tests. Source: arXiv:2602.08146.
- The problem it fixes: 66% of devs say AI code is "almost right, but not quite"; 45% find debugging AI code more time-consuming; AI bug-introduction runs 9.8%-42.1%. And agents tend to emit an explosion of low-value LLM tests. Quantity is not the goal; fault detection is.

## The loop

### Step 0. Reproduce first (non-negotiable for bug fixes)
Write ONE failing test that captures the bug or the missing spec, and run it to confirm it FAILS for the right reason, BEFORE any fix. A fix without a prior failing test is unverified.

### Step 1. Generate candidate tests (test-gen)
Write the smallest set of tests that pin the intended behavior: the happy path, the boundary, and the specific failure mode. Assert on real outputs and identifiers, not on incidental formatting.

### Step 2. Mutate (mutant-gen, the adversary)
Introduce small, plausible faults into the code under test (flip a comparison, off-by-one, drop a branch, swap operands, return early, null a value). Each mutant is a hypothesis: "a real bug could look like this."

### Step 3. Kill or learn
Run the tests against each mutant:
- Mutant killed (a test fails) -> good, that behavior is protected.
- Mutant SURVIVES (all tests pass) -> a gap. Add ONE targeted test that kills it. Do not add unrelated tests.

### Step 4. Stop
Stop when surviving mutants are only equivalent mutants (no behavior change) or the mutation score crosses your threshold. The output is a SMALL suite where every test earns its place by killing a mutant or pinning a spec.

## How to run it

- Small scope: do the loop inline, a few mutants by hand.
- Larger scope: orchestrate with a Workflow, a test-gen stage and a mutant-gen stage in a pipeline, looping until no fresh survivors (mirrors the find -> adversarially-verify pattern). Route the mechanical mutant generation to a cheaper model (see model-router); keep assertion design on the stronger model.
- If the project has a mutation-testing tool (Stryker, mutmut, PIT, cosmic-ray), use it to score instead of hand-mutating.

## Output contract

- A failing test existed before the fix (for bug work).
- Final suite is SMALL and every test maps to a killed mutant or a named spec.
- Report mutation result: mutants tried, killed, survived (and why survivors are equivalent). Do not report a coverage percentage as if it were fault detection.

## Composes with

- `test-driven-development` / `adversarial-verify`: testsmith is the test-quality engine under them.
- `model-router`: mutant generation is mechanical -> cheap model; assertion design -> stronger model.

## Honest limits

- Mutation testing approximates real faults; it does not prove correctness. Equivalent mutants need human judgment.
- The AdverTest numbers are on Java/Defects4J; the pattern transfers but your delta depends on language and harness. Measure mutants-killed, not lines-covered.
