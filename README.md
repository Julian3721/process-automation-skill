# process-automation-skill

> **Status — under development.** See [`process-architecture-skill`](https://github.com/Julian3721/process-architecture-skill) first.

> Lesen auf Deutsch? → [README.de.md](./README.de.md)

---

## What this will be

The companion skill to [`process-architecture-skill`](https://github.com/Julian3721/process-architecture-skill). It takes a completed `architecture-spec.yaml` and builds the actual agent pipeline:

- Model selection per step (Haiku / Sonnet / Opus)
- Single-Source-of-Truth metadata JSON files
- Bias-free verifier subagents for creative steps
- Quality gates with quarantine / retry / escalate behavior per spec
- Feedback-loop wiring
- Metadata-consistency tests enforcing SSoT invariants

## Current status

Scaffolding only. The architecture skill is being validated on real user processes first. Once the spec format stabilizes, this skill will be built out to mechanically convert spec → working pipeline.

## What you can do today

1. Use [`process-architecture-skill`](https://github.com/Julian3721/process-architecture-skill) to produce a complete `architecture-spec.yaml`.
2. Implement the pipeline manually, following the patterns catalogued in the architecture skill's [`bias-control-patterns.md`](https://github.com/Julian3721/process-architecture-skill/blob/main/skills/process-architecture/references/bias-control-patterns.md).
3. Open an issue here with your spec + your implementation — early case studies will shape how this skill automates the conversion.

## Installation

Not recommended yet — the skill is a scaffold.

When the skill is fleshed out, installation will follow the standard Claude Code skill pattern (drop into `.claude/skills/` or `~/.claude/skills/`).

## License

MIT. See [LICENSE](./LICENSE).

---

## Sister skill

[`process-architecture-skill`](https://github.com/Julian3721/process-architecture-skill) — the interview-driven half that produces the spec this skill will consume.
