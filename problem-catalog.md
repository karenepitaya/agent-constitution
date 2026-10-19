# Problem Catalog — AI Slop & Failure Modes

**Purpose:** the problem-side reference archive for all scenario constraint documents (frontend, backend, documents/office, etc.). Every problem has an ID, symptoms, mechanism, and detection method.
Every rule in a constraint document (`AGENTS.frontend.md`, `DESIGN.md`, etc.) must trace back to an ID here. Rules without a problem don't enter the documents; problems without a rule must be explicitly marked "accepted risk".

---

## 1. Root Causes

### R1 — Mean Regression
With no constraints, model output regresses to the "greatest common divisor" of its training corpus. Frontend training data is highly homogeneous (landing pages, portfolios, Awwwards-style work over-sampled), so the default output is the same "average face".
**Corollary:** adjectives ("clean and premium") get translated back into the mean. Only explicit constraints and concrete references swap the prior.

### R2 — Missing Feedback Loop
The model writes CSS blind; it has never seen its own rendered output. Frontend correctness is not machine-checkable and is strongly context-dependent (who the user is — information it has zero of). Layout failures come from predicting instead of observing.
**Corollary:** every quality dimension invisible in a single sample (narrow viewports, long copy, empty states, error states) has zero gradient in its definition of "done".

### R3 — Process and Product Share One Channel
Conversation is the model's only expressive medium, so every step in its head risks leaking into the artifact: design self-doubt → annotation microtext; revision history → "modified because..." in the body text; long-context anchor loss → sycophancy or stupidity. Humans externalize process (git log, comments, verbal handoff); the model has no concept of an external channel.
**Corollary:** channel separation (what enters the artifact vs. what stays in conversation) cannot be self-enforced by the model. Rules must hold the line.

### R4 — English Prior
Its knowledge of font stacks, type metrics, and design trends is almost entirely Latin/English-context. Chinese is passively reached through fallback, so fonts, sizes, line-heights, and line-breaking all arrive mismatched.
**Corollary:** this is the easiest class to eliminate with hard constraints — it's a parameter error, not a taste problem.

---

## 2. Problems

### P1 — AI-Slop Visual Pattern
**Symptoms:** purple-blue gradients, blanket glassmorphism, centered hero + three feature cards, emoji as icons, universal `shadow-lg`, cards nesting cards, floating animations everywhere, over-polished perfect symmetry.
**Mechanism:** R1. The greatest common divisor of landing-page training data.
**Detection:** the blacklist is grep-able (`linear-gradient`, `backdrop-filter`, emoji codepoints); the hero-plus-three-cards layout is visual-inspection detectable — it is the fingerprint of the training distribution, and its presence is probable cause.

### P2 — Hollow Content Corrupts Layout
**Symptoms:** placeholder copy ("empower your business", "unlock infinite possibilities"), obviously fake data (three equal-length testimonials), 99.9% / 10,000+ numbers. (Chinese equivalents: 赋能, 释放无限可能, 一站式.)
**Mechanism:** R1 + R2. Fake data sets the card size rhythm; when real content arrives the layout is forced to reflow — fake content infects visual design.
**Detection:** does the layout survive substitution with the real longest copy? Full-text search for buzzwords.

### P3 — CJK Font Fallback & Shrunken Type
**Symptoms:** pages render with a SimSun/宋体 feel; body text universally one size too small (13–14px), Chinese glyphs cramped and blurry.
**Mechanism:** R4. Font stacks are designed for Latin (`'Playfair Display', Georgia, serif`); every CJK glyph falls through to the generic `serif` family → maps to SimSun-class fonts on Windows/macOS. Latin 13px metrics applied directly to Han characters.
**Detection:** grep CSS for bare `serif`; check `font-family` stacks for explicit CJK families (PingFang SC / Microsoft YaHei / Noto Sans SC); body base size ≥15px and line-height ≥1.6.

### P4 — Responsive Design Absent
**Symptoms:** designed only for the 1440px desktop screenshot; overflow and squeezing at narrow widths; stacking order nonsense; the "responsive" part is a lone `flex-wrap` — it technically deforms, but no stacking decisions were made.
**Mechanism:** R2. The artifact's final review scene is one desktop screenshot; all other viewport quality is invisible in single-point sampling. Responsiveness is a series of UX decisions (what collapses, what goes into a drawer, what keeps priority), not a technical property.
**Detection:** screenshots at 375 / 768 / 1440 fed back for review; any horizontal overflow is failure.

### P5 — Copy Length Welded into Layout (i18n Absent)
**Symptoms:** widths fixed to the current copy's length; longer text breaks the layout; strings never externalized.
**Mechanism:** R1 + R2. i18n is invisible infrastructure in the training corpus; demos all hardcode strings.
**Detection:** the red line "strings never participate in width calculations" is grep-able (fixed-width text containers combined with `overflow: hidden` and no truncation strategy); break-test with copy at 150% length.

