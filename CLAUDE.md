# CLAUDE.md — process-automation-skill

## What this repo will be

The companion to [`process-architecture-skill`](https://github.com/Julian3721/process-architecture-skill). It takes a completed `architecture-spec.yaml` (produced by the architecture skill) and builds the actual agent pipeline:

- **Model selection per step** — Haiku for logical, Sonnet for mixed, Opus for creative, based on the spec's `type` field.
- **SSoT generation** — creates the initial JSON metadata files from `ssot_candidates[]`, with the right structure (pure-label, dual-field, thresholds, aliases, composite).
- **Verifier subagent authoring** — generates `.claude/agents/*-verifier.md` files for every creative/high-stakes-mixed step, applying the bias-control patterns named in the spec.
- **Quality-gate wiring** — implements the `severity` / `on_fail` behavior from the spec (quarantine, auto-fix, escalate, retry-once).
- **Feedback-loop instantiation** — turns `feedback_loops[]` entries into explicit retry/quarantine logic in the coordinator.
- **Coordinator prompt assembly** — writes the top-level skill prompt that orchestrates the whole pipeline.
- **Metadata-consistency test scaffolding** — emits a test file that enforces SSoT invariants at CI time (ID parity, threshold ordering, pure-label purity).

## Origin context (read this before touching anything)

This skill is the planned second half of a two-skill system. The first half — [`process-architecture-skill`](https://github.com/Julian3721/process-architecture-skill) — was built first and shipped. This skill is **deliberately a scaffold** right now because its implementation details depend on:

1. The architecture skill being validated on real user processes (not just paper).
2. The `architecture-spec.yaml` format being stabilized (it's likely to drift during first validations).
3. A handful of worked "spec → pipeline" examples that let us see which parts of the conversion are mechanical vs. judgment-dependent.

Building this skill before those things exist would freeze assumptions that turn out to be wrong.

**The methodology itself is already documented** in the architecture skill's `references/bias-control-patterns.md` (48 principles with Rangello source citations). This skill's job is to *apply* those patterns automatically based on a spec — not to re-derive them.

## Origin story (critical context)

Like the architecture skill, this repo traces back to the **Rangello** daily estimation game project (`/Users/juliandomnik/Projects_2026/Rangello/`), specifically its question-generator pipeline. Rangello is the reference implementation for every pattern this skill will automate.

**When in doubt about what the generated pipeline should look like, check Rangello's `.claude/skills/question-generator/SKILL.md`** — it's an existing, working example of what the output of this skill should resemble.

## Repo layout (current — scaffold)

```
process-automation-skill/
├── skills/
│   └── process-automation/
│       └── SKILL.md                ← frontmatter + planned-workflow outline only
├── README.md                       ← English, scaffold status + pointer to architecture skill
├── README.de.md                    ← German, same
├── LICENSE                         ← MIT
└── .gitignore
```

## Repo layout (planned — after build-out)

```
process-automation-skill/
├── skills/
│   └── process-automation/
│       ├── SKILL.md                              ← orchestration workflow
│       ├── templates/
│       │   ├── verifier-agent.md.tmpl            ← template for verifier subagent files
│       │   ├── coordinator-prompt.md.tmpl        ← template for coordinator skill
│       │   ├── ssot-schemas/                     ← per-structure-hint schemas
│       │   │   ├── labels-only.schema.json
│       │   │   ├── dual-field.schema.json
│       │   │   ├── thresholds.schema.json
│       │   │   ├── aliases.schema.json
│       │   │   └── composite.schema.json
│       │   └── metadata-consistency-test.ts.tmpl ← invariant test scaffold
│       ├── references/
│       │   ├── model-selection.md                ← how type → model mapping works
│       │   ├── verifier-topology.md              ← when one vs many verifiers, which models
│       │   └── spec-validation.md                ← how to verify input spec before conversion
│       └── scripts/
│           ├── validate-spec.js                  ← validates architecture-spec.yaml schema
│           ├── generate-ssot.js                  ← spec → ssot json files
│           ├── generate-verifier.js              ← spec step → verifier agent md
│           └── generate-coordinator.js           ← spec → coordinator skill
├── examples/
│   └── rangello-like/                            ← worked example: a spec similar to Rangello, converted
└── ...
```

## Planned workflow (draft — will be refined after validation)

1. **Spec validation.** Run `validate-spec.js` against the input `architecture-spec.yaml`. Enforce the handoff-gate checklist from the architecture skill. Refuse to proceed if the spec fails.
2. **Step-by-step agent design.** For each step in the spec, decide:
   - Model tier (Haiku / Sonnet / Opus) based on `type`
   - Verifier topology (none / one / three-parallel) based on `severity` and `creative_components`
   - SSoT hookup (which SSoT files does this step read/write?)
3. **SSoT generation.** For each entry in `ssot_candidates[]`, generate the initial JSON file using the matching schema template. Leave entities empty (the user fills them later) but structure is fixed.
4. **Verifier agent authoring.** For each verifier, generate the `.claude/agents/*-verifier.md` file using the template. Embed the correct bias-control patterns as named in the spec's `creative_components[].bias_risks[].mitigation`.
5. **Coordinator assembly.** Write the top-level skill prompt. Orchestrates: read inputs → run steps in dependency order → invoke verifiers at gates → handle feedback loops per spec → emit output.
6. **Test scaffolding.** Emit `metadata-consistency.test.ts` with one invariant per SSoT entry.
7. **Run-the-pipeline rehearsal.** Execute one end-to-end run on a small sample input to catch wiring errors.

## Current status

- **Scaffold only.** `skills/process-automation/SKILL.md` is a descriptive placeholder, not a functional skill.
- **Initial commit shipped** (`4777440`). Public, MIT, EN + DE READMEs.
- **No implementation work should start** until `process-architecture-skill` has been dog-fooded on Rangello and validated on ≥3 real user processes from distinct domains. Premature implementation here will freeze assumptions that turn out to be wrong.

## Dependencies on the architecture skill

This skill cannot be stabilized until:

- [ ] `process-architecture-skill` has been run against Rangello (can its output reverse-engineer into a valid Rangello spec?).
- [ ] 3+ user interviews completed with `process-architecture-skill`, each producing a spec.
- [ ] Those specs have been hand-converted to pipelines (the manual "what would this skill emit?" exercise).
- [ ] From that set, the conversion rules are mechanical enough to codify.

Only then should this repo's implementation start.

## Work queue (post-validation)

| Order | Work | Inputs needed |
|---|---|---|
| 1 | Spec-schema formalization | Stable architecture-spec.yaml from validation runs |
| 2 | `validate-spec.js` | Schema + handoff-gate checklist (from architecture-skill's `interview-checklist.md`) |
| 3 | Model-selection logic | `model-selection.md` decision table |
| 4 | Verifier-topology logic | `verifier-topology.md` decision table |
| 5 | SSoT schema templates | One per structure_hint value |
| 6 | Verifier-agent template | Based on Rangello's `.claude/agents/fact-verifier-*.md` and `content-policy-verifier.md` |
| 7 | Coordinator template | Based on Rangello's `question-generator/SKILL.md` structure |
| 8 | Metadata-consistency-test template | Based on Rangello's `src/lib/__tests__/metadata-consistency.test.ts` |
| 9 | End-to-end rehearsal | One worked example (probably Rangello reproduction) |
| 10 | README + docs update | After all above are done |

## Conventions in this codebase (planned)

- **Every template embeds SSoT data via code-level substitution**, never via copy-paste. The template has `{{PLACEHOLDER}}` markers; `generate-*.js` scripts hydrate from the spec.
- **Every generated file carries a provenance comment** — which spec file it came from, which commit of the spec, and which version of this skill generated it. That way regenerations are deterministic and re-entrant.
- **Never generate code that paraphrases a spec field.** If a spec has `verification_criterion: "..."`, the generated verifier embeds that string verbatim. Paraphrase drift is the single most common silent-failure mode (see architecture skill's `references/bias-control-patterns.md` Principle #2).
- **English-only internal artifacts** — same rule as the architecture skill. IDs, comments, template variable names, commit messages all English.

## For future Claude sessions working on this repo

1. **Before doing any implementation work, check whether the prerequisites are met.** If `process-architecture-skill` hasn't been validated yet, stop and validate it first. Implementation before validation will be thrown away.
2. **Rangello is the reference implementation.** When building the verifier-agent template, read the Rangello `fact-verifier-*.md` files. When building the coordinator template, read Rangello's `question-generator/SKILL.md`. When building the SSoT-generation logic, read Rangello's `data/*.json` files and `src/lib/__tests__/metadata-consistency.test.ts`.
3. **Keep the companion skill's spec format stable once you start implementing.** Breaking changes to `architecture-spec.yaml` invalidate already-generated pipelines. Version the spec schema if you must change it.
4. **No premature abstraction.** The planned workflow above is a draft. After 3–5 real spec → pipeline conversions, you'll see which parts are actually mechanical. Code only those.
5. **Publish regenerated pipelines alongside the skill.** Each example in `examples/` should be a real, committed conversion: input spec, output pipeline, and any manual fix-ups needed. These are the regression suite.

## Key external references

- Companion skill (the spec producer): [`process-architecture-skill`](https://github.com/Julian3721/process-architecture-skill) — locally at `/Users/juliandomnik/Projects_2026/Process-Skills/process-architecture-skill/`
- Rangello repo (reference implementation): `/Users/juliandomnik/Projects_2026/Rangello/`
  - Most relevant files: `.claude/skills/question-generator/SKILL.md`, `.claude/agents/fact-verifier-*.md`, `data/*.json`, `src/lib/__tests__/metadata-consistency.test.ts`

## Relationship to the architecture skill

These two skills must co-evolve. Rules:

- **New spec field?** Add it to the architecture skill's interview and `interview-checklist.md` first. This skill follows.
- **Spec field not used here?** Propose removal from architecture skill. Don't let dead weight accumulate.
- **Pattern added to `bias-control-patterns.md`?** Check whether any template here should know about it. Usually yes.
- **Breaking spec-schema change?** Bump a `spec_version` field; both skills must check compatibility.
