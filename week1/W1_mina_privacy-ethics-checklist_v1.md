# IR Capstone: Privacy and Ethics Checklist

- **Owner:** Mina (Validation and Evidence Lead)
- **Version:** v1.0
- **Applies to:** NileTech Financial Services unified attack scenario (Weeks 1 to 4)
- **Built from:** Kickoff Agreements Checklist sections 3, 5, 9 and 12; Shared AI Request and SOC Prompt Standard v1.0 sections 1, 2 and 6; Attack Scenario v1.0
- **Time standard:** UTC, ISO 8601

## How to use this checklist

1. Run it at three points: **before Week 1 work starts**, **at the end of each week (Day 6)**, and **before final submission (Week 4, Day 7)**.
2. For each item, record **Yes**, **No** or **N/A**. A "Yes" needs an **evidence link** (file path, screenshot, commit, or note). A "Yes" with no evidence counts as "No".
3. Any "No" becomes an entry in the Issues table (section 8) with an owner and a date. Unresolved "No" items go to the instructor.
4. The reviewer initials and date go on every row. The cross-review rule applies: Nader reviews this checklist.

Result codes: **Y** = yes, **N** = no, **N/A** = not applicable.

---

## 1. Data and scenario

| ID | Check | Source | Result | Evidence link | Reviewer | Date |
|---|---|---|---|---|---|---|
| P-01 | Only synthetic, public, or sanitized lab data is used. No real company or personal data anywhere in the project | Kickoff 12; Standard 1.6 | | | | |
| P-02 | Every hostname, user, company name and IP in the datasets matches the fictional NileTech scenario (for example `sara.ahmed`, `niletech.local`) | Scenario 1 | | | | |
| P-03 | Public datasets, if used, were rewritten to the scenario names and their license or source is recorded | Scenario 3 | | | | |
| P-04 | Attacker IPs are documentation-range addresses (`198.51.100.45` is TEST-NET-2, `203.0.113.88` is TEST-NET-3) and no real third-party IP is used | Scenario 1 | | | | |
| P-05 | The attacker domains `mail.niletech-support.com` and `cdn-update-check.net` are never visited, resolved on the public internet, or submitted to online scanners. They may exist in real life and are used as text labels only | Scenario 1; Kickoff 3 | | | | |
| P-06 | The scenario hash `e3b0c442...b855` is documented as a placeholder (it is the SHA-256 of an empty input) and is not sent to VirusTotal as if it were real malware | Scenario 1; Kickoff 11 | | | | |
| P-07 | Any Week 3 enrichment uses simulated data or a documented, approved source. Nothing from the project is uploaded to an external service | Kickoff 11 | | | | |
| P-08 | Credentials and keys appearing in logs (for example `dev_admin`) are lab-only values. No real password or token is stored, and any secrets that appear in evidence are redacted | Standard SOC prompt rule 8 | | | | |

## 2. Lab and attack simulation safety

| ID | Check | Source | Result | Evidence link | Reviewer | Date |
|---|---|---|---|---|---|---|
| P-09 | Attack tools (Atomic Red Team, Hydra, Nikto, curl, dig) run **only** against the team's own VMs, never against real systems, the university network, or the internet | Scenario 3 | | | | |
| P-10 | The lab VMs are on an isolated or host-only network, or any outbound access is blocked or controlled | Scenario 3 | | | | |
| P-11 | Credential-dumping and brute-force tests use lab accounts only. No real person's credentials are involved | Scenario 2 | | | | |
| P-12 | Malicious samples (the `.hta` attachment, `updater.ps1`) stay inside the lab and are not emailed, shared, or uploaded outside the repository's controlled storage | Scenario 1 | | | | |
| P-13 | The instructor was told about the attack-simulation approach, and any unclear point was raised before proceeding | Kickoff 12 | | | | |

## 3. Local AI model and environment

| ID | Check | Source | Result | Evidence link | Reviewer | Date |
|---|---|---|---|---|---|---|
| P-14 | Only the local model (Llama 3.1 8B Instruct Q4_K_M, plus Mistral 7B and Phi-3 Mini for the Week 1 benchmark) touches project data | Kickoff 3; Standard 2 | | | | |
| P-15 | No project data was pasted into any online AI tool (ChatGPT, Claude.ai, Gemini, Copilot, and similar), including "just a small snippet" | Kickoff 3 | | | | |
| P-16 | Nader's offline privacy test is complete: network disconnected, traffic monitored while the model runs, no outbound connections from the model process | Work Plan Week 1 | | | | |
| P-17 | Ollama telemetry and update checks are considered, and the result is noted in the offline test notes | Work Plan Week 1 | | | | |
| P-18 | Only Mahmoud, Adam and Nader run the model. Non-runners work from saved outputs | Work Plan | | | | |
| P-19 | All runners use the same settings: Q4_K_M, context 8192, temperature 0.2, same Ollama version, same Modelfile (only `FROM` differs) | Kickoff 3 | | | | |
| P-20 | Large logs are split into chunks of the agreed fixed size before being sent to the model | Kickoff 3 | | | | |

## 4. Repository and files

