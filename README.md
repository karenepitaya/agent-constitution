# agent-constitution

Behavioral rules for coding agents, packaged as a skill that loads a short core and pulls in a scenario file only when the task needs it.

## Layout

```
AGENTS.md                              pointer (this repo only)
skills/agent-constitution/
  SKILL.md                             core, always loaded when the skill triggers
  frontend.md  office.md  backend.md   scenario files, read when routed
  testing.md   ops.md
  DESIGN.template.md                   copy into a project as DESIGN.md
  problem-catalog.md                   trace or edit rules; not for doing the task
```

`AGENTS.md` at the repo root is only a pointer. The core lives in `SKILL.md` so the installed skill and this repo cannot drift into two constitutions. Scenario files extend the core and never repeal it. A project's own `AGENTS.md` overrides both. A project's `DESIGN.md` replaces the frontend forbidden list; that list is the default only when no `DESIGN.md` exists.

`problem-catalog.md` is the problem archive (symptoms, mechanism, detection). Every rule cites an id from it (`<!-- P3, R4 -->`). Open it to trace or change a rule, not to execute one.

## Install

Copy or symlink the skill folder:

- One project: `.cursor/skills/agent-constitution`
- Every project: `~/.cursor/skills/agent-constitution`

The `description` in `SKILL.md` is the trigger: writing or changing code, UI, pages, HTML, prototypes, slides, docs, reports, email, Chinese copy, APIs, databases, services, tests, deploys, CI, or environments, and editing these rules.

## Use

1. The agent reads `SKILL.md` (decide, simplicity, surgical edits, verified goals, what the reply may contain).
2. It reads the scenario file the routing table names:
   - pages, UI, HTML, slides, prototypes → `frontend.md` and the project's `DESIGN.md` (if there is none, start from `DESIGN.template.md`)
   - docs, reports, email, Chinese copy → `office.md`
   - APIs, databases, services → `backend.md`
   - tests, acceptance → `testing.md`
   - deploy, env, CI → `ops.md`
3. Slides: `office.md` owns the words, `frontend.md` the pixels.
4. Raw check output (screenshots, grep, word counts) goes in `checks.md` or an attachment. The reply states what was delivered, what was verified, and the human-review list.

Each rule is three parts: the rule, a one-line why, and **The test:** (the mechanical check).

Reversible, in-scope choices are decided, with a one-line reason. Ask only to delete, send, or pay; when ambiguity changes the result; or for taste. One question, with a recommendation. On a visual taste call, `frontend.md` caps that at two options plus a recommendation.

## Sizes

Bytes of the files an agent actually loads:

| File | Bytes | Target |
| --- | ---: | --- |
| `SKILL.md` | 2054 | ~2KB |
| `frontend.md` | 4123 | ~3–4KB |
| `office.md` | 3970 | ~3–4KB |
| `backend.md` | 4788 | ~3–4KB |
| `testing.md` | 3600 | ~3–4KB |
| `ops.md` | 3367 | ~3–4KB |

`backend.md` is past 4KB. It holds twelve rules, each with a why and a test; cutting further dropped constraints. `SKILL.md` is 6 bytes over 2048 so the headings stay separated. `frontend.md` is 27 bytes over 4096 for the same reason, and so the forbidden-list tokens stay `hero+3 feature cards` and `floating animations` rather than fragments that grep would misread.
