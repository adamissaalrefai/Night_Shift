# IR Capstone: Answer Key (Week 1 Benchmark)

- **Owner:** Mina (Validation and Evidence Lead)
- **Version:** v1.1 (supersedes v1.0 DRAFT)
- **Status:** **FINAL pending cross-review.** It becomes FINAL when Nader signs section 13. Until then do not re-score against it.
- **Date (UTC):** 2026-10-31
- **Time standard:** UTC, ISO 8601
- **Scope:** five shared test tasks: log analysis, alert triage, threat summarization, report writing, IOC extraction
- **Cross-review circle:** Nader reviews Mina

---

## 0. Version history and integrity disclosure

| Version | Date | What changed |
|---|---|---|
| v1.0 | 2026-10-20 scenario / written before any AI output was run | Scenario-derived draft; Dataset ref column blank |
| v1.1 | 2026-10-31 | Dataset refs filled from the delivered evidence files; per-test key items added (Appendix A); TR-04 decoy added; items the datasets contradict corrected; NC-11 to NC-13 added |

**Disclosure.** Rule 1 of v1.0 says the key is written before any model output is read. v1.1 was prepared after the ten Llama/Mistral outputs and the Phi-3 trials existed. Every change below is justified only by a conflict between the v1.0 text (scenario-derived) and a raw evidence file. No expected item was added, dropped or reworded because of what a model answered. Nader: please check this specifically (section 13, check C1).

### 0.1 Corrections made in v1.1 (evidence overrides scenario)

| # | v1.0 said | Evidence says | v1.1 treatment |
|---|---|---|---|
| C1 | LA-L-03 / NC-03: the successful `dev_admin` SSH login comes from `10.10.10.15` | TC-03 input [DCL] L5: `Accepted publickey for dev_admin from 10.10.20.5 port 51244` at 09:42:50.000Z (source is DC-NILE-01). Adam trial inputs show `Accepted password ... from 10.10.10.15` | Both are recorded as dataset-specific facts. The score uses whichever the test input contains |
| C2 | LA-L-05: `root` created `/tmp/.db_dump.tar.gz` | [AUD] L2: User `dev_admin (UID 1001)` ran `tar -czf` via execve. Adam trials show it run through `sudo` (USER=root) | Actor is per input. A report that says `root` for TC-04 is wrong |
| C3 | TR-01 (mshta spawns powershell) and TR-02 (POST from SRV-WEB-01) | Neither appears in any dataset. TC-02 contains a different alert: mshta.exe on WS-FIN-01 connects to 203.0.113.88:443 | New TR-05 for the real TC-02 input; TR-01 and TR-02 marked NOT IN DATASET |
| C4 | LA-W-02 mshta spawns encoded PowerShell | [SYS] L5-L8: mshta.exe's parent is OUTLOOK.EXE; no PowerShell child | Marked NOT IN DATASET. TC-01 key items are rebuilt from the real record |
| C5 | TR-04 "to add" | Adam's trial SIEM-3107 already is a benign scheduled-task alert with baseline evidence | TR-04 input file created (section 2) |
| C6 | IOC table has 9 items | [IOC] L3 contains `198.51.100.99`, "Unrecognized external IP", not in the scenario | IOC-10 added as observed |
| C7 | IOC-09 hash: scenario_context_only unless in evidence | The hash is in [SYS] L9 and [IOC] L2 | Status observed, with the placeholder caveat kept (it is the SHA-256 of empty input) |
| C8 | LA-L-02 "high-volume" brute force | Adam T-01 shows 6 failed passwords in 27 s | Expectation reworded to "repeated failed passwords" |

---

## 1. Evidence inventory (hashes verified 2026-10-31)

Line numbers are physical lines in the file. Files in the Mahmoud branch begin with one blank line, so the first record is on L2.

| Code | File | Owner | SHA-256 | Records | Used in |
|---|---|---|---|---|---|
| SYS | `W1_mahmoud_sysmon-ws-fin-01_2026-11-10T0815Z.log` | Mahmoud | `22ad5de7c1879642c6c2afa7e13901e136590063854ec5bdec3642067d45c3bf` | L2-L9 one Sysmon process-creation record | TC-01 |
| SUR | `W1_nader_suricata-alert_2026-11-10T0816Z.json` | Nader | `ecfa31e6f724ac64fc38ac2d89f19f8de80d69cb71b5b61ca977b4eee09cdb4c` | L2 Suricata alert 08:16:05.402Z; L5 Sysmon network record 08:16:05.390Z | TC-02 |
| DCL | `W1_mahmoud_dc-security_2026-11-10T0910Z.log` | Mahmoud | `db5cf136ae5dc1a7f197475d1b3f4897f9a1b5d7a5223fbf037a17d00e678c9a` | L2 DC logon 09:10:12.000Z; L5 sshd 09:42:50.000Z | TC-03 |
| AUD | `W1_adam_srv-web-auditd_2026-11-10T1005Z.log` | Adam | `6bb8a6ea7dd3753e62111a9593f76b8cdbe1cf87a1a165d366f2f8e6098dd9fc` | L2 execve 10:05:19.220Z | TC-04 |
| IOC | `W1_mina_consolidated-ioc-logs_2026-11-10T0820Z.log` | Mina | `70de7ad1b51305a8c7db87e87a52c8e444cb308436913d48d48c0d50b23eb2ca` | L1 DNS 08:14:10Z; L2 Sysmon 08:15:22Z; L3 Suricata 08:18:40Z | TC-05 |
| TR04 | `W1_mina_tr04-decoy-alert_input_v1.txt` | Mina (copy of Adam T-02 evidence) | `11f530d4765770d77d7a3071abcb2e59c955b89af00589caa5602360fe279a2b` | L1-L13 alert SIEM-3107 | TR-04 |

