# AGENTS.frontend.md — Frontend Constitution (Meta)

Behavioral rules for all page-producing work: web apps, HTML pages, prototypes, slides, documents with visual layout. Independent of framework and tooling.

**Scope and precedence:**

- The global constitution (AGENTS.md) applies everywhere; this file adds frontend rules; `DESIGN.md` (per project) carries the actual style parameters. A project repo's own AGENTS.md overrides this file.
- Problem traceability: every rule here cites its origin in the problem catalog (`problem-catalog.md`) via `<!-- Pn, Rn -->` comments. Rules without a problem citation don't belong here.
- One premise governs the whole file: **you are blind**. You cannot see what you render. Every rule below is either a prosthetic eye, or a guardrail that exists because the eye is prosthetic.

---

## 1. You Cannot See Your Output — So Close the Loop

<!-- R2, P4, P6 -->

**Never claim visual quality you haven't observed.**

- After generating or changing anything visual, render it and capture screenshots. Desktop, tablet, phone widths (default: 1440 / 768 / 375). Look at them. Then fix what you see.
- A claim like "the layout is clean" made without a screenshot is fabrication. Say "unverified" or verify.
- When the user reports a visual problem, reproduce it via screenshot first — don't guess from the code and patch blind.
- Self-critique pass before delivery: view each screenshot and name the three worst things in it. Fix at least the worst one.

**The test:** every visual claim in your report is accompanied by the screenshot that backs it. No screenshot, no claim.

---

## 2. Design Tokens Before Pixels

<!-- P1, R1 -->

**Style decisions are made once, in `DESIGN.md`, before any page work. Never improvised per component.**

- If the project has no `DESIGN.md`, produce one first and get it approved: palette, type scale, font stacks, spacing scale, radius, shadow, motion rules. No page code until it exists.
- Every color, font, size, spacing, and radius value in the diff must trace to a token in `DESIGN.md`. Ad-hoc values are bugs.
- If the design seems to require a value `DESIGN.md` doesn't have, that's a `DESIGN.md` amendment — propose it, don't smuggle it in.
- Unless `DESIGN.md` explicitly permits them, these are forbidden outright: CSS gradients, glassmorphism (`backdrop-filter`, translucent panels over imagery), emoji as icons, decorative drop shadows, centered hero + three-feature-card layout, floating animations.
- Adjectives are not design input. "Make it clean and premium" changes nothing — ask for a reference (site, screenshot, brand) or a `DESIGN.md` amendment instead.

**The test:** grep the diff for the forbidden list (must be zero hits — run it mechanically and paste the raw output, a claimed "no hits" is not a check <!-- P18 -->); pick any five style values in the diff and name the token each one came from. Can't name it? It doesn't ship.

---

## 3. Chinese Typography Is a Hard Baseline

<!-- P3, R4 -->

**Latin type metrics do not transfer to CJK. This section is non-negotiable defaults, amendable only via `DESIGN.md`.**

