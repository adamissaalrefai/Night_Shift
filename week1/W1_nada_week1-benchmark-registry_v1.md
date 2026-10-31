# Week 1 Benchmark Registry

**Owner:** Nada - Playbook, Detection and Reporting Lead  
**Version:** v1.0  
**Review date:** 2026-10-31 UTC

## Purpose

This registry records the runner artifacts received for Week 1 and separates a valid Llama/Mistral comparison from Phi-3 trial work. It does not assign scores or claim human acceptance.

## Comparable benchmark set

Mahmoud supplied a Llama/Mistral request pair for each task. For each pair, the embedded evidence and shared prompt body are identical; the model is the intended comparison variable.

| Test | Task | Llama request/output ID | Mistral request/output ID | Comparison status |
|---|---|---|---|---|
| TC-01 | Log analysis | `20261028-w1-log-analysis-llama31` | `20261028-w1-log-analysis-mistral7b` | Comparable; scoring pending |
| TC-02 | Alert triage | `20261028-w1-alert-triage-llama31` | `20261028-w1-alert-triage-mistral7b` | Comparable; scoring pending |
| TC-03 | Threat summarization | `20261028-w1-threat-summarization-llama31` | `20261028-w1-threat-summarization-mistral7b` | Comparable; scoring pending |
| TC-04 | Report writing | `20261028-w1-report-writing-llama31` | `20261028-w1-report-writing-mistral7b` | Comparable; scoring pending |
| TC-05 | IOC extraction | `20261028-w1-ioc-extraction-llama31` | `20261028-w1-ioc-extraction-mistral7b` | Comparable; scoring pending |

**Source branch:** `origin/week1/mahmoud-benchmark`  
**Request location:** `Requests/Ai-Requests/`  
**Output location:** `outputs/Ai-Outputs/`  
**Execution metadata recorded:** Ollama 0.40.11, context 8192, temperature 0.2, seed 42.

## Phi-3 trial set

Adam supplied Phi-3 trials for all five task types. They are valid raw trial artifacts but use different inputs and test IDs from TC-01 through TC-05; do not aggregate them into the Llama/Mistral model summary.

| Task | Trial request IDs | Trial status |
|---|---|---|
| Log analysis | `20261027-test-01-phi3` through `20261027-test-05-phi3` | Five trials; Evaluation Records absent |
| Alert triage | `20261027-triage-test-01-phi3` through `20261027-triage-test-03-phi3` | Three trials; Evaluation Records absent |
| Threat summarization | `20261027-test-06-phi3` | One trial; Evaluation Record absent |
| Report writing | `20261027-test-07-phi3` | One trial; Evaluation Record absent |
| IOC extraction | `20261027-test-08-phi3` | One trial; Evaluation Record absent |

**Source branch:** `origin/Adam/Week1`  
**Prompt location:** `week1/Prompts/`  
**Output location:** `week1/Outputs/`

## Required review actions

1. Score the ten comparable Llama/Mistral outputs against answer-key items with completed dataset references.
2. Append reviewer Evaluation Records to the Phi-3 trials, then score them as trials against their own input evidence.
3. Log every wrong or unsupported claim in Mina's hallucination workbook.
4. Do not compare average scores across the Llama/Mistral set and the Phi-3 trial set.
5. Publish a three-model ranking only if Phi-3 runs TC-01 through TC-05 unchanged.