### P6 — Interaction States Missing
**Symptoms:** hover-brighten only; no loading / empty / error states; motion either absent or everything floats.
**Mechanism:** R2. These states don't appear in the one screenshot being reviewed.
**Detection:** for every data-driven block, require loading / empty / error / populated rendered side by side.

### P7 — Chattiness on the Page (Annotation Microtext)
**Symptoms:** clean pages possessed by explanatory small print; explanations everywhere for the maximally confused user; deliverables read like a confessional with footnotes.
**Mechanism:** R3. The model can't distinguish "answering you" from "making a product"; text is its native tongue, so every design insecurity gets translated into a textual patch; defensive completeness — redundant explanation for an imagined worst-case reader.
**Detection:** word budgets (e.g. first screen ≤40 words); the delete pass — for each sentence, ask "if deleted, does the interface already express this?" — if yes, delete.

### P8 — Version-Iteration Pollution
**Symptoms:** filenames on a downward escalator ("revised" → "final" → "final-final" → "final-closing"); body text sprouting "because X, modified to Y" microtext; revision process leaking into the final artifact.
**Mechanism:** R3. Conversation history is forever present to the model, so it feels obliged to account for it — the equivalent of inlining git log into source files.
**Detection:** grep body text for revision vocabulary ("由于 / 故 / 本次修改 / 上一版", "updated because / revised / previous version"); file naming fixed externally; the prompt line "the reader is a third party who has never seen any previous version".

### P9 — Long-Context Drift
**Symptoms:** after the thread grows long, intent loses its anchor — either sycophancy (every recent message treated as supreme directive) or stupidity (answering questions from three turns ago, repeating itself).
**Mechanism:** attention dilution; early constraints decay; the model falls back to its training-prior "safe personality".
**Detection:** a sudden quality shift after a major direction change is the signal. The countermeasure is procedural: squash — distill confirmed decisions into a clean spec, start a new session, never patch the old thread.

### P10 — Over-Orderliness, No Deliberate Asymmetry
**Symptoms:** perfect symmetry, uniform whitespace, uniform density — "correct but boring"; lacking the intentional density contrast and rule-breaking of genuinely good design.
**Mechanism:** R1. A taste-calibration problem — the hardest class to encode.
**Detection:** visual inspection only. This is currently the one problem humans must own. Admit it, concentrate human review effort here, and don't waste rules on it.

---

### P11 — Black Speech: Borrowed Jargon & Self-Coined Terms
**Symptoms:** (a) borrowed corporate slang used as a professionalism signal — Chinese: 赋能, 抓手, 闭环, 颗粒度, 对齐, 打法; English: leverage, synergy, seamless, robust, best-practice; (b) self-coined pseudo-frameworks — the model invents impressive-sounding methodology names that have no definition, no source, no existence outside this document; (c) abstract nominalizations where a concrete verb would do.
**Mechanism:** R1. Corporate/consulting prose reads as "professional" in the training distribution; unable to *be* authoritative, the model optimizes for *sounding* authoritative. Species (b) is the more dangerous one: readers assume the term is real and blame themselves for not knowing it.
**Detection:** jargon grep list (both languages); the define test — pick any five abstract terms, demand a one-sentence concrete definition for each, any failure → rewrite; the deletion test — if removing an adjective loses no information, it was decoration.

### P12 — Fabricated Facts & Citations in Documents
**Symptoms:** invented references, links, standards numbers; plausible but nonexistent quotes; precise-looking statistics with no source; gaps silently filled with made-up data.
**Mechanism:** R1 + R2. The model is trained to always produce an answer, and documents have no compile step — a fabricated citation reads exactly like a real one.
**Detection:** the source test — pick any five numbers, quotes, or citations and demand the source for each; anything unverifiable is marked `[unverified]` or deleted. Never "probably true".

### P13 — Tests That Can't Fail (Self-Agreement)
**Symptoms:** the model writes implementation and test from the same (possibly wrong) understanding — they agree with each other and both are wrong; expected values copied from the implementation's actual output; mocks so complete the test becomes a tautology; tests that pass on the first run and never went red.
**Mechanism:** R2. No external oracle: agreement between code and test is not evidence of correctness when both share one author and one misunderstanding.
**Detection:** the origin test — for each assertion, "where did this expected value come from: the spec, or the implementation's output?"; red-before-green must be demonstrated, including failing for the *right* reason.

### P14 — Test Environment Contamination
**Symptoms:** real credentials, PII, or production snapshots in fixtures; tests hitting live or shared services; tests requiring elevated privileges; order-dependent tests polluting shared state.
**Mechanism:** R1 (demo corpus hardcodes real-looking secrets) + R2 (contamination is invisible in the test output — the suite passes while leaking).
**Detection:** grep fixtures/snapshots for credential patterns and PII; the suite runs hermetically (network off, no shared services) and passes under random ordering.

