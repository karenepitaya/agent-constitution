# AGENTS.backend.md — Backend Constitution (Meta)

Behavioral rules for backend work, independent of language, framework, and storage.
This file constrains **how backend decisions are made**, not which stack is used.

**Scope and precedence:**

- The global constitution (AGENTS.md) applies everywhere; this file adds backend-specific rules.
- A project repo's own AGENTS.md overrides this file. When you bend a rule here, say so and name the rule.
- One concept governs the whole file: the **trust boundary** — the edge where your system meets things you don't control (users, networks, external services, disks, other teams' APIs). Rules are divided by which side they apply to.

---

## 1. Stack Selection Is Part of the Task

**No default stack. Choosing is a decision, and decisions need reasons.**

- Choose the stack from project constraints: existing code, team skills, operational reality, expected lifespan. Not from familiarity or fashion.
- Before writing code, state the choice and the rejected alternatives. One sentence each.
- Prefer boring, proven technology. Novelty requires justification proportional to its operational risk.
- Adding a dependency is a decision: state why the existing stack can't do it. "It's popular" is not a reason.
- Don't import a framework for one function. Don't hand-roll what the standard library already does well.

**The test:** every new dependency or service in the diff carries a one-sentence justification. If the justification is "it's standard practice", redo the decision.

---

## 2. Restraint: No Defensive Noise

**Code says each thing once, to the layer that can act on it. Everything else is chattiness.**

Inside the trust boundary:

- Validate at the boundary, once. Inside the system, trust the contracts the boundary established. Re-validating the same invariant at every layer is noise, not safety.
- Never catch an exception you cannot handle meaningfully. Catch-log-continue is hiding, not handling. Let it propagate to the layer that can act.
- No handling for impossible scenarios. If the type system, schema, or constraint already forbids it, don't write code for it. If you believe it's possible, the boundary is broken — fix the boundary, not the symptom.
- Defaults and fallbacks are product decisions, not hedges. Silent fallback (guessed config, assumed timezone, empty-string defaults for required values) hides misconfiguration. Fail loudly at startup instead.
- No speculative generality: one caller means no abstraction, no configuration knobs nobody asked for, no "just in case" parameters.
- Comments explain *why*, never *what*. If code needs a "what" comment, rename or restructure it. No commented-out code, no changelog comments, no TODOs in committed code — do it now or file an issue.
- Logs record state transitions and failures, not a narration of execution. Entry/exit logging of every function is chattiness.

**The test:** for every `try/catch`, every validation, and every fallback in the diff, answer: *which real failure does this handle, and what does the caller gain?* If the answer is "just in case" — delete it. For every comment: does it say why? If it says what — delete or rename.

---

## 3. Data First

**State the model and its invariants before writing endpoints.**

- Before implementing, state the data model and its invariants. Invariants live as close to the data as the stack allows (constraints, transactions) — not in callers' memory, not in the frontend.
- Every write path names which invariants it protects and where that protection is enforced.
- There is exactly one source of truth per fact. Derived and cached state is labeled as such, with its invalidation rule stated.

**The test:** pick any write endpoint — you can name the invariant it protects and the layer that enforces it. If the answer is "the caller checks it", that's a bug.

---

## 4. Contracts Before Implementation

**The API is a promise. Design the promise before keeping it.**

- The error model is defined once and used everywhere: same shape, machine-readable codes. Clients never parse human-readable strings.
- Validation happens at the boundary, once (see §2). Past the boundary, types are trusted — or the boundary is broken.
- Breaking changes are explicit: versioned, migrated, or rejected. Never silent.
- Error responses to clients describe what the client can do, and never leak internals (stack traces, schema details, query text).

**The test:** any two endpoints return errors in the same shape; a client can handle every failure mode without reading prose.

---

## 5. Failure Is Normal

**Applies outside the trust boundary. (Inside it, §2 governs.)**

- Every call to an external dependency has: a timeout, a retry policy (or an explicit no-retry reason), and a defined caller-visible failure behavior.
- Retried operations are idempotent — or retry is forbidden.
- Degradation is deliberate: name what the user sees when dependency X is down. "It errors" is sometimes the right answer, but it's a decision, not an accident.
- Never swallow exceptions silently. Fail loudly or degrade deliberately — both are fine, invisibility is not.

**The test:** for each external call in the diff, answer three questions: timeout? retries? what does the caller see when it's down? Three answers per call, no hand-waving.

---

## 6. State and Concurrency

**Assume any request can arrive twice, out of order, or concurrently with itself.**

- Transactions are as small as possible and never span network calls.
- Shared mutable state is named: who owns it, who may write it, how readers stay consistent.
- Retries and double-clicks from clients are facts of life; write paths are idempotent or guarded.

**The test:** for each write endpoint, answer: "this exact request arrives twice at the same time — what happens?" "Probably fine" is not an answer.

---

## 7. Data Integrity Over Convenience

- Schema changes ship with a migration. Migrations are reversible, or the irreversibility is stated with its reason.
- No destructive operation without a backup or export path.
- Production data is never edited by hand without a recorded trail.
- A migration is part of the diff: review it like code, because it rewrites history.

**The test:** for every migration, answer: how do you roll back, and what does rollback do to data written in between? The answer goes in the change notes.

---

## 8. Observability Is Not Optional

- Every request carries a correlation ID through logs and downstream calls.
- Errors are diagnosable from logs alone. "Reproduce it locally to see" means the logging failed.
- Log facts at decision points (see §2: no narration). Never log secrets, credentials, or full payloads of user data.
- Health checks distinguish "process alive" from "dependencies available".

**The test:** given "user X got an error at 14:03", logs alone can locate the exact request and the failing call. If not, fix the logging before shipping.

---

## 9. Security Defaults

- Every endpoint states its authorization rule. Deny by default. "It's internal" is not a rule.
- All input is hostile: validate at the boundary, parameterize queries, escape output.
- Secrets come from the environment or a secret store — never code, never logs, never the diff.
- New endpoints ship closed; opening access is the explicit step.

**The test:** grep the diff for secrets; every new endpoint names its authorization rule.

---

## 10. Test Behavior, Not Implementation

- Tests live at boundaries: HTTP/API level, database level. Mock only true external services — never your own code.
- From the constitution: watch it fail first. A test that never went red proves nothing.
- Deleting the feature must turn tests red. If it doesn't, the tests are decoration — delete them.
- Test names describe behavior and expectation, not method names.

**The test:** name the test that goes red if this endpoint disappears. Can't name one? Write it first.

---

## 11. Performance: Measure, But Don't Be Naive

- No optimization without a measurement. But some things are not optimizations — they're baseline competence:
  - Every list endpoint has pagination and a maximum page size.
  - Know your query count per request. N+1 is a bug, not a style issue.
  - Indexes follow query patterns, stated in the migration that adds them.
- Caching is a decision with an invalidation story (see §3), not a performance garnish.

**The test:** for each endpoint, state: queries per request and maximum payload size. "Depends" requires a bound.

---

## 12. Operability

- Configuration comes from the environment, not code. A fresh environment boots with documented env vars and one command — no manual steps.
- Shutdown is graceful: stop accepting, finish in-flight work, exit.
- Required config is validated at startup; missing config fails loudly at boot (see §2: no silent fallbacks).

**The test:** fresh clone + documented env vars + one documented command = running service.

---

## When Rules Conflict

- §2 (restraint) and §5 (failure handling) divide at the trust boundary. If you can't tell which side a piece of code is on, say so — that ambiguity is itself a design smell worth surfacing.
- Any rule here loses to a real project constraint, but only if you name the rule and the constraint out loud.
