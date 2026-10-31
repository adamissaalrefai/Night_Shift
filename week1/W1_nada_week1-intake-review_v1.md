# Week 1 - Intake Review of Runner Branches

**Reviewer:** Nada - Playbook, Detection and Reporting Lead  
**Review date:** 2026-10-31 UTC
**Reviewed branches:** `origin/week1/mahmoud-benchmark`, `origin/Adam/Week1`  
**Decision:** Intake rechecked after updated runner pushes. Keep raw artifacts on their source branches until cross-review; use the benchmark registry for consolidated tracking.

## Mahmoud branch review

### Received

- AI lab setup guide with Llama 3.1 8B Q4_K_M and Mistral 7B Q4_K_M configuration.
- Ten matching requests and outputs: five task types on Llama and the same five on Mistral.
- Shared settings recorded: Ollama 0.40.11, context size 8192, temperature 0.2, seed 42, and no Modelfile `SYSTEM` line.
- Settings verification screenshot.

### Acceptable for benchmark intake

- The five required task types are covered: log analysis, alert triage, threat summarization, report writing, and IOC extraction.
- All ten output files include an Evaluation Record.

### Remaining conditions before final acceptance

1. The referenced synthetic evidence files are now present in the branch.
2. Human-review fields are now marked `Pending`, which is correct until review is complete.
3. Runner pre-check `Pass` entries are not final validation. Mina's answer-key scoring must determine evidence accuracy, unsupported-claim counts, and final acceptance.
4. The branch still uses `Requests/Ai-Requests` and `outputs/Ai-Outputs`, not the agreed lowercase queue paths. Preserve the source artifacts; use the shared naming convention for future files.

## Adam branch review

### Received

- Five log-analysis prompts, three alert-triage prompts, and one Phi-3 trial each for threat summarization, report writing, and IOC extraction.
- Phi-3 trials now cover all five Week 1 task types.

### Blocking issues

1. All Phi-3 output files still lack the mandatory Evaluation Record. They cannot be accepted as reviewed benchmark evidence until a reviewer appends it.
2. Phi-3 trial inputs differ from Mahmoud's TC-01 through TC-05 inputs. They must remain a separate trial set, not a direct three-model benchmark.
3. The sampled Phi-3 log-analysis output materially misread the evidence: it described a successful `dev_admin` login as external although the source log states `10.10.10.15`; it used unsupported/incorrect ATT&CK mapping `T1008`; and it claimed no unknowns. Record these in Mina's hallucination log and score the output accordingly.
4. The sampled output did not consistently use the required structured tables or evidence-reference format.

## Integration status

| Input | Status | Next owner |
|---|---|---|
| Llama/Mistral benchmark execution | Received; comparable five-task set, validation pending | Mina and Nada |
| Phi-3 execution | Received for all five task types; separate trial set | Nada/Mina |
| Log-analysis and alert-triage prompt contributions | Received; catalogued | Nada |
| Threat-summary/report-writing prompt contributions | Received and integrated | Nada |
| IOC extraction prompt contribution | Shared template provides output format | Nada |
| Answer key, scoring, and hallucination log | Received as draft/framework; scoring and dataset references pending | Mina |

## Merge recommendation

Keep raw runner artifacts on their source branches. Use `W1_nada_week1-benchmark-registry_v1.md` as the consolidated record. Do not state a final model ranking before Mina's scoring and cross-review.