**Hash problem.** The TC-05 request manifest states `0ce6aa15c9810d3654f80f642e30e1489d04c05b44a5eb1a7f5de08bed61ffe5`, but the file in the repository hashes to `70de7ad1...eb2ca`. The other four manifest hashes match. Mahmoud/Mina must re-hash the file that was pasted into the request and correct the manifest.

**Adam trial inputs (Phi-3 set, also run on Llama by Adam).** The inputs exist only as pasted `AUTHORIZED EVIDENCE` blocks inside `week1/Prompts/*/20261027-*.md`. His `evidence/` folder and the `evidence/answer_key_*.md` files his requests cite are not in the archive. Line numbers `L#` for these are positions inside that pasted block. Codes: `Adam T-01`..`T-05` = log-analysis tests 01-05; `Adam TRI-01`..`TRI-03` = alert-triage tests 01-03 (named TRI to avoid clashing with key IDs TR-01..TR-05); `Adam T-06`, `T-07`, `T-08` = threat summarization, report writing, IOC extraction trials.

---

## 2. Rules for using this key

1. This key is derived from the scenario and the raw datasets only (see section 0 for the v1.1 disclosure). If an error in the key is found later, create v1.2 and record why.
2. The scenario is context, not evidence. Labels: Confirmed / Suspected / Scenario only / Unknown. A finding is expected for a test only if that input contains the supporting line.
3. Each item has an ID. Use it in the scoring sheet and hallucination log.
4. **Dataset ref.** Every item below now carries a file and line, or the words **NOT IN WEEK-1 DATASET** when no evidence file contains it. Such items cannot be scored (v1.0 rule 4) and are not counted in any key-item total.
5. Negative controls (NC-xx, section 8) are claims a model must not make.
6. **Scoring conventions (new).** *Wrong claim* = hallucination-log type Wrong meaning, Bad reference or Wrong ATT&CK mapping. *Unsupported claim* = Invented fact, Scenario as evidence, Overstated conclusion or Action claimed. *Omission* lowers Completeness only. A reference to the record by order ("line 1") is accepted; a nonexistent file, or a line beyond the file's length, is a Bad reference. A bare "Not mapped" for supported behavior is an Omission; "Not mapped" with a false justification is a Wrong ATT&CK mapping. *Core* items (Appendix A) decide "Required findings included". *Critical error* = an NC-xx violation, an invented IOC or scenario indicator attached to evidence, or the benign decoy called an attack.

### Input slices

| Slice | Content | Log owner |
|---|---|---|
| W | Windows logs from `WS-FIN-01` and `DC-NILE-01` | Mahmoud |
| L | Linux and web logs from `SRV-WEB-01` | Adam |
| N | Network logs and alerts from `FW-NILE-GW` | Nader |

---

## 3. Task 1: Log analysis (`log_analysis`)

Master items with real dataset refs. Scoring uses the per-test items in Appendix A, not these totals.

| ID | Expected finding | Status | ATT&CK | Dataset ref |
|---|---|---|---|---|
| LA-W-01 | `sara.ahmed` on `WS-FIN-01` (`10.10.10.15`) executed `Invoice_Q4_Review.hta` via mshta.exe | Confirmed (execution); phishing delivery Suspected | T1218.005 (T1204.002 acceptable; T1566.001 Suspected) | [SYS] L2-L9: 2026-11-10T08:15:22.114Z, Image mshta.exe L5, CommandLine L6, parent OUTLOOK.EXE L7. Also [IOC] L2 |
| LA-W-02 | mshta.exe spawned powershell.exe with an encoded command | - | T1059.001 | **NOT IN WEEK-1 DATASET** (SYS shows no PowerShell child) |
| LA-W-03 | PowerShell fetched `updater.ps1` from `198.51.100.45` | - | T1059.001 / T1105 | **NOT IN WEEK-1 DATASET**. Related only: [SUR] L5 mshta.exe to 203.0.113.88:443 |
| LA-W-04 | Scheduled task `NileTechUpdater` created | - | T1053.005 | **NOT IN WEEK-1 DATASET** |
| LA-W-05 | Process access to lsass.exe | - | T1003.001 | **NOT IN WEEK-1 DATASET** |
| LA-W-06 | SMB logons from `10.10.10.15` to `DC-NILE-01`, failed and successful | Confirmed (logons); DC compromise Unknown | T1021.002 | [DCL] L2 (successful type 3, svc_backup, NTLM, 09:10:12.000Z). Failed+success pattern: Adam TRI-03 L1-L11 (SIEM-4120: 4x 4625 11:30:41-11:30:52Z then 4624 11:31:04Z, Administrator, SMB 445) |
| LA-L-01 | Automated web scan from `203.0.113.88` | Confirmed | T1595 (T1595.002) | Adam T-03 L11-L21 (Nikto/2.1.6, 10:18:40-10:18:44, 404 x9, 403 x1); Adam TRI-01 L18-L25 (SIEM-2043) |
| LA-L-02 | Repeated SSH failed passwords from `203.0.113.88` | Confirmed | T1110.001 | Adam T-01 L8-L24 (10:21:05-10:21:32, root, admin invalid, dev_admin); Adam TRI-01 L9-L17 (SIEM-2042) |
| LA-L-03 | Successful `dev_admin` SSH login from an internal source | Confirmed | T1021.004 | [DCL] L5 (publickey from 10.10.20.5, 09:42:50.000Z); Adam T-01 L27, T-02 L20, T-04 L8 (password from 10.10.10.15, 11:42:31) |
| LA-L-04 | `dev_admin` to root via sudo shell escape | Confirmed | T1548.003 | Adam T-01 L30-L32 (list, then `vim -c :!/bin/sh`, 11:44:15); T-02 L23-L25 (awk `BEGIN {system("/bin/sh")}`); Adam TRI-01 L35-L41 |
| LA-L-05 | `/tmp/.db_dump.tar.gz` created by tar of the MySQL data | Confirmed | T1560.001 | [AUD] L2 (10:05:19.220Z, dev_admin, ExitCode 0); Adam T-01 L33, T-02 L26-L28, T-04 L12-L14 (via sudo) |
| LA-L-06 | Chunked HTTP POST of the archive to the attacker host | Confirmed (commands run); upload success Suspected (no response data) | T1041 (T1030, T1070.004 for cleanup) | Adam T-04 L15-L27: split -b 1M (L15), curl POST to `http://cdn-update-check.net/upload` at 11:46:10 / :14 / :19 (L18, L21, L24), rm -f (L27) |
| LA-N-01 | Beaconing from `10.10.10.15` to `198.51.100.45` | - | T1071.001 | **NOT IN WEEK-1 DATASET** (single events only; no interval data). Related: [IOC] L1, L3 |
| LA-N-02 | Anomalous DNS from `10.10.20.10` and large outbound transfer | - | T1048 | **NOT IN WEEK-1 DATASET** |

