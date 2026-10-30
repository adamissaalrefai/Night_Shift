# Week 1 - Intake Review of Runner Branches

**Reviewer:** Nada - Playbook, Detection and Reporting Lead  
**Review date:** 2026-10-30 UTC  
**Reviewed branches:** `origin/week1/mahmoud-benchmark`, `origin/Adam/Week1`  
**Decision:** Do not merge either branch into the shared Week 1 baseline until the issues below are corrected or explicitly accepted by the team.

## Mahmoud branch review

### Received

- AI lab setup guide with Llama 3.1 8B Q4_K_M and Mistral 7B Q4_K_M configuration.
- Ten matching requests and outputs: five task types on Llama and the same five on Mistral.
- Shared settings recorded: Ollama 0.40.11, context size 8192, temperature 0.2, seed 42, and no Modelfile `SYSTEM` line.
- Settings verification screenshot.

### Acceptable for benchmark intake

- The five required task types are covered: log analysis, alert triage, threat summarization, report writing, and IOC extraction.
- All ten output files include an Evaluation Record.

### Required corrections before final acceptance

1. Commit the synthetic evidence files referenced in each request, or revise the evidence manifest so it truthfully states that the evidence exists only inline in the request. Current `/evidence/...` references do not resolve in the branch.
2. Do not mark `Human reviewer decision: Accept` before Nada/Mina have reviewed the output. Change to `Pending cross-review`.
3. Do not mark all checks `Pass` before comparison with Mina's answer key. The runner may record execution metadata, but evidence accuracy and unsupported-claim counts require validation.
4. Align the request/output directory names with the team-agreed lowercase `/ai-requests` and `/ai-outputs` paths, or document a unanimous folder-name decision before merge.

## Adam branch review

### Received

- Five log-analysis prompts and three alert-triage prompts, each with Llama and Phi-3 versions.
- Eight Llama outputs and eight Phi-3 outputs covering log analysis and alert triage.

### Blocking issues

1. All 16 output files are missing the mandatory Evaluation Record. They cannot be scored or accepted as Week 1 benchmark evidence until that record is appended.
2. Adam's submitted work covers only two task types. Phi-3 benchmark coverage is still missing threat summarization, report writing, and IOC extraction.
3. The sampled Phi-3 log-analysis output materially misread the evidence: it described a successful `dev_admin` login as coming from an external IP although the source log states `10.10.10.15`; it used unsupported/incorrect ATT&CK mapping `T1008`; and it claimed no unknowns. Record these in Mina's hallucination log and score the output accordingly.
4. The sampled output did not use the required structured tables or evidence-reference format consistently.

## Integration status

| Input | Status | Next owner |
|---|---|---|
| Llama/Mistral benchmark execution | Received; validation pending | Mina and Nada |
| Phi-3 benchmark execution | Partial; three task types missing | Adam |
| Log-analysis and alert-triage prompt contributions | Received; quality/integration pending | Nada after Adam's output corrections |
| Threat-summary/report-writing prompt contributions | Not received | Nader |
| IOC extraction prompt contribution | Shared template provides output format | Nada |
| Answer key, scoring, and hallucination log | Not received | Mina |

## Merge recommendation

Keep the branches separate. Accept only corrected files through pull requests after Mina's scoring and the required cross-reviews. Do not merge an unreviewed benchmark result into `main`.
