# Week 1 - AI Accuracy Review

**Owner:** Nada - Playbook, Detection and Reporting Lead  
**Version:** v1.0  
**Status:** Draft framework awaiting benchmark outputs and Mina's final scoring.  
**Review scope:** Llama 3.1 8B Instruct (primary), Mistral 7B, and Phi-3 Mini on the five agreed Week 1 tasks.

## Purpose

Document whether the local models produce accurate, complete, useful, and evidence-traceable SOC assistance under the agreed benchmark settings. This review does not treat AI output as incident evidence.

## Fixed comparison conditions

- Context size: 8192 tokens
- Temperature: 0.2
- Seed: 42
- Ollama version: `[team-agreed version to be recorded]`
- Shared SOC prompt: template v1.0 pasted into every request
- Modelfile: no `SYSTEM` line
- Same test input and settings for each model per test case

## Evidence sources for this review

| Source | Required state |
|---|---|
| Benchmark request files | Pending creation/execution |
| Matching model outputs | Pending runner execution |
| Mina's answer key | Pending final version from Mina |
| Mina's scoring rubric and scores | Pending completion from Mina |
| Hallucination log | Pending entries from reviewers |

## Results summary

| Model | Tests completed / 5 | Mean accuracy | Mean completeness | Mean usefulness | Automatic failures | Overall finding |
|---|---:|---:|---:|---:|---:|---|
| Llama 3.1 8B Q4_K_M | `[pending]` | `[pending]` | `[pending]` | `[pending]` | `[pending]` | `[pending]` |
| Mistral 7B | `[pending]` | `[pending]` | `[pending]` | `[pending]` | `[pending]` | `[pending]` |
| Phi-3 Mini | `[pending]` | `[pending]` | `[pending]` | `[pending]` | `[pending]` | `[pending]` |

## Required analysis after scoring

1. Identify the best-performing model per task, based on Mina's score records.
2. Record recurring omissions, unsupported claims, incorrect evidence references, and scenario-versus-evidence confusion.
3. Record whether any output failed automatically: invented evidence, unapproved sensitive-data handling, or missing task format.
4. Compare the models only where the same test input and fixed settings were used.
5. Add every observed hallucination to the shared hallucination log with: AI claim, source log line, correct/wrong, error type, and severity.
6. Recommend prompt/template changes only when supported by the benchmark results; version any approved template change.

## Findings and actions

### Strengths

`[Complete from benchmark results.]`

### Accuracy and evidence-traceability failures

`[Complete from Mina's scores and hallucination log.]`

### Prompt or process improvements

`[Complete only after team review of measured failures.]`

### Week 2 recommendation

`[Select the approved primary-model workflow and required human-validation controls.]`

## Final approval

| Item | Reviewer | Status | Date UTC |
|---|---|---|---|
| Benchmark outputs complete | Nada | Pending | `[pending]` |
| Scores and answer-key comparison complete | Mina | Pending | `[pending]` |
| Accuracy review cross-reviewed | Mina | Pending | `[pending]` |
| Week 1 review accepted | Team | Pending | `[pending]` |

## Completion criteria

- Every required benchmark output has a matching request and Evaluation Record.
- Mina's answer key and scores are linked.
- The hallucination log is reviewed.
- All result placeholders are replaced by measured values.
- Mina has cross-reviewed the final review.