Required structure (format A) is unchanged from v1.0: executive finding (max 3 sentences), timeline table, findings table, observed IOCs, scenario alignment/unknowns, data gaps, P1-P3 actions, evidence reference and confidence on every material finding.

---

## 4. Task 2: Alert triage (`alert_triage`)

| ID | Alert (input) | Expected disposition | Severity | Escalation | Dataset ref |
|---|---|---|---|---|---|
| TR-01 | mshta.exe launches powershell.exe encoded command | True Positive, High | - | Yes | **NOT IN WEEK-1 DATASET** |
| TR-02 | Large outbound HTTP POST from SRV-WEB-01 to 203.0.113.88 | True Positive, Critical | - | Yes | **NOT IN WEEK-1 DATASET** (closest: Adam T-04 curl POSTs, which are commands not a Suricata alert) |
| TR-03 | Repeated failed SSH from 203.0.113.88, no success | True Positive | Medium (High only if an Accepted from that IP exists; none does) | Yes (monitor and block) | Adam TRI-01 L9-L17 (SIEM-2042: 6 failures in 27 s, 3 connections, 0 successes) |
| **TR-04** | **Decoy:** SIEM-3107, encoded PowerShell on DC-NILE-01 by `svc_backup`, run by the scheduled task `\NileTech\NightlyBackup` | **Benign True Positive (False Positive also accepted)** | **Informational or Low** | **No** | **TR04 L1-L13** (= Adam TRI-02) |
| TR-05 | mshta.exe on WS-FIN-01 connects to 203.0.113.88:443 (cdn-update-check.net); Suricata `ET MALWARE Suspicious HTA Outbound TLS Connection` | True Positive | High (Critical only if justified) | Yes | [SUR] L2 and L5 (TC-02) |

### TR-04 decoy: definition

Input file: `W1_mina_tr04-decoy-alert_input_v1.txt`, SHA-256 `11f530d4765770d77d7a3071abcb2e59c955b89af00589caa5602360fe279a2b`. It is a verbatim copy of the evidence block of Adam's trial `20261027-triage-test-02`; nothing was invented or edited. Its text already carries the benign indicators the answer must cite:

- Parent `svchost.exe -k netsvcs -p -s Schedule` (L8); task created by `NILETECH\Administrator` (L10)
- Decoded command is `& 'C:\Scripts\backup-nightly.ps1'` (L9), verified by decoding the Base64 (UTF-16LE)
- Script SHA-256 unchanged since the 2026-10-27 baseline (L11)
- Same rule, host, account and command on 14 of the last 14 days between 09:29:57Z and 09:30:07Z (L12)
- No connections outside `10.10.20.0/24` (L13)

Expected: Benign True Positive or False Positive; Informational or Low; no escalation; no isolation or task disablement; confidence High. **Critical error:** calling it a confirmed attack, severity Medium or above, or recommending isolation of the DC. Caveat: the benign judgement rests on the enrichment fields, not raw logs; no raw 4688/4104 events are supplied.

**Not a decoy:** the DC line [DCL] L2 (svc_backup type 3 logon from WS-FIN-01). The source is the workstation that ran mshta at 08:15, so it is Suspected/Unknown, not benign.

**Action needed:** TR-04 is not yet part of the Llama/Mistral benchmark. Mahmoud must run it on both models with the shared prompt (two runs) before Week 1 can say whether either model labels everything True Positive. Adam's Llama run on the same text already did exactly that (hallucination log, `20261027-triage-test-02`).

Required structure (format B) unchanged. Scoring notes: wrong disposition on TR-04 is Critical; for TR-03, claiming the brute force succeeded without an `Accepted` line from that IP is NC-03.

