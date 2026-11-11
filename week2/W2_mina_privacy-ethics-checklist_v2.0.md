# IR Capstone: Privacy and Ethics Checklist, Week 2 re-run

- **Owner:** Mina (Validation and Evidence Lead)
- **Version:** v2.0 **DRAFT** (updates v1.0)
- **Date (UTC):** 2026-11-11
- **Time standard:** UTC, ISO 8601

## How this draft was filled

The Result column is filled **only where the uploaded files prove it** (Answer Key v1.1, hallucination log v1.1, scoring workbook v1.1, the cross-review of Nada's files, the two Modelfiles, the repository screenshots and the scenario). The evidence column names the document. **Everything else is `Unverified`**: it needs a link to real evidence (for example Nader's offline-test notes, repository settings or the Decisions Log) before anyone marks it Yes. Mina confirms by signing section 10. Rule from v1.0 stays: a Yes without an evidence link counts as No.

Result codes: **Yes**, **No**, **Partly**, **Unverified**, **N/A**.

## Summary of this draft

- Items with a result from the files: 14 of 51 (see the tables).
- Items that are Unverified: the rest. Most of them depend on files not supplied (offline-test notes, repository settings, Decisions Log).
- **No items are marked Yes for the model privacy section (P-14 to P-18)** because Nader's offline-test notes were not provided.


## 1. Data and scenario

| ID | Check | Source | Result | Evidence / note | Reviewer | Date |
|---|---|---|---|---|---|---|
| P-01 | Only synthetic, public, or sanitized lab data is used. No real company or personal data anywhere in the project | Kickoff 12; Standard 1.6 | Yes | Evidence files are labeled synthetic (Answer Key v1.1 s.1; Input Registry) | | |
| P-02 | Every hostname, user, company name and IP in the datasets matches the fictional NileTech scenario (for example `sara.ahmed`, `niletech.local`) | Scenario 1 | Partly | Scenario hosts match; Adam's trial inputs also use 10.10.10.30 and 192.0.2.x, not in the scenario table. Fictional, but add them to the scenario | | |
| P-03 | Public datasets, if used, were rewritten to the scenario names and their license or source is recorded | Scenario 3 | Unverified |  | | |
| P-04 | Attacker IPs are documentation-range addresses (`198.51.100.45` is TEST-NET-2, `203.0.113.88` is TEST-NET-3) and no real third-party IP is used | Scenario 1 | Yes | 198.51.100.45, 198.51.100.99, 203.0.113.88 and 192.0.2.x are documentation ranges | | |
| P-05 | The attacker domains `mail.niletech-support.com` and `cdn-update-check.net` are never visited, resolved on the public internet, or submitted to online scanners. They may exist in real life and are used as text labels only | Scenario 1; Kickoff 3 | Unverified |  | | |
| P-06 | The scenario hash `e3b0c442...b855` is documented as a placeholder (it is the SHA-256 of an empty input) and is not sent to VirusTotal as if it were real malware | Scenario 1; Kickoff 11 | Partly | Placeholder documented (Key v1.1 C7, NC-04). Not verified that it was never sent to an online scanner | | |
| P-07 | Any Week 3 enrichment uses simulated data or a documented, approved source. Nothing from the project is uploaded to an external service | Kickoff 11 | Unverified |  | | |
| P-08 | Credentials and keys appearing in logs (for example `dev_admin`) are lab-only values. No real password or token is stored, and any secrets that appear in evidence are redacted | Standard SOC prompt rule 8 | Unverified |  | | |

## 2. Lab and attack simulation safety

| ID | Check | Source | Result | Evidence / note | Reviewer | Date |
|---|---|---|---|---|---|---|
| P-09 | Attack tools (Atomic Red Team, Hydra, Nikto, curl, dig) run **only** against the team's own VMs, never against real systems, the university network, or the internet | Scenario 3 | Unverified |  | | |
| P-10 | The lab VMs are on an isolated or host-only network, or any outbound access is blocked or controlled | Scenario 3 | Unverified |  | | |
| P-11 | Credential-dumping and brute-force tests use lab accounts only. No real person's credentials are involved | Scenario 2 | Unverified |  | | |
| P-12 | Malicious samples (the `.hta` attachment, `updater.ps1`) stay inside the lab and are not emailed, shared, or uploaded outside the repository's controlled storage | Scenario 1 | Unverified |  | | |
| P-13 | The instructor was told about the attack-simulation approach, and any unclear point was raised before proceeding | Kickoff 12 | Unverified |  | | |

## 3. Local AI model and environment

