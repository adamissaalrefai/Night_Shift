# Week 1 Cross-Review: Nada's Deliverables

- **Reviewer:** Mina (Validation and Evidence Lead). Draft prepared 2026-10-31; Mina confirms by signing the table at the end.
- **Reviewed:** the five files in `week1/` of branch `docs/add-shared-soc-prompt`: accuracy review v1.1, benchmark and test procedure v1.0, SOC prompt library v1.1, benchmark registry v1.0, intake review v1.0.
- **Checked against:** the evidence and outputs in `week1/mahmoud-benchmark` and `Adam/Week1`, Answer Key v1.1, scoring workbook v1.1, hallucination log v1.1.
- **Verified true (no change needed):** Mahmoud's ten pairs have identical evidence and prompt bodies (only IDs, request times and model label differ); all ten outputs carry an Evaluation Record; none of Adam's 19 outputs do; Adam's Phi-3 set is eleven runs over five task types; the Phi-3 log-analysis misreadings in the intake review are real (details in section 1).

| # | Deliverable | Decision |
|---|---|---|
| 1 | AI accuracy review v1.1 | **Accept with corrections AR-1 to AR-7** |
| 2 | Benchmark and test procedure v1.0 | **Accept with corrections BP-1 to BP-6** |
| 3 | SOC prompt library v1.1 | **Accept with corrections PL-1 to PL-6** (not releasable as `_final` yet) |
| 4 | Benchmark registry v1.0 | **Accept with corrections BR-1 to BR-5** |
| 5 | Intake review v1.0 | **Accept with corrections IR-1 to IR-5** |

## Results to carry into the documents (scoring workbook v1.1)

| Model (Mahmoud set, one run per task) | Avg accuracy | Avg completeness | Avg usefulness | Wrong / unsupported claims | PASS / FAIL |
|---|---:|---:|---:|---|---|
| Llama 3.1 8B Q4_K_M | 3.0 | 3.2 | 2.8 | 6 / 7 | 0 / 5 |
| Mistral 7B | 2.2 | 3.4 | 2.4 | 16 / 5 | 0 / 5 |

Reading it honestly: all ten outputs fail the zero-tolerance gate, so PASS/FAIL does not separate the models. Llama is ahead on accuracy (fewer wrong claims, no critical error); Mistral is level or slightly ahead on completeness and had one Critical error (TC-02, scenario file name attached to the alert). Each figure is one run per task; differences under about 0.5 are noise. Phi-3 (11 trials, own inputs): accuracy 1.8, completeness 1.9, usefulness 1.8, 0 of 11 pass; it is not ranked with the others.

---

## 1. AI accuracy review v1.1: Accept with corrections

| ID | Correction |
|---|---|
| AR-1 | Results summary: replace "Not scored" for Llama and Mistral with the figures above. Keep Phi-3 as a separate row with its own numbers. |
| AR-2 | "Evidence sources" table: answer key is now v1.1 (dataset refs filled, cross-review requested from Nader); scoring rows and the hallucination log are populated (116 rows: 43 Mahmoud-set rows, 73 Adam-set rows). |
| AR-3 | "A sampled earlier Phi-3 log-analysis output" is `20261027-test-01-phi3`. Its three listed faults are confirmed (login from 10.10.10.15 called external; T1008; "no unknowns"). Add: `-test-02` is a degenerate loop with invented events at 16:00/18:00, a user `devbonus` and the invalid IP `192.168.1.100.100`; `-test-05` calls a benign admin baseline "Confirmed unauthorized access"; `-test-03` blames a benign IP for the SQL injection. |
| AR-4 | The statement that Phi-3 inputs differ applies to Mahmoud's set only. Adam's branch also holds **eight Llama runs on the same inputs as his Phi-3 trials** (5 log analysis, 3 alert triage). A Llama-vs-Phi-3 comparison on identical input is therefore possible for those eight; a three-model ranking is still not (no Mistral on them). |
| AR-5 | "Accuracy and evidence-traceability failures" must name the findings: nonexistent evidence files cited by Llama TC-02 (`W1_nader_endpoint-telemetry...`), Mistral TC-03 (`W1_mahmoud_sshd...`), and a nonexistent line `:L123` in Mistral TC-01; ATT&CK ID/name mismatches in Mistral TC-01/02/03 and Phi-3; internal assets listed as IOCs in both TC-03 outputs. |
| AR-6 | Add the runner pre-check finding: every Evaluation Record states "Unsupported claims identified: 0" and "Pass" on required findings, which the review contradicts for all ten (for example Llama TC-01 claims "MITRE mappings included" while the output says Not mapped). Runner self-checks must not be counted as validation. |
| AR-7 | Week 2 recommendation: Llama as operational model is supported by the numbers, but state the limits: one run per task, no benign decoy in the shared inputs (TR-04 not yet run on Llama/Mistral), a critical-free but not error-free Llama (13 wrong/unsupported claims across five outputs). Approval table: mark "Scores and answer-key comparison" complete only after Mina and Nader sign. |

## 2. Benchmark and test procedure v1.0: Accept with corrections