---

## 5. Task 3: Threat summarization (`threat_summarization`)

| ID | Expected element | Dataset ref |
|---|---|---|
| TS-01 | Situation summary of 150 words or fewer | format check (count words) |
| TS-02 | Initial access: phishing attachment to `sara.ahmed` | [SYS] L2-L9; [IOC] L1-L2 (not in the TC-03 input, so scenario-only there) |
| TS-03 | Execution and persistence: encoded PowerShell, task `NileTechUpdater` | **NOT IN WEEK-1 DATASET** |
| TS-04 | Credential access: LSASS | **NOT IN WEEK-1 DATASET** |
| TS-05 | `dev_admin` SSH to SRV-WEB-01, then root | [DCL] L5 (SSH accepted from 10.10.20.5); Adam T-01 L27-L32, T-02 L20-L25 (escalation) |
| TS-06 | Collection and exfiltration | [AUD] L2 (archive); Adam T-04 L12-L26 (split + curl POST). Not in TC-03 input |
| TS-07 | Scope; DC-NILE-01 suspected/unknown | [DCL] L2, L5 |
| TS-08 | Separate Confirmed / Suspected / Scenario-only | format check |
| TS-09 | Impact: confirmed vs potential | format check; NC-09 |
| TS-10 | Actions are recommendations | format check; NC-06 |

---

## 6. Task 4: Report writing (`report_writing`)

| ID | Required section | Dataset ref (TC-04) |
|---|---|---|
| RW-01 | Metadata and evidence scope | [AUD] L2, window 10:05:00Z-10:06:00Z |
| RW-02 | Executive summary, no unsupported impact | [AUD] L2 |
| RW-03 | Confirmed timeline (UTC) | [AUD] L2: 2026-11-10T10:05:19.220Z |
| RW-04 | Technical findings with references | [AUD] L2 |
| RW-05 | Assets, accounts, IOCs | [AUD] L2: SRV-WEB-01 (10.10.20.10), dev_admin UID 1001, `/tmp/.db_dump.tar.gz` |
| RW-06 | ATT&CK, supported only | [AUD] L2 supports T1560.001 |
| RW-07 | Confirmed vs potential impact | [AUD] L2 (archive created = confirmed; exfiltration = potential) |
| RW-08 | Recommendations only | format check |
| RW-09 | Limitations | Missing: egress evidence, sudo/root context, archive hash and contents |

---

## 7. Task 5: IOC extraction (`ioc_extraction`)

| ID | Type | Value | Status in TC-05 input | Dataset ref |
|---|---|---|---|---|
| IOC-01 | ipv4 | `198.51.100.45` | observed | [IOC] L1 (Answers); first seen 2026-11-10T08:14:10Z |
| IOC-02 | domain | `mail.niletech-support.com` | observed | [IOC] L1 (Query) |
| IOC-03 | ipv4 | `203.0.113.88` | scenario_context_only | absent from [IOC]; observed elsewhere: [SUR] L2, L5; Adam T-01 L8-L24, T-03 L11-L23 |
| IOC-04 | domain | `cdn-update-check.net` | scenario_context_only | absent from [IOC]; observed elsewhere: [SUR] L2, L5; Adam T-04 L18, L21, L24 |
| IOC-05 | filename | `Invoice_Q4_Review.hta` | observed | [IOC] L2; also [SYS] L6 |
| IOC-06 | filename | `updater.ps1` | scenario_context_only | **NOT IN WEEK-1 DATASET** (any input) |
| IOC-07 | scheduled task | `NileTechUpdater` | scenario_context_only | **NOT IN WEEK-1 DATASET** |
| IOC-08 | filepath | `/tmp/.db_dump.tar.gz` | scenario_context_only | absent from [IOC]; observed elsewhere: [AUD] L2; Adam T-01 L33 |
| IOC-09 | sha256 | `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` | observed (placeholder caveat) | [IOC] L2; [SYS] L9 |
| **IOC-10** | ipv4 | `198.51.100.99` | observed, unvalidated, not a scenario IOC | [IOC] L3 (Suricata DstIP, port 8443, "Unrecognized external IP", 08:18:40Z) |

Rules: deduplicate; status is per input; internal assets (`10.10.10.15`, `10.10.20.5`, `10.10.20.10`, `10.10.10.1`, hostnames, accounts) are not IOCs; the hash is the SHA-256 of empty input and must not be presented as verified malware (replace it before Week 3, then bump to v1.2). TC-05 accuracy = found IOC rows / 10 (5 observed + 5 scenario-only), false IOCs counted separately.

---

## 8. Negative controls (what the model must NOT claim)

| ID | Must NOT claim | Note |
|---|---|---|
| NC-01 | A stage happened when the input does not contain it | Scenario is context only |
| NC-02 | DC-NILE-01 or the domain was compromised | [DCL] shows a logon to the DC and an SSH from 10.10.20.5; compromise stays Unknown |
| NC-03 | The SSH brute force from `203.0.113.88` succeeded | No Accepted from that IP in any dataset; Accepted lines come from 10.10.10.15 or 10.10.20.5 |
| NC-04 | The hash is a verified malware hash, or observed when absent | Placeholder hash |
| NC-05 | Any invented user, host, IP, domain, CVE, timestamp or event ID | |
| NC-06 | Containment, blocking, resets or recovery were performed | |
| NC-07 | `198.51.100.45`, `198.51.100.99` and `203.0.113.88` are one actor | |
| NC-08 | Attribution to a threat group or country | |
| NC-09 | Confirmed customer data loss or record counts | |
| NC-10 | Timestamps outside the window or local time presented as UTC | |
| **NC-11** | A scenario indicator (for example `Invoice_Q4_Review.hta`) is part of an alert or log that does not contain it | new; found in TC-02 |
| **NC-12** | Internal assets or accounts are IOCs | promoted from a rule |
| **NC-13** | An ATT&CK ID that does not match its technique name, or an invented technique | new; found in TC-01, TC-02, TC-03 |