| ID | Check | Source | Result | Evidence link | Reviewer | Date |
|---|---|---|---|---|---|---|
| P-21 | The repository is private and only the five team members are collaborators | Kickoff 5 | | | | |
| P-22 | `.gitignore` exists from day one and covers keys, tokens, model files, and large logs/PCAPs | Kickoff 5, 6 | | | | |
| P-23 | No passwords, API keys, or real personal data in any commit (spot-check history, not just the latest version) | Kickoff 5 | | | | |
| P-24 | Model files are not committed. Large logs and PCAPs are in shared storage and linked | Kickoff 6 | | | | |
| P-25 | `main` is protected. Changes go through a pull request reviewed by the cross-review partner | Kickoff 5 | | | | |
| P-26 | Everything is stored inside the agreed folder structure, nothing outside it | Kickoff 6 | | | | |
| P-27 | Original evidence files are never overwritten; edits create a new version | Standard 6 | | | | |

## 5. Evidence handling

| ID | Check | Source | Result | Evidence link | Reviewer | Date |
|---|---|---|---|---|---|---|
| P-28 | Every evidence file in `/evidence` has the agreed name and a one-line note (what it is, where it came from, who collected it) | Kickoff 9 | | | | |
| P-29 | All timestamps are UTC, ISO 8601 | Kickoff 9 | | | | |
| P-30 | Input file hashes are recorded in each request's evidence manifest and Evaluation Record | Standard 3, 5 | | | | |

## 6. Human review and AI labeling

| ID | Check | Source | Result | Evidence link | Reviewer | Date |
|---|---|---|---|---|---|---|
| P-31 | The answer key was written **before** any AI output was run or read (check commit dates) | Work Plan Week 2 | | | | |
| P-32 | Every file in `/ai-outputs` ends with a completed Evaluation Record and a named reviewer | Kickoff 3, 4 | | | | |
| P-33 | No AI output became a finding, detection, playbook action or report statement without human review against raw evidence | Kickoff 3, 12 | | | | |
| P-34 | Hallucinations are logged when found (AI claim, source log line, correct/wrong, error type, severity), not at the end of the week | Work Plan rules | | | | |
| P-35 | AI-written text in the final report is labeled "AI-drafted, human-validated", and Nada's workflow document records what AI drafted and what humans corrected | Kickoff 12; Work Plan Week 4 | | | | |
| P-36 | AI recommendations are treated as drafts. No operational action is recorded as completed unless the evidence shows it was done | Standard 6 | | | | |
| P-37 | Week 1 benchmark outputs from Mistral and Phi-3 are not used as evidence in later weeks | Work Plan | | | | |

## 7. Governance

| ID | Check | Source | Result | Evidence link | Reviewer | Date |
|---|---|---|---|---|---|---|
| P-38 | A Decisions Log exists and records every agreed setting (what, who, date) | Kickoff 1 | | | | |
| P-39 | The two daily queue-check times are agreed and recorded in the repository | Standard 1.4 | | | | |
| P-40 | Course requirements (deadline, deliverable format, demo, grading) are confirmed with the instructor | Kickoff 13 | | | | |
| P-41 | Any change to the AI Request template or SOC prompt has a new version number and team agreement | Kickoff 4 | | | | |

---

## 8. Issues found in the current documents (to resolve at the next Day 0 meeting)

These come from comparing the files uploaded so far. Each one affects a checklist item above.

| # | Issue | Affects | Suggested decision | Owner |
|---|---|---|---|---|
| 1 | **System prompt conflict.** The Work Plan and Kickoff say "no SYSTEM line in the Modelfile; the SOC prompt is pasted into every request file". The Standard (section 2) says "one shared SOC Analyst system prompt stored in the repository", and the request template has a "Shared system prompt path" field | P-19, P-41 | Keep the Work Plan rule (prompt pasted in each request, no SYSTEM line) and remove the system-prompt path field from the template, or vice versa. Choose one | Nada (template owner) |
| 2 | **Seed not in the Standard.** The Work Plan and Kickoff set seed 42 on every runner. The Standard, request template and Evaluation Record do not mention a seed | P-19 | Add "Seed: 42" to the Standard, template and Evaluation Record | Nada |
| 3 | **Ollama version not agreed.** The Kickoff says Adam has 0.40.6 and the team still needs to agree one version | P-19 | Record one version in the Decisions Log | Mahmoud, Adam, Nader |
| 4 | **Placeholder hash.** The scenario hash is the SHA-256 of an empty input | P-06 | Replace with a real test or public hash before Week 3 | Mahmoud |
| 5 | **Attacker domains may be real.** `niletech-support.com` and `cdn-update-check.net` could exist publicly | P-05 | Treat as text only; never resolve or scan; consider reserved `.example` names | Mahmoud |
| 6 | **Scenario file incomplete.** It ends with an empty "## 4" heading | P-02 | Confirm nothing was cut off | Mahmoud |

## 9. Issues table (fill during use)

| Date (UTC) | Item ID | What went wrong | Action | Owner | Status |
|---|---|---|---|---|---|
| | | | | | |

## 10. Sign-off

| Checkpoint | Completed by | Date (UTC) | Cross-reviewer | Date (UTC) |
|---|---|---|---|---|
| Before Week 1 | Mina | | Nader | |
| End of Week 1 | Mina | | Nader | |
| End of Week 2 | Mina | | Nader | |
| End of Week 3 | Mina | | Nader | |
| Before final submission (Week 4) | Mina | | Nader | |