- Font stacks must explicitly include CJK families: `-apple-system, 'PingFang SC', 'Microsoft YaHei', 'Noto Sans SC', sans-serif` (or the project's `DESIGN.md` stack). A bare `serif` or `sans-serif` generic family is forbidden — it silently maps Chinese text to SimSun-class (宋体-class) rendering, which reads as dated and renders poorly at small sizes.
- Body text: minimum 15px, recommended 16px. Line-height for CJK: minimum 1.6. Captions never below 12px.
- Serif/display type for Chinese headings only if `DESIGN.md` says so, with the CJK family named explicitly — never via generic fallback.
- When a design reference is in English, translate its type metrics for CJK: size +1~2px, line-height +0.1~0.2, letter-spacing reset to 0 for Chinese runs.

**The test:** grep all font stacks for bare `serif` / missing CJK families; measure computed body size and line-height against the floor. Both are mechanical checks — do them, don't estimate.

---

## 4. Design for the Space, Not the Screenshot

<!-- P4, P5, P6, R2 -->

**The artifact will be used at every width, with every content length, in every state. The screenshot you show is one point in that space.**

- Responsive behavior is a set of decisions, not a property: for each region, state what collapses, what stacks, what hides, what keeps priority at narrow widths. Write these decisions down before coding.
- Type and spacing are fluid where they can be (`clamp()`, min/max), not stepped-afterthought media queries only.
- **Strings never participate in width calculations.** No fixed-width text containers without an overflow strategy (truncate or wrap, chosen deliberately). Any layout that breaks when copy grows 50% is broken now.
- Every data-driven block ships with four states rendered: loading, empty, error, populated. "No data yet" is a designed state, not a blank hole.
- Interactive elements need hover, focus-visible, and disabled states. Keyboard operability is not a feature, it's the baseline.

**The test:** (a) screenshots at 1440/768/375 — any horizontal overflow is failure; (b) swap in copy at 150% realistic length — layout survives; (c) render all four states of every data block side by side. All three are deliverables, not aspirations.

---

## 5. Content Is Real Before Layout Is Final

<!-- P2, R1, R2 -->

- Never finalize layout against placeholder copy. If real content exists, use it. If it doesn't, ask for it — or use realistic worst-case content (longest plausible strings, realistic data volume), never conveniently short lorem ipsum.
- Fake data must look like real data's worst day: uneven lengths, missing fields, long names. Three identical-length testimonials is a lie that designs the wrong layout.
- Marketing buzzwords are forbidden in product UI (e.g. "empower", "unlock infinite possibilities", "seamless one-stop"; Chinese equivalents: 赋能, 释放无限可能, 一站式). If copy is needed, write what the element actually does.

**The test:** replace any region's content with the real longest variant. If the layout breaks or the change reveals the copy was load-bearing, the layout was never finished.

---

## 6. Restraint: The Page Is Not Your Essay

<!-- P7, P8, R3 -->

**The artifact is for its end user. Your process, your doubts, and your history with this file belong in the conversation — never in the artifact.**

- Word budget: state it before writing copy (default: one core message per screen; no explanatory helper text unless the interaction genuinely cannot express it). Helper text requires justification, not the other way around.
- When you feel the design "hasn't conveyed enough", the fix is form — hierarchy, spacing, iconography, flow — not another sentence. Adding a sentence requires naming why form failed.
- Delete pass before delivery: for every sentence on the page, answer "if I delete this, does the interface already express it?" If yes, delete. Report how many sentences the pass removed.
- Revision notes, changelogs, and "modified because..." annotations go in the chat reply or a separate change-notes file — zero words of process in the artifact itself. The reader of the artifact has never seen any previous version.
- Register belongs to the reader, not the conversation: no memes, inside jokes, or phrasing carried over from the chat. If a phrase only makes sense to someone who was in this conversation, rewrite it for someone who wasn't. <!-- P19 -->
- File naming is fixed by the user or project convention. Never invent versioning suffixes ("final", "final-v2", "final-final"). Versioning is external (git, version tools), not the filename's job.
- No commented-out code, no TODOs in shipped markup — do it now or file an issue.

**The test:** (a) word budget stated and met; (b) grep the artifact for process vocabulary — English ("updated because", "revised", "previous version") and Chinese (由于, 故, 本次, 上一版, 修改) — zero hits (these literals are search targets, since artifacts may be in either language); (c) the delete-pass report exists. If the pass removed nothing, it wasn't performed; (d) every mechanically runnable check ships with its **raw output** (grep results, word counts, screenshots) — a narrative claim of compliance is not evidence. <!-- P18 -->

---

## 7. Small Units, Human Checkpoints

<!-- R2, P10 -->

- Generate in reviewable units (nav, then table region, then form), not whole pages in one shot. Each unit gets the §1 loop before the next begins.
- Checkpoints exist because taste doesn't transfer (P10): layout rhythm, deliberate asymmetry, density — the user owns these. Present options where the choice is aesthetic; don't silently pick the symmetrical default every time.
- Over-polish is a smell: perfectly uniform spacing and symmetry in every region means no decisions were made. If everything is equally emphasized, nothing is.

**The test:** each delivered unit passed through one screenshot loop and one explicit human or self-review before the next unit started.

---

## 8. What Humans Own — Escalation Rules

<!-- P9, P10, R3 -->

Some failure modes cannot be ruled away. They are handled by process, not by effort:

- **Context drift (P9):** when a session has gone long or direction has changed significantly, do not patch the old thread. Squash confirmed decisions into a clean spec and restart. If you notice yourself re-answering old questions or over-agreeing with the latest message, say so — that's the drift signal.
- **Taste calibration (P10):** final judgment on rhythm, asymmetry, and density belongs to the human. Surface these as explicit review items ("check the density of section two") instead of hoping they pass unnoticed.

**The test:** your delivery report ends with a short "human review needed" list. An empty list on a visual deliverable is suspicious.

---

## When Rules Conflict

- §6 (restraint) vs. §4 (completeness of states): the four states are mandatory, but each state's *copy* obeys the word budget. Completeness of states, sparseness of words.
- §2 (token purity) vs. §1 (fix what you see): if the screenshot shows a problem whose fix requires a value not in `DESIGN.md`, stop and propose the token. Never smuggle the fix in as an ad-hoc value, and never ship the visible defect to preserve token purity.
- Any rule here loses to a real project constraint, but only if you name the rule and the constraint out loud.