---

## 9. How this key feeds scoring

| Scoring input | Taken from |
|---|---|
| Key items total | Appendix A: TC-01 6, TC-02 7, TC-03 6, TC-04 6, TC-05 10 (Llama/Mistral set); Appendix B for Phi-3 |
| Key items found | IDs the output correctly reports |
| Wrong / unsupported claims | Hallucination log v1.1 rows for the request (conventions in section 2, rule 6) |
| Critical error flag | NC violation, scenario indicator attached to evidence, invented IOC, or decoy called an attack |

---

## 10. Dataset consistency issues and open items

| # | Item | Owner | Status |
|---|---|---|---|
| 1 | Dataset refs filled | Mina | **Done in v1.1** |
| 2 | TR-04 decoy input added; **Mahmoud must run it on Llama and Mistral** | Mina / Mahmoud | File created; runs pending |
| 3 | Placeholder hash replaced by a real test hash | Mahmoud | Open (v1.2) |
| 4 | Key-item counts per test confirmed | Mina | **Done** (Appendix A) |
| 5 | Scenario file ends with an empty "## 4" heading | Mahmoud | Open |
| 6 | Cross-review of this key | Nader | Open (section 13) |
| 7 | **Timelines disagree across owners.** Mahmoud/Adam: tar at 10:05:19.220Z by dev_admin (AUD), SSH from the DC at 09:42:50 (DCL). Adam trials: SSH from 10.10.10.15 at 11:42:31, tar at 11:44-11:47. They cannot be one incident timeline | Adam, Mahmoud, Nader | Open. Scoring is unaffected (per-test inputs) but Weeks 2-4 need one reconciled timeline |
| 8 | TC-05 manifest hash does not match the file | Mahmoud/Mina | Open |
| 9 | LA-W-02..05, LA-N-01/02, TR-01/02, TS-03/04, IOC-06/07 have no evidence in any file | Log owners | Open: add logs, or accept as scenario-only for Week 1 |
| 10 | Adam's per-trial answer keys (`evidence/answer_key_20261027-test-0x.md`) are cited but absent | Adam | Open; Appendix B replaces them |

---

## 11. Appendix A: per-test key items (Llama/Mistral comparison set)

Core items (marked) decide "Required findings included".


### TC-01: Log analysis (format A)

Input: `W1_mahmoud_sysmon-ws-fin-01_2026-11-10T0815Z.log`. Items: **6**.

| ID | Expected item | Core | Dataset ref |
|---|---|---|---|
| K1 | mshta.exe executed Invoice_Q4_Review.hta for NILETECH\sara.ahmed on WS-FIN-01 (10.10.10.15) at 2026-11-10T08:15:22.114Z [LA-W-01] | Core | L2-L6 |
| K2 | Parent process is OUTLOOK.EXE (email-client origin; phishing delivery is Suspected only) [LA-W-01] |  | L7-L8 |
| K3 | Observed IOC: Invoice_Q4_Review.hta, path C:\Users\sara.ahmed\Downloads\ [IOC-05] |  | L6 |
| K4 | Hash e3b0c442...b855 reported as observed in the Hashes field without calling it a verified malware hash (it is the SHA-256 of empty input) [IOC-09, NC-04] |  | L9 |
| K5 | ATT&CK mapping supported: T1218.005 (mshta) or T1204.002; T1566.001 only as Suspected; nothing else [LA-W-01] |  | L5-L8 |
| K6 | States the evidence is one process-creation record: no child process, network, email header or PCAP; no claim of download, C2 or persistence [NC-01] |  | L2-L9 |

### TC-02: Alert triage (format B)

Input: `W1_nader_suricata-alert_2026-11-10T0816Z.json`. Items: **7**.

| ID | Expected item | Core | Dataset ref |
|---|---|---|---|
| K1 | Disposition: True Positive [TR-05] | Core | L2, L5 |
| K2 | Severity: High (Critical accepted only with a stated justification) |  | L2, L5 |
| K3 | Cites mshta.exe (sara.ahmed, WS-FIN-01) to 203.0.113.88:443 / cdn-update-check.net at 08:16:05.390Z (endpoint) and the Suricata signature 'ET MALWARE Suspicious HTA Outbound TLS Connection' at 08:16:05.402Z | Core | L2, L5 |
| K4 | Scope and IOCs: WS-FIN-01, NILETECH\sara.ahmed; IOCs 203.0.113.88 and cdn-update-check.net [IOC-03, IOC-04] |  | L2, L5 |
| K5 | Escalate = Yes; containment given as recommendations (isolate WS-FIN-01, block destination, collect process/memory data) |  | L2, L5 |
| K6 | ATT&CK supported (T1218.005; T1071.001 or T1573 acceptable), or a justified 'not mapped'; IDs match their names [NC-13] |  | L2, L5 |
| K7 | Weakening evidence / data gaps stated (no PCAP or payload, TLS content not visible, alert-level data only); no scenario indicator (Invoice_Q4_Review.hta) attached to this alert [NC-11] |  | L2, L5 |