| ID | Check | Source | Result | Evidence / note | Reviewer | Date |
|---|---|---|---|---|---|---|
| P-14 | Only the local model (Llama 3.1 8B Instruct Q4_K_M, plus Mistral 7B and Phi-3 Mini for the Week 1 benchmark) touches project data | Kickoff 3; Standard 2 | Unverified |  | | |
| P-15 | No project data was pasted into any online AI tool (ChatGPT, Claude.ai, Gemini, Copilot, and similar), including "just a small snippet" | Kickoff 3 | Unverified |  | | |
| P-16 | Nader's offline privacy test is complete: network disconnected, traffic monitored while the model runs, no outbound connections from the model process | Work Plan Week 1 | Unverified |  | | |
| P-17 | Ollama telemetry and update checks are considered, and the result is noted in the offline test notes | Work Plan Week 1 | Unverified |  | | |
| P-18 | Only Mahmoud, Adam and Nader run the model. Non-runners work from saved outputs | Work Plan | Unverified |  | | |
| P-19 | All runners use the same settings: Q4_K_M, context 8192, temperature 0.2, same Ollama version, same Modelfile (only `FROM` differs) | Kickoff 3 | Partly | Llama and Phi-3 Modelfiles match (ctx 8192, temp 0.2, seed 42, no SYSTEM). Mistral Modelfile not seen; Ollama version not agreed; Llama file has no TEMPLATE line (check with `ollama show --modelfile`) | | |
| P-20 | Large logs are split into chunks of the agreed fixed size before being sent to the model | Kickoff 3 | Unverified |  | | |

## 4. Repository and files

| ID | Check | Source | Result | Evidence / note | Reviewer | Date |
|---|---|---|---|---|---|---|
| P-21 | The repository is private and only the five team members are collaborators | Kickoff 5 | Unverified |  | | |
| P-22 | `.gitignore` exists from day one and covers keys, tokens, model files, and large logs/PCAPs | Kickoff 5, 6 | Unverified |  | | |
| P-23 | No passwords, API keys, or real personal data in any commit (spot-check history, not just the latest version) | Kickoff 5 | Unverified |  | | |
| P-24 | Model files are not committed. Large logs and PCAPs are in shared storage and linked | Kickoff 6 | Unverified |  | | |
| P-25 | `main` is protected. Changes go through a pull request reviewed by the cross-review partner | Kickoff 5 | Partly | PRs #1 and #2 exist; `week1/mahmoud-benchmark` and `Adam/Week1` have no PR yet | | |
| P-26 | Everything is stored inside the agreed folder structure, nothing outside it | Kickoff 6 | No | Repository has `outputs/`, `template/` and `week5/`; agreed structure is `ai-outputs`, `templates`, `ai-requests`, weeks 1 to 4 | | |
| P-27 | Original evidence files are never overwritten; edits create a new version | Standard 6 | Unverified |  | | |

## 5. Evidence handling

| ID | Check | Source | Result | Evidence / note | Reviewer | Date |
|---|---|---|---|---|---|---|
| P-28 | Every evidence file in `/evidence` has the agreed name and a one-line note (what it is, where it came from, who collected it) | Kickoff 9 | Unverified |  | | |
| P-29 | All timestamps are UTC, ISO 8601 | Kickoff 9 | Partly | Request ID dates differ from the 'Requested at' times in Adam's Phi-3 requests (registry review BR-3) | | |
| P-30 | Input file hashes are recorded in each request's evidence manifest and Evaluation Record | Standard 3, 5 | No | TC-05 manifest hash `0ce6aa15...` does not match the file (`70de7ad1...`) | | |

## 6. Human review and AI labeling

| ID | Check | Source | Result | Evidence / note | Reviewer | Date |
|---|---|---|---|---|---|---|
| P-31 | The answer key was written **before** any AI output was run or read (check commit dates) | Work Plan Week 2 | No | Key v1.1 was written after Week 1 outputs existed; disclosed in its section 0 and sent to Nader (check C1). v2.0 repeats the disclosure | | |
| P-32 | Every file in `/ai-outputs` ends with a completed Evaluation Record and a named reviewer | Kickoff 3, 4 | No | All ten Mahmoud outputs carry an Evaluation Record; none of Adam's 19 do (cross-review of Nada's files) | | |
| P-33 | No AI output became a finding, detection, playbook action or report statement without human review against raw evidence | Kickoff 3, 12 | Partly | Mina validated the ten Llama/Mistral outputs. Runner self-checks ('Unsupported claims: 0', 'Pass') were contradicted by the review and must not count as validation | | |
| P-34 | Hallucinations are logged when found (AI claim, source log line, correct/wrong, error type, severity), not at the end of the week | Work Plan rules | Partly | Log v1.1 has 116 rows, dated at draft date with no time of day. Adam's Llama runs are only partly logged | | |
| P-35 | AI-written text in the final report is labeled "AI-drafted, human-validated", and Nada's workflow document records what AI drafted and what humans corrected | Kickoff 12; Work Plan Week 4 | Unverified |  | | |
| P-36 | AI recommendations are treated as drafts. No operational action is recorded as completed unless the evidence shows it was done | Standard 6 | Unverified |  | | |
| P-37 | Week 1 benchmark outputs from Mistral and Phi-3 are not used as evidence in later weeks | Work Plan | Yes | Phi-3 is scored on a separate tab and not ranked with Llama and Mistral | | |

## 7. Governance

