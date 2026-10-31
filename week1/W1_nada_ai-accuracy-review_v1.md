# Week 1 - AI Accuracy Review

**Owner:** Nada - Playbook, Detection and Reporting Lead  
**Version:** v1.1
**Status:** Execution review complete; numerical scoring and final acceptance pending Mina's validation.
**Review scope:** Llama 3.1 8B Instruct (primary), Mistral 7B, and Phi-3 Mini on the five agreed Week 1 tasks.

## Purpose

Document whether the local models produce accurate, complete, useful, and evidence-traceable SOC assistance under the agreed benchmark settings. This review does not treat AI output as incident evidence.

## Fixed comparison conditions

- Context size: 8192 tokens
- Temperature: 0.2
- Seed: 42
- Ollama version: 0.40.11 for the Mahmoud benchmark set; Nader's privacy test records 0.40.13. A single team version is not yet recorded.
- Shared SOC prompt: template v1.0 pasted into every request
- Modelfile: no `SYSTEM` line
- Same test input and settings for the Llama/Mistral comparison only. Phi-3 inputs differ and are reviewed separately.

## Evidence sources for this review

| Source | Required state |
|---|---|
| Llama/Mistral requests and outputs | Ten received from `origin/week1/mahmoud-benchmark`; five matched task pairs |
| Phi-3 requests and outputs | Eleven trial runs received from `origin/Adam/Week1`; five task types covered |
| Mina's answer key | Draft received; dataset-reference columns and cross-review remain open |
| Mina's scoring rubric | Workbook received; all real scoring rows are blank |
| Hallucination log | Workbook received; no reviewed runner findings entered |

## Results summary

| Model | Tests completed / 5 | Mean accuracy | Mean completeness | Mean usefulness | Automatic failures | Overall finding |
|---|---:|---:|---:|---:|---:|---|
| Llama 3.1 8B Q4_K_M | 5 of 5 | Not scored | Not scored | Not scored | Not determined | Directly comparable with Mistral after scoring |
| Mistral 7B | 5 of 5 | Not scored | Not scored | Not scored | Not determined | Directly comparable with Llama after scoring |
| Phi-3 Mini | 5 task types, 11 trials | Not scored | Not scored | Not scored | Not determined | Separate trial set; exclude from three-model ranking |

## Required analysis after scoring

1. Identify the best-performing model per task, based on Mina's score records.
2. Record recurring omissions, unsupported claims, incorrect evidence references, and scenario-versus-evidence confusion.
3. Record whether any output failed automatically: invented evidence, unapproved sensitive-data handling, or missing task format.
4. Compare the models only where the same test input and fixed settings were used.
5. Add every observed hallucination to the shared hallucination log with: AI claim, source log line, correct/wrong, error type, and severity.
6. Recommend prompt/template changes only when supported by the benchmark results; version any approved template change.

## Findings and actions

### Strengths

Execution coverage exists for all five tasks. Mahmoud's Llama/Mistral task pairs use identical embedded evidence and prompt bodies, which supports a fair two-model comparison once scored.

### Accuracy and evidence-traceability failures

No final numerical finding is reported. Mina's scoring rows are blank and the answer key still lacks dataset references. Adam's raw Phi-3 outputs also lack the mandatory Evaluation Record. A sampled earlier Phi-3 log-analysis output contained evidence and ATT&CK errors; it must be entered in the hallucination log during review rather than silently edited.

### Prompt or process improvements

Keep seed 42, the no-SYSTEM Modelfile rule, and the pasted shared prompt. Standardize new work to lowercase `/ai-requests` and `/ai-outputs`. Do not publish a three-model ranking until Phi-3 is rerun on the five Llama/Mistral requests.

### Week 2 recommendation

Use Llama 3.1 8B as the agreed operational model only after human validation. Retain the request queue, evidence manifest, Evaluation Record, Mina's answer key, and hallucination log for every Week 2 AI-assisted finding.

## Final approval

| Item | Reviewer | Status | Date UTC |
|---|---|---|---|
| Benchmark execution inventory complete | Nada | Complete | 2026-10-31 |
| Scores and answer-key comparison complete | Mina | Pending | `[pending]` |
| Accuracy review cross-reviewed | Mina | Pending | `[pending]` |
| Week 1 review accepted | Team | Pending | `[pending]` |

## Completion criteria

- Mina fills dataset references in the answer key and scores the comparable Llama/Mistral set.
- Nada or Mina appends reviewer Evaluation Records to Phi-3 trials before scoring them.
- The hallucination log records all discovered errors.
- Mina cross-reviews this review.
- A three-model ranking remains intentionally absent unless identical-input Phi-3 runs are supplied.
