# Week 1 - Consolidated SOC Prompt Library

**Owner:** Nada - Playbook, Detection and Reporting Lead  
**Version:** v1.0  
**Status:** Integration baseline. The shared prompt is complete; task-prompt contributions await their owners.  
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
| PL-01 | Log analysis | Adam | 3-5 tested prompts | Received on `Adam/Week1`; pending mandatory Evaluation Records and quality review |
| PL-02 | Alert triage | Adam | 3-5 tested prompts | Received on `Adam/Week1`; pending mandatory Evaluation Records and quality review |
| PL-03 | Threat summarization | Nader | Tested prompt(s) | Awaiting Nader's reviewed contribution |
| PL-04 | Report writing | Nader | Tested prompt(s) | Awaiting Nader's reviewed contribution |
| PL-05 | IOC extraction | Nada | Output format in shared template | Ready in v1.0 |

## Integration checklist

- [x] Common evidence-handling rules consolidated.
- [x] Five shared task types defined.
- [x] Required task-specific output formats consolidated.
- [x] Week 1 reproducibility settings recorded.
- [x] Adam's log-analysis prompt set received on remote branch.
- [x] Adam's alert-triage prompt set received on remote branch.
- [ ] Adam adds Evaluation Records to matching outputs; Nada completes quality review before integration.
- [ ] Nader's reviewed threat-summary prompt set received and inserted.
- [ ] Nader's reviewed report-writing prompt set received and inserted.
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

## Release conditions

Publish as `W1_nada_soc-prompt-library_final.md` only after all five catalogue entries are present, linked to tested request/output pairs, and Mina has completed cross-review.