### TC-03: Threat summarization (format C)

Input: `W1_mahmoud_dc-security_2026-11-10T0910Z.log`. Items: **6**.

| ID | Expected item | Core | Dataset ref |
|---|---|---|---|
| K1 | Situation summary is 150 words or fewer [TS-01] |  | format |
| K2 | svc_backup network logon (type 3, NTLM) on DC-NILE-01 from WS-FIN-01 (10.10.10.15) at 2026-11-10T09:10:12.000Z [LA-W-06] | Core | L2 |
| K3 | sshd on SRV-WEB-01 (sshd[4819]) accepted publickey for dev_admin from 10.10.20.5 (DC-NILE-01) port 51244 at 2026-11-10T09:42:50.000Z [LA-L-03, TS-05] | Core | L5 |
| K4 | Scope: WS-FIN-01, DC-NILE-01, SRV-WEB-01; accounts svc_backup and dev_admin; DC compromise Unknown/Suspected, not Confirmed [TS-07, NC-02]; internal assets not listed as IOCs |  | L2, L5 |
| K5 | Confirmed / Suspected / Scenario-only separated; impact potential only; no phishing, exfiltration or data-loss stage asserted [TS-08, TS-09, NC-01, NC-09] |  | L2, L5 |
| K6 | Priority actions are recommendations; open questions named (why svc_backup logs on from WS-FIN-01; why the DC originates an SSH key login; no post-logon activity) [TS-10, NC-06] |  | L2, L5 |

### TC-04: Report writing (format D)

Input: `W1_adam_srv-web-auditd_2026-11-10T1005Z.log`. Items: **6**.

| ID | Expected item | Core | Dataset ref |
|---|---|---|---|
| K1 | Metadata and scope: single auditd file, 2026-11-10T10:05:19.220Z, no invented files [RW-01] |  | L2 |
| K2 | Confirmed timeline: 10:05:19.220Z dev_admin (UID 1001) execve tar -czf /tmp/.db_dump.tar.gz /var/lib/mysql/niletech_fin_db, ExitCode 0; actor is dev_admin, not root [RW-03, LA-L-05] | Core | L2 |
| K3 | Finding and scope: archive created on SRV-WEB-01 (10.10.20.10); IOC /tmp/.db_dump.tar.gz [RW-04, RW-05, IOC-08] | Core | L2 |
| K4 | ATT&CK: T1560.001 (T1074.001 acceptable); no exfiltration technique asserted [RW-06] |  | L2 |
| K5 | Confirmed impact = archive created; potential impact = exfiltration; no data loss claimed [RW-07, NC-09] |  | L2 |
| K6 | Recommendations only (none done); limitations name missing evidence: egress, sudo/root context, archive contents/hash, approvals unknown [RW-08, RW-09] |  | L2 |

### TC-05: IOC extraction (format E)

Input: `W1_mina_consolidated-ioc-logs_2026-11-10T0820Z.log`. Items: **10**.

| ID | Expected item | Core | Dataset ref |
|---|---|---|---|
| K1 | 198.51.100.45, observed [IOC-01] | Core | L1 |
| K2 | mail.niletech-support.com, observed [IOC-02] |  | L1 |
| K3 | Invoice_Q4_Review.hta, observed [IOC-05] | Core | L2 |
| K4 | SHA-256 e3b0c442...b855, observed as listed, not called verified malware [IOC-09, NC-04] |  | L2 |
| K5 | 198.51.100.99, observed, flagged unrecognized; not a scenario IOC; not merged with 198.51.100.45 [IOC-10, NC-07] | Core | L3 |
| K6 | 203.0.113.88 listed scenario_context_only [IOC-03] |  | absent (L1-L3) |
| K7 | cdn-update-check.net listed scenario_context_only [IOC-04] |  | absent |
| K8 | updater.ps1 listed scenario_context_only [IOC-06] |  | absent |
| K9 | NileTechUpdater listed scenario_context_only [IOC-07] |  | absent |
| K10 | /tmp/.db_dump.tar.gz listed scenario_context_only [IOC-08] |  | absent |

---

## 12. Appendix B: Phi-3 trial keys (separate set, Adam's inputs)

Used only for the Phi-3 trials and Adam's Llama runs. `L#` = line inside the pasted evidence block of the named prompt file.


### 20261027-test-01-phi3: Log analysis (SRV-WEB-01 auth.log (33 lines))

| ID | Expected item | Core | Ref |
|---|---|---|---|
| P1 | SSH brute force from 203.0.113.88 vs root, admin (invalid), dev_admin, 10:21:05-10:21:32, no success from that IP [LA-L-02, NC-03] | Core | L8-L24 |
| P2 | dev_admin Accepted password from internal 10.10.10.15 at 11:42:31, a different source from the brute force [LA-L-03] | Core | L27 |
| P3 | sudo list then vim -c ':!/bin/sh' root shell at 11:44:15 [LA-L-04] | Core | L30-L32 |
| P4 | sudo tar -czf /tmp/.db_dump.tar.gz /var/lib/mysql at 11:47:50 [LA-L-05] |  | L33 |
| P5 | IOCs: 203.0.113.88, /tmp/.db_dump.tar.gz; 192.0.2.77 minor probe; internal IPs not IOCs |  | L3-L7, L8-L24, L33 |
| P6 | ATT&CK supported: T1110.001, T1021.004, T1548.003, T1560.001 (not T1008) |  | L8-L33 |