| ID | Check | Source | Result | Evidence / note | Reviewer | Date |
|---|---|---|---|---|---|---|
| P-38 | A Decisions Log exists and records every agreed setting (what, who, date) | Kickoff 1 | Unverified |  | | |
| P-39 | The two daily queue-check times are agreed and recorded in the repository | Standard 1.4 | Unverified |  | | |
| P-40 | Course requirements (deadline, deliverable format, demo, grading) are confirmed with the instructor | Kickoff 13 | Unverified |  | | |
| P-41 | Any change to the AI Request template or SOC prompt has a new version number and team agreement | Kickoff 4 | Unverified |  | | |

## 7a. New Week 2 checks

| ID | Check | Source | Result | Evidence / note | Reviewer | Date |
|---|---|---|---|---|---|---|
| P-42 | Week 2 evidence comes only from Llama 3.1 8B Instruct Q4_K_M with the agreed settings (ctx 8192, temp 0.2, seed 42, no SYSTEM). Mistral and Phi-3 outputs are not used | Work Plan rules | Unverified | | | |
| P-43 | Every request file name equals its output file name exactly, and request IDs follow YYYYMMDD-short-task-name | Standard 1.2-1.3 | Unverified | | | |
| P-44 | Every input file is hashed before the run and the hash matches the request's evidence manifest | Cross-review BP-6 | Unverified | | | |
| P-45 | Every Week 2 output ends with a completed Evaluation Record. Runner self-checks are not counted as validation | Cross-review AR-6, IR-2 | Unverified | | | |
| P-46 | Every Week 2 output is validated claim by claim in `W2 Claims`, and each wrong claim is also logged in the runner's tab | Answer Key v2.0 s.4 | Unverified | | | |
| P-47 | The triage set includes benign controls (at least 2 to 3), including the SIEM-3107 decoy | Answer Key v2.0 s.2 | Unverified | | | |
| P-48 | Nada acted as second validator on the Linux and network results; disagreements were settled against the raw log | Work Plan Week 2 | Unverified | | | |
| P-49 | No AI recommendation (isolate, disable, block, reset) appears in a playbook or report as done, and no recommendation for a benign alert was acted on | Standard 6; NC-06 | Unverified | | | |
| P-50 | The Week 2 answer key was signed before Week 2 outputs were scored. Any later change is a new version with the reason recorded | Answer Key v2.0 s.0 | Unverified | | | |
| P-51 | One reconciled timeline and one set of attacker-host roles is recorded in the Decisions Log (open conflicts in Answer Key v2.0 s.6) | Answer Key v2.0 s.6 | Unverified | | | |

## 8. Issues found (open at this date)

| # | Issue | Affects | Suggested decision | Owner |
|---|---|---|---|---|
| 1 | **System prompt wording.** The Modelfiles confirm no SYSTEM line. The Shared AI Request Standard still says "one shared SOC Analyst system prompt stored in the repository" and the request template has a system-prompt path field | P-19, P-41 | Correct the Standard and template (no SYSTEM; prompt pasted into each request) | Nada |
| 2 | **Seed not in the Standard.** Seed 42 is in both Modelfiles but missing from the Standard, template and Evaluation Record | P-19 | Add "Seed: 42" | Nada |
| 3 | **Ollama version not agreed** | P-19 | Record one version in the Decisions Log | Mahmoud, Adam, Nader |
| 4 | **Placeholder hash** (SHA-256 of empty input) | P-06 | Replace with a real test or public hash before Week 3 | Mahmoud |
| 5 | **Attacker domains may exist publicly** | P-05 | Text only; never resolve or scan; consider reserved example names | Mahmoud |
| 6 | **Scenario file still ends with an empty "## 4" heading** | P-02 | Confirm nothing was cut off | Mahmoud |
| 7 | **Folder structure differs** from the agreement (`outputs`, `template`, `week5`) | P-26 | Choose one structure; the request template needs exact paths | Team |
| 8 | **TC-05 manifest hash mismatch** | P-30, P-44 | Re-hash the pasted file and correct the manifest | Mahmoud, Mina |
| 9 | **Adam's 19 outputs have no Evaluation Record** | P-32, P-45 | Add the record or re-run | Adam |
| 10 | **Corrupted characters in Mahmoud's lab setup guide** (settings unreadable) | P-19 | Fix the file | Mahmoud |
| 11 | **Llama Modelfile has no TEMPLATE line** | P-19 | Run `ollama show <model> --modelfile`; add the template once in the shared Modelfile if missing | Mahmoud |
| 12 | **Timeline and attacker-host conflicts** (Answer Key v2.0 s.6, items 3 and 4) | P-51 | One reconciled timeline | Adam, Mahmoud, Nader |

## 9. Issues table (fill during use)

| Date (UTC) | Item ID | What went wrong | Action | Owner | Status |
|---|---|---|---|---|---|
| | | | | | |

## 10. Sign-off

| Checkpoint | Completed by | Date (UTC) | Cross-reviewer | Date (UTC) |
|---|---|---|---|---|
| Before Week 2 | Mina | | Nader | |
| End of Week 2 | Mina | | Nader | |
| End of Week 3 | Mina | | Nader | |
| Before final submission (Week 4) | Mina | | Nader | |
