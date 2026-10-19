# AGENTS.office.md — Document & Office Work Constitution (Meta)

Behavioral rules for document-producing work: reports, Word/Markdown deliverables, proposals, memos, and slide content. Independent of toolchain.

**Scope and precedence:**

- The global constitution (AGENTS.md) applies everywhere; this file governs document **content, structure, and lifecycle**. Visual and layout aspects of pages and slides are governed by `AGENTS.frontend.md` — when both apply (e.g. slides), this file owns the words, that file owns the pixels.
- Problem traceability: rules cite their origin in `problem-catalog.md` via `<!-- Pn, Rn -->`. Rules without a citation don't belong here.
- One premise governs the whole file: **the artifact is a deliverable to a third party who has never seen this conversation.** It must stand alone, say things once, and contain zero process.

---

## 1. The Reader Has Never Seen This Conversation

<!-- P8, R3 -->

- The document carries no meta-commentary: no references to previous versions, prompts, discussions, or decisions made along the way. It reads as if it was written once, deliberately, for its reader.
- Never address the requester inside the artifact ("as you asked", "per your feedback"). The artifact has an audience; the conversation has a participant. Don't mix them.
- Background a reader needs is stated inside the document; context only you and the requester share stays in the conversation.
- Register belongs to the reader, not the conversation: no memes, inside jokes, or shorthand carried over from the chat. Any phrase whose meaning depends on having seen the conversation is rewritten for the third-party reader. After each incident, the caught words join the §4 banned list. <!-- P19 -->

**The test:** hand the artifact to someone who knows nothing about its production. Any sentence that confuses them because it references the process is a violation.

---

## 2. Versioning Is External

<!-- P8, R3 -->

**There is only ever one artifact: the current one. History lives outside it.**

- File naming follows the user's instruction or project convention — a fixed name, versioned by git / version tools / a dated folder, never by filename suffix. Forbidden suffix vocabulary: "revised", "final", "final-v2", "final-final", "结案", "修订版", "最终版" and their cousins.
- On revision: produce the clean next version. What changed and why goes in the chat reply or a separate change-notes file if the user asks for one — zero words of it in the artifact body.
- No inline revision marks ("changed from X to Y because...", "previously this said...") unless the user explicitly requests a redline/track-changes deliverable — that's a distinct artifact type, produced on request, not by default.

**The test:** (a) the filename contains no version vocabulary; (b) grep the body for process vocabulary — English ("revised", "updated because", "previous version") and Chinese (由于, 故, 本次, 上一版, 修改) as search literals — zero hits.

---

## 3. Restraint: Every Sentence Earns Its Place

<!-- P7, R3 -->

- No throat-clearing: documents don't open with "in today's fast-paced world" or restate the assignment. The first sentence already says something.
- Say it once. No paragraph that re-says the previous paragraph with different adjectives; no conclusion that repeats the introduction; no summary of a summary.
- Headings carry information. "Analysis" is a label; "Retention drops after week three" is a heading. Where the genre allows, headings state conclusions, not topics.
- Word budget: state the target length before writing, then meet it. Padding to look thorough is chattiness.
- Delete pass before delivery: for each paragraph, "if deleted, what information is lost?" If the answer is nothing, delete it. Report the removal count.

**The test:** (a) word budget stated and met; (b) the delete-pass report exists — a pass that removed nothing wasn't performed; (c) every heading states a claim or names a concrete object, not a vague theme; (d) mechanically runnable checks (word counts, greps) ship with **raw output** — narrative compliance is not evidence. <!-- P18 -->

---

## 4. Plain Language: No Black Speech

<!-- P11, R1 -->

**Professional means precise, not opaque. Two banned species:**

- **Borrowed jargon** — words whose function is to signal professionalism rather than carry information. Banned-list literals for grep (extend per project): English — *leverage, synergy, seamless, empower, robust, best-in-class, unlock, elevate, holistically*; Chinese — *赋能, 抓手, 闭环, 颗粒度, 对齐, 打法, 心智, 链路* (used loosely; legitimate technical uses with fixed meanings — e.g. 链路 in a tracing system — are fine, the define test below decides).
- **Self-coined pseudo-terminology** — never invent framework/methodology/metric names that sound established. If a concept needs a name and no standard term exists, coin it explicitly ("we call this X") and define it on first use. A coined term presented as if the reader should already know it is fabrication of authority.
- Prefer concrete verbs over abstract nominalizations: "cut review time by half" over "realize efficiency enablement". Every abstract claim must be cashable into a concrete fact or example on demand.
- Adjectives and adverbs carry a burden of proof: if deleting one loses no information, delete it.

**The test:** (a) grep the banned list — zero hits or justified exceptions, and the raw grep output is attached (a claimed "no hits" is not a check <!-- P18 -->); (b) the define test: pick any five abstract terms from the document, give a one-sentence concrete definition for each — any failure means rewrite; (c) the deletion test on adjectives.

---

## 5. Structure: Conclusion First

<!-- R1 -->

**Documents are read top-down by busy people. Structure is a service, not a ritual.**

- The document's first paragraph states its conclusion or recommendation. Support follows. No mystery-novel structure that saves the point for the end.
- One message per section; one message per slide. If a section carries two messages, split it.
- Slide titles are assertions, not topics: "Churn concentrated in the first 30 days", not "Churn Analysis". A reader skimming only titles should reconstruct the argument.
- Numbers before adjectives. "Response time p99 = 240ms" is a sentence; "blazing fast" is noise.

**The test:** read only the first paragraph plus all headings/slide titles — the argument must reconstruct. If it doesn't, the structure is decoration.

---

## 6. Facts Must Exist

<!-- P12, R1, R2 -->

- Every number, quote, citation, standard number, and named entity comes from user-provided material or a verifiable source you actually accessed. Never from memory, never "typically", never invented.
- A gap in the material is stated as a gap: "this figure is not in the provided data" — never silently filled with plausible fiction. Where an estimate is genuinely useful, label it as an estimate with its derivation.
- If sources can't be verified in the current session, mark the claim `[unverified]` and list it in the delivery notes. "Probably true" is not a status.
- Charts and tables must reconcile with the body text and with each other — same number, same value, everywhere it appears.

**The test:** the source test — pick any five factual claims, name the source of each. Any failure is a stop-and-fix, not a caveat in small print.

---

## 7. Audience and Register

<!-- R3 -->

- Before writing, state the reader assumption: who they are, what they know, what they'll do with the document. Vocabulary and depth follow from that statement.
- Match the reader's vocabulary: domain terms the audience actually uses are precision, not jargon (§4's define test draws the line).
- No sycophantic openers or closers inside the artifact. No "I hope this helps". The document is not a message from you.
- Hedge once, precisely, where uncertainty is real — "based on Q3 data only" — never hedging stacks ("it could perhaps potentially be argued").

**The test:** the reader assumption is stated in the delivery notes; the register is consistent from first page to last.

---

## When Rules Conflict

- §3 (brevity) vs. completeness: structure carries completeness (§5), sentences stay lean. If something must be said, it gets a section — not a longer paragraph.
- §4 (plain language) vs. domain precision: terminology with a fixed meaning in the reader's field is allowed. The define test is the judge — it passes real terminology and catches black speech.
- §5 (conclusion first) vs. genre: narrative genres exist. If the deliverable is one, say so and state where the conclusion lives instead.
- Any rule here loses to a real project constraint, but only if you name the rule and the constraint out loud.
