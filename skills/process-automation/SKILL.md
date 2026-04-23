---
name: process-automation
description: Use when the user has a completed architecture-spec.yaml produced by process-architecture-skill and wants to build the actual agent pipeline — model selection per step, Single-Source-of-Truth metadata files, bias-free verifier subagents, quality gates, and feedback-loop wiring. MUST trigger on "implement my architecture", "build the automation", "orchestrate agents for this process", "automation umsetzen", "agenten bauen fuer prozess". Requires architecture-spec.yaml as input. If the spec is missing or incomplete, redirect to process-architecture-skill first.
---

# Process Automation Skill — Status: Under Development

> **This skill is a scaffold. The implementation details are being written based on validation of `process-architecture-skill` on real user processes.**

## Planned purpose

Take a completed `architecture-spec.yaml` from [`process-architecture-skill`](https://github.com/Julian3721/process-architecture-skill) and turn it into a working agent pipeline:

- Select the right model (Haiku / Sonnet / Opus) per step based on `type` (logical / mixed / creative)
- Generate Single-Source-of-Truth metadata JSON files from the `ssot_candidates[]` entries
- Instantiate verifier subagents for every `creative` and high-stakes `mixed` step, following the patterns in [`bias-control-patterns.md`](https://github.com/Julian3721/process-architecture-skill/blob/main/skills/process-architecture/references/bias-control-patterns.md)
- Wire quality gates matching the `severity` and `on_fail` behavior declared in the spec
- Generate the coordinator prompt that orchestrates the pipeline end-to-end
- Emit metadata-consistency tests that enforce SSoT invariants at CI time
- Produce an initial repo layout (directories, scripts, agent definitions, schemas)

## Planned workflow (draft)

1. **Spec validation** — verify the handoff checklist from the architecture skill
2. **Step-by-step agent design** — for each step, decide model + verifier topology + SSoT hookup
3. **SSoT generation** — create initial JSON files with structural schemas
4. **Verifier agent authoring** — generate `.claude/agents/*-verifier.md` files for every gate
5. **Coordinator assembly** — write the top-level skill/orchestrator prompt
6. **Test scaffolding** — emit the metadata-consistency test file with SSoT invariants
7. **Run-the-pipeline rehearsal** — produce one end-to-end run on a small sample input

## Not yet implemented

Workflow templates, agent-generation prompts, SSoT-file emitters, and test-scaffolding logic.

## How to contribute

Until this skill is fleshed out, you can:

- Use `process-architecture-skill` to produce specs
- Manually implement pipelines following the patterns catalogued there
- Share real-world spec → implementation examples as issues on this repo to seed the scaffolding patterns

Once `process-architecture-skill` has been validated on 5+ real user processes and the spec format has stabilized, this skill will be built out to mechanically convert spec → working pipeline.