| ID | Correction |
|---|---|
| BP-1 | Three ID schemes are in use: `W1-T01..T05` (procedure), `TC-01..TC-05` (Mahmoud's requests), `BM-T1..T5` (scoring workbook). Add one mapping table (W1-T01 = TC-01 = BM-T1, and so on). |
| BP-2 | Naming rule `YYYYMMDD-w1-t0x-<model>.md` is not what the runners used (`20261028-w1-log-analysis-llama31`, `20261027-test-01-phi3`). Either adopt the in-use pattern for Week 1 or record the rename; the intake review already accepts the existing names. |
| BP-3 | "Automatic failure conditions" differ from the rubric gate (zero wrong or unsupported claims). Reconcile: automatic failure = Critical flag; PASS/FAIL gate = rubric. Note that with limit 0 all ten outputs fail, so add a tie-break rule (Critical count, then High count, then average score) before anyone publishes a ranking. |
| BP-4 | Replace the aggregated "TC-03 to TC-05" result row with ten rows. Fill them from `W1_mina_scoring-rubric_v1.1.xlsx` (Scoring Sheet). |
| BP-5 | Add a benign control to the alert-triage test: TR-04 (SIEM-3107 copy, `W1_mina_tr04-decoy-alert_input_v1.txt`), run on Llama and Mistral. Without it the test cannot detect a model that labels everything True Positive; Adam's Llama run on the same text did exactly that. |
| BP-6 | Add a pre-run step: hash every input and compare it with the request manifest. TC-05's manifest hash (`0ce6aa15...`) does not match the file (`70de7ad1...`). |

## 3. SOC prompt library v1.1: Accept with corrections (not releasable as final)

| ID | Correction |
|---|---|
| PL-1 | PL-01/PL-02 cite only Phi-3 trials. The handoff format asks for one matching request/output pair; cite Adam's Llama pairs (`20261027-test-01` ... `-test-05`, `20261027-triage-test-01` ... `-03`), since Llama is the operational model. |
| PL-2 | Release condition (all five entries linked to tested pairs) is not met for PL-03 and PL-04: Mahmoud's TC-03/TC-04 runs used the shared template formats C and D, not Nader's prompt modules. Run Nader's two prompts on Llama before release. |
| PL-3 | Resolve the open "no conflicting instructions" item. Nader's report prompt has 13 sections and his threat prompt 10 sections, against 9 and 9 in template formats D and C. The answer key (RW-01..09, TS-01..10) scores formats C and D. Declare the template formats authoritative, or version the template. |
| PL-4 | Add the observed failure modes the handoff format requires: invented ATT&CK ID/name pairs; citing files not in the evidence manifest; internal assets listed as IOCs; scenario indicators attached to alerts that do not contain them; status values outside `observed | scenario_context_only`; Phi-3 degenerate loops and prompt echo. Suggested wording (version it as template v1.1 only if the team approves): "Cite only filenames listed in the evidence manifest. Give an ATT&CK ID only with its exact technique name; otherwise write 'not mapped'." |
| PL-5 | Nader's three `.md` files contain literal backslash escapes (`\#`, `\-`) and CRLF line endings, so headings do not render. Clean them before they are pasted into request files. |
| PL-6 | Integration checklist: the Phi-3 Evaluation Record item is still open and is now a blocker for scoring Phi-3 as reviewed evidence. |

## 4. Benchmark registry v1.0: Accept with corrections

| ID | Correction |
|---|---|
| BR-1 | Add evidence file name and SHA-256 per test (inventory in Answer Key v1.1, section 1). Flag the TC-05 mismatch. |
| BR-2 | Add a third table: Adam's eight Llama runs on his trial inputs, with request IDs, so the registry lists everything on `Adam/Week1`. |
| BR-3 | Phi-3 table: request IDs for `-test-06/07/08` are dated 20261027 but the metadata says "Requested at 2026-10-30T08:15:00Z"; the log-analysis ones say 2026-10-28T11:11:24Z. Record that the ID date is not the run date. |
| BR-4 | Note that every Phi-3 request lists "Primary model: Llama 3.1 8B" in the execution block while the benchmark field says Phi-3 Mini; this is a template field misuse, not a model mix-up. |
| BR-5 | "Required review actions" 1-3 are done in v1.1 of the workbooks; update the status column from "scoring pending" to the scored result, or link the workbook. Keep action 4 (never average across the two sets) and action 5. |

## 5. Intake review v1.0: Accept with corrections

| ID | Correction |
|---|---|
| IR-1 | Adam section "Received" misses that his branch contains matching Llama prompts and outputs (8 each). State this and treat them as unreviewed until they are logged (done in hallucination log v1.1 only for the errors that matter to TR-04). |
| IR-2 | Adam's `evidence/` folder holds only a readme, yet his requests cite `evidence/SRV-WEB-01_test-0x.log` and `evidence/answer_key_20261027-test-0x.md`. Add a blocking item: raw evidence files and his answer keys must be committed (Answer Key v1.1 Appendix B reconstructs the keys from the pasted blocks). |
| IR-3 | Blocking item 3 is correct as written; extend it with the other four log-analysis outputs and the alert/summary/report/IOC trials (see AR-3). |
| IR-4 | Mahmoud section: the lab setup guide has corrupted characters (`\u0000.40.11` for 0.40.11, `\u0000.2` for 0.2, `\num_ctx`, `\temperature`). Ask Mahmoud to fix the file so the recorded settings are readable. |
| IR-5 | "Integration status" row for answer key, scoring and hallucination log: change from "pending" to "delivered v1.1; awaiting Nader's cross-review of the key and Mina's sign-off." |

---

## Sign-off

| Item | Reviewer | Status | Date UTC |
|---|---|---|---|
| Cross-review of five Nada deliverables | Mina | `[to sign]` | |
| Nada acknowledges corrections | Nada | `[pending]` | |
