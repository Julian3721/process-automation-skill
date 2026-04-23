# process-automation-skill

> **Status — in Entwicklung.** Nutze zuerst [`process-architecture-skill`](https://github.com/Julian3721/process-architecture-skill).

> Read in English → [README.md](./README.md)

---

## Was das werden wird

Das Schwester-Skill zu [`process-architecture-skill`](https://github.com/Julian3721/process-architecture-skill). Es nimmt eine fertige `architecture-spec.yaml` und baut daraus die tatsächliche Agenten-Pipeline:

- Modell-Auswahl pro Schritt (Haiku / Sonnet / Opus)
- Single-Source-of-Truth Metadaten-JSON-Files
- Bias-freie Verifier-Subagenten für kreative Schritte
- Quality-Gates mit Quarantäne / Retry / Eskalations-Verhalten laut Spec
- Feedback-Loop-Verdrahtung
- Metadata-Consistency-Tests, die SSoT-Invarianten erzwingen

## Aktueller Status

Nur Scaffold. Das Architecture-Skill wird zuerst an echten Prozessen validiert. Sobald das Spec-Format stabilisiert ist, wird dieses Skill so ausgebaut, dass es Spec → funktionierende Pipeline mechanisch konvertiert.

## Was du heute schon machen kannst

1. Mit [`process-architecture-skill`](https://github.com/Julian3721/process-architecture-skill) eine vollständige `architecture-spec.yaml` erzeugen.
2. Die Pipeline manuell umsetzen, anhand der Muster in der [`bias-control-patterns.md`](https://github.com/Julian3721/process-architecture-skill/blob/main/skills/process-architecture/references/bias-control-patterns.md) des Architecture-Skills.
3. Ein Issue hier aufmachen mit deiner Spec + Umsetzung — frühe Fallstudien formen, wie dieses Skill später die Konvertierung automatisiert.

## Installation

Noch nicht empfohlen — das Skill ist ein Scaffold.

Sobald es fertig ist, folgt die Installation dem Standard-Claude-Code-Skill-Muster (ins `.claude/skills/` oder `~/.claude/skills/` legen).

## Lizenz

MIT. Siehe [LICENSE](./LICENSE).

---

## Schwester-Skill

[`process-architecture-skill`](https://github.com/Julian3721/process-architecture-skill) — die interview-getriebene Hälfte, die die Spec produziert, die dieses Skill konsumieren wird.
