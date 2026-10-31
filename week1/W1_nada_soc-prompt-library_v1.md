# Week 1 - Consolidated SOC Prompt Library

**Owner:** Nada - Playbook, Detection and Reporting Lead  
**Version:** v1.1
**Status:** Consolidated library; prompt sources received and catalogued. Final cross-review remains pending.
**Authoritative common prompt:** `template/IR_Capstone_Shared_AI_Request_and_SOC_Prompt_v1.md`

## Use rules

1. Start every request with the AI Request metadata, execution assignment, evidence manifest, and the full shared SOC Analyst prompt v1.0.
2. Select only one task output format per request.
3. Paste the authorized evidence in the request file or cite its exact repository-relative path.
4. Do not use scenario context as evidence.
5. Use Llama 3.1 8B Q4_K_M, Ollama, 8192 context, temperature 0.2, and seed 42 for real work. Week 1 comparison runs additionally use Mistral 7B and Phi-3 Mini with identical settings.

## Prompt catalogue

| Library ID | Task | Owner | Required contribution | Integration status |
|---|---|---|---|---|
| PL-01 | Log analysis | Adam | Five tested Phi-3 prompt trials | Catalogued from `origin/Adam/Week1:week1/Prompts/phi3_log_analysis_prompts/` |
| PL-02 | Alert triage | Adam | Three tested Phi-3 prompt trials | Catalogued from `origin/Adam/Week1:week1/Prompts/phi3_alert_triage_prompts/` |
| PL-03 | Threat summarization | Nader | One shared task prompt | Integrated from `week1/nader-threat-summarization-prompt.md` |
| PL-04 | Report writing | Nader | One shared task prompt | Integrated from `week1/nader-report-writing-prompt.md` |
| PL-05 | IOC extraction | Nada | Output format in shared template | Ready in v1.0 |

## Integration checklist

- [x] Common evidence-handling rules consolidated.
- [x] Five shared task types defined.
- [x] Required task-specific output formats consolidated.
- [x] Week 1 reproducibility settings recorded.
- [x] Adam's log-analysis prompt set received on remote branch.
- [x] Adam's alert-triage prompt set received on remote branch.
- [ ] Adam adds Evaluation Records to matching outputs; they remain trial outputs until reviewed.
- [x] Nader's threat-summary prompt received and integrated.
- [x] Nader's report-writing prompt received and integrated.
- [ ] Team confirms the final library has no duplicate or conflicting instructions.
- [ ] Mina cross-reviews this consolidated library.

## Required contributor handoff format

Each contribution must include:

- Library ID and task name.
- Prompt text exactly as tested.
- Purpose, accepted input type, expected output, and known limits.
- One matching benchmark request ID and output ID.
- Model/settings used.
- Any observed failure mode or refinement note.

## IOC extraction entry - PL-05

Use output format **E. IOC EXTRACTION** in the shared SOC Analyst prompt. The model must return a deduplicated IOC table, distinguish `observed` from `scenario_context_only`, preserve evidence references, and state extraction limitations. No additional instructions are needed beyond the shared prompt unless a test case specifies its own expected IOC types.

## Integrated task modules

The full common prompt and required output formats remain authoritative in `template/IR_Capstone_Shared_AI_Request_and_SOC_Prompt_v1.md`. The following task modules are used with that common prompt, not instead of it.

| Task | Module source | Required focus |
|---|---|---|
| Log analysis | Adam's five prompt trials | Evidence-referenced timeline, findings, IOCs, data gaps, and P1-P3 actions. |
| Alert triage | Adam's three prompt trials | Disposition, severity, supporting/weakening evidence, escalation, and confidence. |
| Threat summarization | Nader's prompt | Maximum 150-word summary; separate confirmed, suspected, scenario-only, and unknown activity. |
| Report writing | Nader's prompt | Evidence-scoped report with confirmed versus potential impact and no completed-action claims. |
| IOC extraction | Shared template output E | Deduplicated IOCs; observed versus scenario-context-only status. |

Adam's trial prompts remain useful examples, but their outputs require Evaluation Records before they can be treated as reviewed benchmark evidence.

## Release conditions

Publish as `W1_nada_soc-prompt-library_final.md` only after all five catalogue entries are present, linked to tested request/output pairs, and Mina has completed cross-review.
