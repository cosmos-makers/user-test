# User Test

A Claude Code plugin that runs **multi-agent user testing**. Spawn AI persona agents — each with distinct demographics, behaviors, and a personal diary — to test your spec, web service, or API. Get a structured feedback report with prioritized recommendations.

## Install

```bash
claude plugin marketplace add cosmos-makers/user-test
claude plugin install user-test@user-test
```

## Commands

| Command | Description |
|---------|-------------|
| `/user-test:setup [preset\|custom]` | Configure persona group (preset or custom) |
| `/user-test:start <target>` | Run UT with configured personas → feedback report |
| `/user-test:enhance [report]` | Apply report recommendations to code/spec |
| `/user-test:run <target> [preset]` | One-shot: setup → start → enhance |

## UT Modes

Automatically detected by target type:

- **spec** — Read and review documents, specs, or codebases
- **web** — Interact with live web services via browser (requires chrome MCP)
- **api** — Call API endpoints directly

## Presets

| Preset | Description |
|--------|-------------|
| `mz-2030` | 20-30s digital natives (3 personas) |
| `office-worker-40s` | 40s office workers, varied IT proficiency (3 personas) |
| `senior` | 50s+ seniors, accessibility focus (3 personas) |
| `diverse-mix` | Age/gender/proficiency balanced mix (4 personas) |

## Persona Schema

```json
{
  "id": "unique-id",
  "label": "Display Name",
  "demographics": { "age": 27, "gender": "male", "region": "서울", "income": "중상", "occupation": "개발자" },
  "behavioral": { "domain_expertise": "high", "it_proficiency": "heavy_user", "competitor_experience": [], "current_workaround": "" },
  "psychographic": { "tech_adoption": "early_adopter", "motivation": "", "usage_context": "" }
}
```

## Output

- `.user-test/personas.json` — Configured persona group
- `.user-test/diaries/{id}.md` — Persona diary (generated during setup)
- `.user-test/report-{timestamp}.md` — Feedback report with prioritized recommendations

## v1.1 Improvements (2026-03-22)

Research-backed enhancements to improve persona agent quality and realism.

### 1. Perspective-Taking Prompting

Before evaluation, each agent explicitly enters the persona's perspective by answering: "What situation would I encounter this service in?", "What's my current state?", "What catches my eye in 3 seconds?"

This technique is shown to improve Theory-of-Mind capabilities in LLMs.

### 2. Anti-Stereotype Guardrail

Agent prompts now include explicit instructions to base responses on **defined behavioral traits and experiences**, not demographic stereotypes. For example, a 52-year-old is not assumed to be bad with technology — instead, the agent references their specific `it_proficiency: light_user` and `current_workaround: phone/in-person` attributes.

### 3. Chain-of-Persona Self-Reflection

After completing all evaluations, each agent performs a consistency check: re-reads their profile, verifies each response aligns with their defined attributes, corrects inconsistencies, and assigns a `persona_consistency` score (1-5). Low scores are flagged in the report.

### 4. Persona Diary Generation

During setup, each persona gets an auto-generated "yesterday's diary" — a 300-500 character first-person journal entry reflecting their daily life, digital service usage patterns, and frustrations. This provides vivid experiential context that makes test responses more grounded and realistic.

Diaries are stored in `.user-test/diaries/{id}.md` and automatically loaded as context during testing.

## Research References

These improvements are grounded in the following academic research on LLM role-playing, persona simulation, and character consistency:

| # | Paper | Year | Venue | Applied In |
|---|-------|------|-------|------------|
| 1 | Shanahan et al. "Role Play with Large Language Models" | 2023 | Nature | Theoretical foundation |
| 2 | Shao et al. "Character-LLM: A Trainable Agent for Role-Playing" | 2023 | EMNLP | Diary (experience replay) |
| 3 | Park et al. "Generative Agents: Interactive Simulacra of Human Behavior" | 2023 | UIST | Memory/reflection architecture |
| 4 | Gupta et al. "Bias Runs Deep: Implicit Reasoning Biases in Persona-Assigned LLMs" | 2024 | ICLR | Anti-stereotype guardrail |
| 5 | Zheng et al. "Quantifying Global Faithfulness in Persona-Driven Role-Playing" | 2024 | NeurIPS | Consistency scoring (APC) |
| 6 | Cai et al. "Persona-Aware Contrastive Learning for Persona Consistency" | 2025 | ACL | Chain-of-Persona |
| 7 | "UXAgent: An LLM Agent-Based Usability Testing Framework" | 2025 | CHI | Dual-system cognitive model |
| 8 | "Think Twice: Perspective-Taking Improves LLMs' ToM Capabilities" | 2023 | arXiv | Perspective-taking prompting |
| 9 | Kim et al. "Persona is a Double-Edged Sword" | 2024 | arXiv | Ensemble validation |
| 10 | "Human Simulacra: A Step toward Personification of LLMs" | 2024 | arXiv | Diary (life story generation) |

Full survey covers 23 papers on LLM role-playing, persona simulation, and character consistency.

## License

MIT
