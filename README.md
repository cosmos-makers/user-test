# user-test

Multi-agent User Testing plugin for Claude Code.

Spawn persona agents to test your spec, web service, or API — then compile actionable feedback into a structured report.

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
- `.user-test/report-{timestamp}.md` — Feedback report with prioritized recommendations

## License

MIT
