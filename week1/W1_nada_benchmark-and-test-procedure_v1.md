# Week 1 - Nada: Local Model Benchmark and Test Procedure

**Owner:** Nada - Playbook, Detection and Reporting Lead  
**Version:** v1.0  
**Status:** Execution inventory updated 2026-10-31; reviewer scoring remains pending.
**Scope:** Week 1 only

## Objective

Compare the agreed local models on five fixed SOC tasks using the same test cases and execution settings. This benchmark measures model-output quality; it does not establish that an incident occurred.

## Fixed execution settings

| Item | Required value |
|---|---|
| Primary model | Llama 3.1 8B Instruct, Q4_K_M |
| Benchmark-only models | Mistral 7B; Phi-3 Mini |
| Runner | Ollama; same agreed version on all runner laptops |
| Context size | 8192 tokens |
| Temperature | 0.2 |
| Seed | 42 |
| Modelfile | Shared repository Modelfile; no `SYSTEM` line; only `FROM` differs by machine |
| Prompt source | Shared SOC Analyst prompt v1.0 pasted into every request file |
| Time standard | UTC, ISO 8601 |

## Test cases

The scenario file is background context only. The test data must be synthetic/public and supplied in the matching request file. Mina owns the final answer key, which must be completed before scoring begins.

| ID | Task | Required output check | Benchmark runner/model |
|---|---|---|---|
| W1-T01 | Log analysis | Timeline, evidence references, confidence, data gaps | Mahmoud: comparable Llama + Mistral; Adam: separate Phi-3 trials |
| W1-T02 | Alert triage | Disposition, severity, evidence for/against, escalation decision | Mahmoud: comparable Llama + Mistral; Adam: separate Phi-3 trials |
| W1-T03 | Threat summarization | Confirmed/suspected/unknown separation, scope, prioritized actions | Mahmoud: comparable Llama + Mistral; Adam: separate Phi-3 trials |
| W1-T04 | Report writing | Required headings, evidence traceability, no unsupported completion claims | Mahmoud: comparable Llama + Mistral; Adam: separate Phi-3 trials |
| W1-T05 | IOC extraction | Deduplicated IOCs, correct status, source references | Mahmoud: comparable Llama + Mistral; Adam: separate Phi-3 trials |

## Procedure

1. Nada creates one request file per test and model in `/ai-requests/` using the shared template. Name each `YYYYMMDD-w1-t0x-<model>.md`.
2. Each request references the identical test input for that test ID, includes the complete SOC prompt v1.0, and records the exact settings above.
3. Mahmoud runs Llama and Mistral; Adam runs Phi-3. Nader runs Nada's queue requests when assigned; Adam is backup.
4. Each runner saves the matching output in `/ai-outputs/` and appends the completed Evaluation Record.
5. Mina scores outputs against her prewritten answer key using the 1-5 scale for accuracy, completeness, and usefulness, and records pass/fail checks.
6. Nada records results below, consolidates the prompt library, and writes the AI accuracy review.
7. Cross-review: Mina reviews Nada's deliverables; fixes are versioned before merge.

## Scoring rules

| Score | Meaning |
|---|---|
| 5 | Correct, complete, evidence-traceable, and directly usable with only minor wording edits. |
| 4 | Substantially correct; minor omissions or clarity fixes needed. |
| 3 | Partially useful; material analyst correction required. |
| 2 | Major inaccuracies, omissions, or unsupported claims. |
| 1 | Unsafe or unusable result. |

Automatic failure conditions: invented evidence, treating scenario context as observed evidence, missing required output format, or disclosure of sensitive data.

## Execution and comparison status

Mahmoud's Llama/Mistral pairs are directly comparable: for every task, the paired requests have the same embedded evidence and prompt body. Adam's Phi-3 work covers all five task types, but uses trial inputs and request IDs that differ from Mahmoud's set. It is reviewed separately and is not used for a three-model ranking.

| Comparison set | Tasks covered | Inputs and prompt matched within set | Status |
|---|---:|---|---|
| Llama 3.1 8B vs Mistral 7B | 5 of 5 | Yes, verified from request content | Comparable execution set; scoring pending |
| Phi-3 Mini trials | 5 of 5 | No match to Mahmoud's inputs | Separate trial evaluation; Evaluation Records and scoring pending |

## Benchmark result sheet

| Test ID | Model | Runner | Output path | Accuracy (1-5) | Completeness (1-5) | Usefulness (1-5) | Pass/fail | Unsupported claims | Reviewer | Status |
|---|---|---|---|---:|---:|---:|---|---:|---|---|
| TC-01 | Llama 3.1 8B | Mahmoud | `origin/week1/mahmoud-benchmark:outputs/Ai-Outputs/20261028-w1-log-analysis-llama31.md` | Pending | Pending | Pending | Pending | Pending | Mina/Nada | Pending |
| TC-01 | Mistral 7B | Mahmoud | `origin/week1/mahmoud-benchmark:outputs/Ai-Outputs/20261028-w1-log-analysis-mistral7b.md` | Pending | Pending | Pending | Pending | Pending | Mina/Nada | Pending |
| TC-02 | Llama 3.1 8B | Mahmoud | `origin/week1/mahmoud-benchmark:outputs/Ai-Outputs/20261028-w1-alert-triage-llama31.md` | Pending | Pending | Pending | Pending | Pending | Mina/Nada | Pending |
| TC-02 | Mistral 7B | Mahmoud | `origin/week1/mahmoud-benchmark:outputs/Ai-Outputs/20261028-w1-alert-triage-mistral7b.md` | Pending | Pending | Pending | Pending | Pending | Mina/Nada | Pending |
| TC-03 to TC-05 | Llama 3.1 8B and Mistral 7B | Mahmoud | See `W1_nada_week1-benchmark-registry_v1.md` | Pending | Pending | Pending | Pending | Pending | Mina/Nada | Pending |
| Phi-3 trials | Phi-3 Mini | Adam | See `W1_nada_week1-benchmark-registry_v1.md` | Pending | Pending | Pending | Not directly comparable | Pending | Mina/Nada | Pending |

## Completion criteria

- Ten matching Llama/Mistral request-output pairs exist and have been scored against finalized answer-key references.
- Phi-3 trial outputs are scored separately and contain Evaluation Records.
- A three-model ranking is published only after all three models run the same five requests.
- Mina cross-reviews the final accuracy review.
