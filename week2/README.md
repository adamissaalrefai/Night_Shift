# Nader — Week 2 Upload Package

This package contains Nader's Week 2 network-analysis deliverables and the record of the completed local Llama run.

## Folder map

- `week2/`
  - `W2_nader_network-analysis_v1.md` — human-prepared network analysis, timeline, IOC context, scenario reconciliation, and detection proposals.
  - `W2_nader_local-llm-run-sheet_v1.md` — updated run sheet with actual execution details.
  - `W2_nader_llm-evaluation_v1.md` — preliminary QA of the raw model output; disposition is **Revise**.
  - `W2_nader_hallucination-log_v1.csv` — claim-level validation log for review; Mina remains owner of the master hallucination log.
  - `tasks.md` — Nader's Week 2 task status.
- `ai-requests/`
  - `20261125-w2-nader-network-log-analysis.md` — runner-prepared archive of the exact prompt and evidence manifest used for the run.
- `ai-outputs/`
  - `20261125-w2-nader-network-log-analysis.md` — verbatim model response plus the required Evaluation Record.
  - `20261125-w2-nader-network-log-analysis-metadata.txt` — original execution metadata.

## Important notes

1. The local run completed using `llama3.1:8b`, Ollama `0.40.18`, context 8192, temperature 0.2, and seed 42.
2. Team-wide approval of Ollama `0.40.18` has **not** been verified. Keep the run marked exploratory until the team confirms the shared version.
3. The model output is preserved verbatim and has a preliminary **Revise** disposition. Do not present it as an approved incident report.
4. Adam's cross-review and Nada's second validation remain pending.
5. The archived request file is not represented as a Nada-issued queue item; no Nader-assigned Week 2 request authored by Nada was found in the repository snapshots reviewed.
6. The original source evidence notes remain on branch `week1/mahmoud-benchmark`, commit `7b34d77da5dd4713a7a81dc7f02aae4fa61707eb`. They are referenced by path and SHA-256; this package does not duplicate or alter them.
7. Do not upload or copy any credential-bearing source log into this package. The analysis and request documentation do not reproduce credentials.

## Upload target

Upload the package contents to the repository branch `Nader`, preserving the folder paths. Do not commit to `main`.