### P15 — Cowboy Operations
**Symptoms:** destructive commands (`rm -rf`, `drop table`, `kubectl delete`) executed without a stated blast radius, without confirmation, without a verified rollback path; unrelated changes bundled into one action; no record of what was changed or why.
**Mechanism:** R1 (tutorials normalize YOLO commands) + R2 (the model never experiences consequences).
**Detection:** every state-changing action is preceded by a blast-radius statement and explicit confirmation; every change answers "how do I undo this" *before* execution.

### P16 — Privilege Escalation as Problem-Solving
**Symptoms:** `chmod 777`, `--privileged`, disabling TLS verification, security groups opened to `0.0.0.0/0`, running as root/admin — applied to silence a permission error.
**Mechanism:** R1. Q&A corpus is full of these as "fixes"; they silence the symptom without understanding the error.
**Detection:** any permission or security-control change must name the underlying error it addresses and why narrower scoping is insufficient. "It works now" is not an explanation.

### P17 — Secrets Exposure
**Symptoms:** secrets echoed into logs, chat replies, committed `.env` files, scripts, or screenshots.
**Mechanism:** R1 + R3 — process leaks into artifacts, and the model treats secrets as ordinary strings.
**Detection:** grep diffs and outputs for secret patterns; any exposure is an incident — rotate first, report, then continue.

### P18 — Compliance Theater
**Symptoms:** checks claimed as performed ("responsive verified", "delete pass done") without actually being run; self-audit reports are narrative, not evidence; the failure surfaces only when a human re-runs the check by hand.
**Mechanism:** R2. A check that was run and a check that was claimed produce identical text — nothing in the output distinguishes them. And claiming is free while running takes effort, so the gradient points the wrong way.
**Detection:** any mechanically runnable check (grep, word count, screenshot) must ship with its raw output. Narrative claims of compliance are rejected on arrival. *Origin: caught in production — a landing page shipped with its own banned-list word (闭环) in it, one day after the list was written.*

### P19 — Register Leak
**Symptoms:** the conversation's tone, memes, and inside jokes appear in the deliverable; in-group phrasing used for a general audience; decorative bilingual fragments carried over from chat. The artifact reads like the room it was made in, not like something for a reader who wasn't there.
**Mechanism:** R3. The conversation is the model's whole world; its register bleeds into the artifact. Distinct from P7 (quantity of words) and P8 (revision history) — this is a *tone* contamination.
**Detection:** the third-party-reader pass — flag every phrase whose meaning depends on having been in the conversation; after each incident, add the caught words to the project's jargon grep list.

---

## 3. Root Cause × Problem Matrix

| Problem | Root causes | Eliminable by hard rules? |
|---|---|---|
| P1 AI-slop visuals | R1 | Mostly (blacklist + references + DESIGN.md) |
| P2 hollow content | R1, R2 | Yes (real data first) |
| P3 CJK typography | R4 | Fully (font stack + size baseline) |
| P4 responsive absent | R2 | Partially (verification loop; stacking decisions still need human review) |
| P5 copy welded in | R1, R2 | Mostly (red line + break tests) |
| P6 states missing | R2 | Yes (four-state screenshot review) |
| P7 chattiness | R3 | Yes (word budget + delete pass) |
| P8 version pollution | R3 | Yes (channel separation + external naming) |
| P9 context drift | R3 | Not by rules — procedural avoidance (squash & restart) |
| P10 over-orderly | R1 | No — humans own it |
| P11 black speech | R1 | Mostly (grep + define test + deletion test) |
| P12 fabricated facts | R1, R2 | Partially (sourcing rules force marking; verification stays human) |
| P13 self-agreement tests | R2 | Partially (origin test + red-before-green) |
| P14 test environment contamination | R1, R2 | Mostly (grep + hermeticity checks) |
| P15 cowboy operations | R1, R2 | Yes (blast-radius protocol) |
| P16 escalation as fix | R1 | Yes (forbidden-silencer list + justification rule) |
| P17 secrets exposure | R1, R3 | Yes (grep + incident rule) |
| P18 compliance theater | R2 | Yes (raw-output evidence rule) |
| P19 register leak | R3 | Mostly (third-party-reader pass + incident-driven grep list) |

---

## 4. How to Use

1. When writing constraint documents, annotate every rule with its traceability ID, e.g. `<!-- P3, R4 -->`.
2. Review rules bidirectionally: delete rules with no traceable problem; mark uncovered problems as "accepted risk" with a stated reason.
3. When a new problem appears, register it here first with an ID, then write the constraint — never let constraint documents absorb problems directly. Keep the problem archive and the countermeasure archive separate.