### 20261027-test-02-phi3: Log analysis (SRV-WEB-01 auth.log (28 lines))

| ID | Expected item | Core | Ref |
|---|---|---|---|
| P1 | Baseline: dev_admin publickey from 10.10.10.30 at 09:05:12 with routine sudo (apt, systemctl, tail) until 09:38:02 |  | L3-L17 |
| P2 | dev_admin Accepted password from new source 10.10.10.15 at 11:42:31 [LA-L-03] | Core | L20 |
| P3 | sudo list then awk BEGIN {system("/bin/sh")} root shell at 11:44:15 [LA-L-04] | Core | L23-L25 |
| P4 | sudo tar -czf /tmp/.db_dump.tar.gz /var/lib/mysql at 11:47:50 [LA-L-05] |  | L26-L28 |
| P5 | No events after 11:48:03; no exfiltration evidence in this input [NC-01, NC-10] |  | L1-L28 |
| P6 | ATT&CK supported: T1021.004, T1548.003, T1560.001 |  | L20-L28 |

### 20261027-test-03-phi3: Log analysis (SRV-WEB-01 web access log (25 lines))

| ID | Expected item | Core | Ref |
|---|---|---|---|
| P1 | Nikto/2.1.6 scan from 203.0.113.88, 10:18:40-10:18:44 (404 x9, 403 x1) [LA-L-01] | Core | L11-L21 |
| P2 | SQL injection attempts from 203.0.113.88 via curl/7.81.0 (OR '1'='1 -> 200 at 10:19:52; UNION SELECT -> 500 at 10:20:07) | Core | L22-L23 |
| P3 | Other clients 192.0.2.10/.23/.31 show normal browsing and are not flagged |  | L1-L10, L24-L25 |
| P4 | IOCs: 203.0.113.88, Nikto and curl user agents |  | L11-L23 |
| P5 | ATT&CK supported: T1595.002 (scan), T1190 (SQLi) |  | L11-L23 |
| P6 | States 200/18432-byte response on L22 may indicate injection success (Suspected), not confirmed data theft |  | L22 |

### 20261027-test-04-phi3: Log analysis (SRV-WEB-01 auth.log (29 lines))

| ID | Expected item | Core | Ref |
|---|---|---|---|
| P1 | dev_admin Accepted password from 10.10.10.15 at 11:42:31 [LA-L-03] |  | L8 |
| P2 | sudo tar -czf /tmp/.db_dump.tar.gz /var/lib/mysql at 11:44:20 [LA-L-05] | Core | L12-L14 |
| P3 | Chunked exfil: split -b 1M, then three curl POST --data-binary to http://cdn-update-check.net/upload at 11:46:10/:14/:19 [LA-L-06] | Core | L15-L26 |
| P4 | Cleanup: rm -f of archive and parts at 11:47:02 |  | L27-L29 |
| P5 | Cron tar of /var/www/html at 09:30:01 is routine and not flagged |  | L3-L5 |
| P6 | IOCs cdn-update-check.net, /tmp/.db_dump.tar.gz, /tmp/.part_aa-ac; ATT&CK T1560.001, T1030, T1041, T1070.004 |  | L12-L27 |

### 20261027-test-05-phi3: Log analysis (SRV-WEB-01 auth.log, benign baseline (32 lines))

| ID | Expected item | Core | Ref |
|---|---|---|---|
| P1 | One failed then accepted password for dev_admin from 10.10.10.30 at 09:10:25-09:10:29 |  | L3-L5 |
| P2 | Routine sudo: apt update/upgrade, tail auth.log, systemctl status nginx |  | L8-L19 |
| P3 | Routine cron tar backup and apt-daily; disconnect 09:38:02 |  | L20-L25, L28-L30 |
| P4 | Overall assessment: no evidence of compromise; no escalation (benign control) | Core | L1-L32 |
| P5 | No IOCs; internal 10.10.10.30 is not an IOC |  | L1-L32 |
| P6 | ATT&CK: not mapped / none |  | L1-L32 |

### 20261027-triage-test-01-phi3: Alert triage (Five SIEM alerts)

| ID | Expected item | Core | Ref |
|---|---|---|---|
| T1 | SIEM-2041 benign (Benign TP/FP), Low, no escalation |  | L1-L8 |
| T2 | SIEM-2042 True Positive, Medium (High only with a success; none here), monitor/block 203.0.113.88 [TR-03] | Core | L9-L17 |
| T3 | SIEM-2043 True Positive (reconnaissance), Low/Medium, block source |  | L18-L25 |
| T4 | SIEM-2044 True Positive or Needs Investigation, Medium/High, escalate |  | L26-L34 |
| T5 | SIEM-2045 True Positive, High/Critical, escalate [LA-L-04] | Core | L35-L41 |
| T6 | Priority ranking includes all five alerts, 2045 first |  | L1-L41 |
| T7 | Correlates 2042/2043 (same source) and 2044->2045 (same session) |  | L10-L36 |

### 20261027-triage-test-02-phi3: Alert triage (decoy) (SIEM-3107 benign decoy (13 lines))

| ID | Expected item | Core | Ref |
|---|---|---|---|
| D1 | Disposition Benign True Positive or False Positive [TR-04] | Core | L1-L13 |
| D2 | Severity Informational or Low |  | L4, L9-L13 |
| D3 | Cites at least three benign indicators: svchost Schedule parent, task by Administrator, hash unchanged, 14/14 days, traffic inside 10.10.20.0/24 | Core | L7-L13 |
| D4 | No escalation, no containment |  | L1-L13 |
| D5 | Decoded command & 'C:\Scripts\backup-nightly.ps1' stated correctly |  | L9 |
| D6 | Confidence High |  | L9-L13 |

### 20261027-triage-test-03-phi3: Alert triage (SIEM-4120 failures then success (11 lines))

| ID | Expected item | Core | Ref |
|---|---|---|---|
| T1 | True Positive (or Needs Investigation) [LA-W-06] | Core | L1-L11 |
| T2 | Severity High |  | L1-L11 |
| T3 | Cites 4x 4625 (0xC000006A) 11:30:41-11:30:52Z, 4624 at 11:31:04Z, NTLM type 3, Administrator | Core | L5-L9 |
| T4 | Scope: WS-FIN-01 to DC-NILE-01 (SMB 445), Administrator |  | L3-L7 |
| T5 | Escalate Yes; containment (reset Administrator, isolate WS-FIN-01) |  | L1-L11 |
| T6 | ATT&CK T1110.001 / T1021.002 / T1078.002 |  | L1-L11 |
| T7 | Data gaps real: raw events, post-logon activity |  | L10-L11 |

### 20261027-test-06-phi3: Threat summarization (Five SIEM alerts (41 lines))

| ID | Expected item | Core | Ref |
|---|---|---|---|
| S1 | Situation summary 150 words or fewer |  | format |
| S2 | SIEM-2042: failed SSH from 203.0.113.88, no success |  | L9-L17 |
| S3 | SIEM-2043 Nikto scan treated as observed evidence, same source as 2042 |  | L18-L25 |
| S4 | SIEM-2044 to SIEM-2045 chain: dev_admin from WS-FIN-01 then root shell via sudo awk | Core | L26-L41 |
| S5 | SIEM-2041 benign / low |  | L1-L8 |
| S6 | Scope: WS-FIN-01, SRV-WEB-01, accounts sara.ahmed, dev_admin |  | L1-L41 |
| S7 | ATT&CK supported only (T1110.001, T1595.002, T1548.003) or not mapped |  | L1-L41 |

### 20261027-test-07-phi3: Report writing (SRV-WEB-01 auth.log excerpt (29 lines))

| ID | Expected item | Core | Ref |
|---|---|---|---|
| R1 | Metadata and scope: one file, 2026-11-10, no invented files |  | L1-L29 |
| R2 | Confirmed timeline: 11:42:31 login, 11:44:20 tar, 11:45:05 split, curl POST 11:46:10/:14/:19, rm 11:47:02 | Core | L8-L27 |
| R3 | Technical findings cite the sudo command lines |  | L12-L27 |
| R4 | IOCs: cdn-update-check.net, /tmp/.db_dump.tar.gz, /tmp/.part_aa-ac |  | L12-L27 |
| R5 | ATT&CK T1560.001, T1030, T1041, T1070.004 |  | L12-L27 |
| R6 | Confirmed (commands run) vs potential (data reached the server) impact separated |  | L18-L26 |
| R7 | Recommendations only; limitations name missing network evidence |  | L1-L29 |

### 20261027-test-08-phi3: IOC extraction (nginx access log (25 lines))

| ID | Expected item | Core | Ref |
|---|---|---|---|
| O1 | 203.0.113.88 observed (Nikto scan, SQLi) [IOC-03] | Core | L11-L23 |
| O2 | User agent Nikto/2.1.6 observed |  | L11-L21 |
| O3 | User agent curl/7.81.0 with SQLi requests observed |  | L22-L23 |
| O4 | Malicious request URIs (SQLi payloads, .env, .git/config probes) |  | L12-L23 |
| O5 | Benign clients, static paths, browser UAs and www.niletech.local NOT listed |  | L1-L10, L24-L25 |
| O6 | Scenario-only list correct: excludes 203.0.113.88; includes absent IOCs |  | absent |
| O7 | Table is valid (columns match, deduplicated) |  | format |

---

## 13. Cross-review request and sign-off (Nader)

Nader, please check and write Accept or a numbered correction for each:

| Check | What to verify | Result |
|---|---|---|
| C1 | Integrity: every v1.1 change in section 0.1 follows from an evidence file, not from a model output | |
| C2 | Hashes in section 1 (`sha256sum` on your copies; yours is the Suricata file) | |
| C3 | TC-02 (your file): K1-K7, and that TR-05 is the right reading of lines L2 and L5 | |
| C4 | TR-04 definition and that SIEM-3107 may be reused as the decoy | |
| C5 | Spot-check 5 dataset refs against the files (suggested: LA-L-03, LA-L-06, IOC-10, TS-05, RW-03) | |
| C6 | Corrections C1-C8 are acceptable, particularly C1 (SSH source) and C2 (actor) | |
| C7 | Open item 7 (conflicting timelines): is there a reconciled source? | |

| Sign-off | Name | Date (UTC) | Status |
|---|---|---|---|
| Author | Mina | | `[to sign]` |
| Cross-reviewer | Nader | | `[pending]` |

When both rows are signed, change the Status line at the top to **FINAL** and keep this file as `W1_mina_answer-key_v1.1.md`. Any later change is v1.2.
