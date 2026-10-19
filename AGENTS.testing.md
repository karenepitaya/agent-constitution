# AGENTS.testing.md — Testing Constitution (Meta)

Behavioral rules for writing, changing, and running tests. Independent of language and test framework.

**Scope and precedence:**

- The global constitution (AGENTS.md) applies everywhere — especially §4 (goal-driven execution, watch it fail first), which this file extends but does not repeat.
- Problem traceability: rules cite `problem-catalog.md` via `<!-- Pn, Rn -->`.
- One premise governs the whole file: **a test is evidence, not decoration.** Evidence can be faked; the rules below exist to make faking detectable.

---

## 1. Tests Verify the Spec, Not the Implementation

<!-- P13, R2 -->

**When you wrote the code, agreement between your code and your test proves nothing — both may share the same misunderstanding.**

- Expected values come from the spec, the user's statement, or the domain — never from observing the implementation's output. If you derived an expectation by running the code, say so; that's characterization, not verification, and it must be labeled as such.
- Read the requirement again before writing assertions. The test encodes *your reading of the requirement*, and that's exactly the thing that needs checking.
- When the user's description and the code's behavior disagree, the test sides with the user's description — and the disagreement is surfaced, not silently resolved.

**The test (origin test):** for any five assertions in the diff, answer "where did this expected value come from?" If the answer is "from what the code does", the test is circular — rewrite it from the spec or flag the spec as unclear and ask.

---

## 2. Red Before Green, For the Right Reason

<!-- P13 -->

- Every new test must be watched failing before it passes (constitution §4). A test that passed on the first run is suspect: break the implementation deliberately and confirm the test goes red.
- The failure must be the right failure: the assertion firing, not a syntax error, import failure, or harness misconfiguration. "It failed" is not enough — *why* it failed is the evidence.
- Deleting the feature must turn tests red. If nothing goes red, the tests are decoration — delete them or make them real.

**The test:** report, per delivery, which tests were observed red and what made them red. No red observation, no claim of verification.

---

## 3. Mock Only True Externals

<!-- P13 -->

- Mock what's outside your control: vendor APIs, network, clocks, randomness. Never mock your own code — a fully mocked test verifies that your mocks agree with themselves.
- Fakes over mocks where practical: an in-memory substitute with real behavior beats a script of expected calls.
- A test asserting that a mock was called with X tests the wiring of the mock, not the behavior of the system. Assert outcomes.

**The test:** name what each mock replaces. If the answer is "the layer under test", the test is a tautology.

---

## 4. Test Data and Environment Hygiene

<!-- P14, P17, R1 -->

**Security and permissions apply to test code with full force — fixtures leak too.**

- Synthetic data only. No real credentials, tokens, connection strings, personal data, or production dumps in fixtures, snapshots, or example payloads — even "just for local". Grep the test tree for secret patterns before committing.
- Tests are hermetic: they run with the network off, touch no shared or live services, and require no elevated privileges. A test that needs production access is not a test — it's an unreviewed ops action.
- Tests clean up after themselves and pass under random ordering. Order-dependence is shared-state pollution; shared state in tests hides the same bugs it hides in production.
- Test fixtures use obviously fake values (`example.com`, `test-key-...`), so a leak is recognizable as synthetic at a glance.

**The test:** (a) grep fixtures and snapshots for credential/PII patterns — zero hits; (b) run the suite isolated and in random order — it passes; (c) name any test that can't run hermetically and justify it explicitly.

---

## 5. Flaky Tests Are Bugs, Not Weather

<!-- P13, R2 -->

- A flaky test is a failing investigation, not an inconvenience. Fix it or delete it — never mute it, never retry-until-green in CI configuration.
- Retries mask races. If a test passes on retry, the system has a timing or state bug that the test is correctly detecting and the retry is silencing.
- Quarantine with an owner and a deadline is acceptable; silent skipping is not.

**The test:** the suite contains no `@skip`/`@ignore`/retry-config without a linked issue and an expiry.

---

## 6. Coverage Is a Floor, Not a Goal

<!-- R2 -->

- No coverage theater: hitting a line is not testing it. The measure of a suite is what breaks when the code breaks.
- For each behavior you ship, ask the mutation question: "if I break this line, which test goes red?" No answer → missing test. This is the cheap man's mutation testing, and it catches decoration that coverage reports applaud.

**The test:** pick the three most important lines of your change; name the test that kills each one if broken.

---

## When Rules Conflict

- §1 (spec-derived tests) vs. legacy code with no spec: characterization tests are legitimate *as a starting point* — label them as such, and state what would convert them into spec tests.
- §4 (hermetic) vs. integration testing: true integration tests against staging are valuable — but they live in a separately marked suite, never in the default run, and never against production.
- Speed vs. §2: in a genuine emergency, fixes may ship with red-observation deferred — but the deferral is stated out loud, with a follow-up task. Emergencies explain deviations; they don't erase them.
