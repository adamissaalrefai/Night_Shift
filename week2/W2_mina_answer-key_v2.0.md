# IR Capstone: Answer Key, Week 2 (triage and log-analysis scope)

- **Owner:** Mina (Validation and Evidence Lead)
- **Version:** v2.0 **DRAFT** (builds on v1.1; v1.1 stays the reference for the Week 1 benchmark)
- **Date (UTC):** 2026-11-11
- **Status:** DRAFT until (a) Mina signs and (b) Nader signs section 9. Do not score Week 2 outputs against it before then.
- **Model under test:** Llama 3.1 8B Instruct Q4_K_M only (context 8192, temperature 0.2, seed 42, no SYSTEM line). Mistral and Phi-3 are Week 1 only.
- **Time standard:** UTC, ISO 8601

---

## 0. What this version is, and what it is not

**It is:** the Week 2 triage key (8 alerts), the status of the log-analysis items per source, the claim-counting rules needed for a hallucination rate, and the data still missing.

**It is not a new answer key built from new raw logs.** No Week 2 raw log files were available when this was prepared, and the two branch archives (`week1/mahmoud-benchmark`, `Adam/Week1`) arrived as 0-byte files. Every expected answer below is carried unchanged from Answer Key v1.1 (section 2 and Appendix B). Line references (`L#`) are the ones recorded in v1.1 and were **not re-verified** against the files for this version.

**Integrity disclosure.** v1.1 already admitted that it was written after Week 1 model outputs existed. v2.0 adds no expected item and changes none because of what a model answered. The additions are structural: the alert set, a priority ranking, metric definitions and a proposed PASS rule. Nader: please check this in section 9, check W1.

---

## 1. Week 2 scope and evidence status per log source

| Slice | Owner | Key items (v1.1) | Evidence in hand | Week 2 action |
|---|---|---|---|---|
| W: Windows | Mahmoud | LA-W-01 to LA-W-06 | LA-W-01 (SYS), LA-W-06 (DCL, and SIEM-4120). **LA-W-02, 03, 04, 05 have no evidence in any file** | Mahmoud supplies the missing events (encoded PowerShell child, `updater.ps1` fetch, task `NileTechUpdater`, LSASS access) or the team marks them scenario-only |
| L: Linux and web | Adam | LA-L-01 to LA-L-06 | All six exist, but only as pasted blocks inside Adam's prompt files. His `evidence/` folder and `answer_key_*.md` files are not in the archive | Adam commits the raw files and their hashes |
| N: Network | Nader | LA-N-01, LA-N-02 | **None.** Single events only; no beacon interval data, no DNS exfiltration records | Nader supplies Zeek `conn`, `dns`, `http` and Suricata `eve.json` covering the window |

Items with no evidence cannot be scored (v1.0 rule 4) and are not counted in any key-item total.

---

## 2. Week 2 alert set (the triage key)

Expected answers are carried from v1.1. Accepted values are what a correct answer may say.

| ID | Alert | v1.1 reference | Accepted disposition | Accepted severity | Escalate | Benign control |
|---|---|---|---|---|---|---|
| AS-01 | SIEM-2041: one failed then one successful logon, same console, 6 s apart | TRI-01 T1 (L1-L8) | Benign True Positive or False Positive | Low | No | **Yes** |
| AS-02 | SIEM-2042: repeated SSH failures from 203.0.113.88 | TR-03; TRI-01 T2 (L9-L17) | True Positive | Medium (High only with an Accepted from that IP; none exists) | Yes: monitor and block | No |
| AS-03 | SIEM-2043: Nikto scan from 203.0.113.88 | TRI-01 T3 (L18-L25) | True Positive (reconnaissance) | Low or Medium | Block source | No |
| AS-04 | SIEM-2044: dev_admin session from WS-FIN-01 | TRI-01 T4 (L26-L34) | True Positive or Needs Investigation | Medium or High | Yes | No |
| AS-05 | SIEM-2045: root shell via sudo awk escape | TRI-01 T5 (L35-L41); LA-L-04 | True Positive | High or Critical | Yes | No |
| AS-06 | SIEM-3107: encoded PowerShell on DC-NILE-01, svc_backup | **TR-04** (TR04 L1-L13); TRI-02 D1-D6 | Benign True Positive or False Positive | Informational or Low | **No** | **Yes (decoy)** |
| AS-07 | SIEM-4120: 4 failed then 1 successful SMB logon, Administrator, WS-FIN-01 to DC-NILE-01 | TRI-03 T1-T7 (L1-L11); LA-W-06 | True Positive (Needs Investigation accepted) | High | Yes: reset Administrator, isolate WS-FIN-01 | No |
| AS-08 | Suricata ET MALWARE HTA outbound TLS plus mshta.exe to 203.0.113.88:443 | TR-05; TC-02 K1-K7 (SUR L2, L5) | True Positive | High (Critical only with a stated justification) | Yes | No |

### Required evidence per alert (what a correct answer must cite)

- **AS-01:** failure and success on the same console, 6 s apart.
- **AS-02:** 6 failures in 27 s, 3 connections, users root, admin (invalid) and dev_admin, 0 successes (T-01 L8-L24, 10:21:05 to 10:21:32).
- **AS-03:** external Nikto scan, 11 requests in 4 s, same source as AS-02.
- **AS-05:** `sudo` list, then a shell escape to root, following AS-04.
- **AS-06:** parent `svchost.exe -k netsvcs -p -s Schedule`; task `\NileTech\NightlyBackup` created by `NILETECH\Administrator`; decoded command is `& 'C:\Scripts\backup-nightly.ps1'`; script hash unchanged since 2026-10-27; same rule fired 14 of 14 days between 09:29:57Z and 09:30:07Z; no connections outside `10.10.20.0/24`. Caveat: the benign judgement rests on enrichment fields, not on raw 4688 or 4104 events.
- **AS-07:** 4x event 4625 (0xC000006A) from 11:30:41Z to 11:30:52Z, then 4624 at 11:31:04Z, NTLM type 3, Administrator, SMB 445. ATT&CK supported: T1110.001, T1021.002, T1078.002.
- **AS-08:** mshta.exe (sara.ahmed, WS-FIN-01) to 203.0.113.88:443 / cdn-update-check.net at 08:16:05.390Z; signature at 08:16:05.402Z. Data gaps that must be stated: no PCAP or payload, TLS content not visible, alert-level data only.

### Expected correlations and priority

- AS-02 and AS-03 share the source 203.0.113.88.
- AS-04 leads to AS-05 (same dev_admin session).
- Among the five Adam alerts, AS-05 ranks first (v1.1 T6).
- **Proposed full ranking (Mina's judgement, needs review):** P1 AS-05, AS-07, AS-08, AS-04; P2 AS-02, AS-03; P3 AS-01, AS-06.

### Benign coverage

Two of eight alerts are benign (AS-01, AS-06). The Day 0 proposal was a minimum of two to three. **A log owner should add one more benign alert** (for example a routine web-server or admin alert). Do not invent its content here; it must come from the datasets.

---

## 3. Negative controls (unchanged from v1.1 section 8)

NC-01 to NC-13 apply to every Week 2 request. The ones most likely to be tested by the alert set:

| ID | Must NOT claim | Alerts |
|---|---|---|
| NC-02 | DC-NILE-01 or the domain was compromised | AS-06, AS-07 |
| NC-03 | The SSH brute force from 203.0.113.88 succeeded | AS-02 |
| NC-06 | Containment, blocking or resets were performed | all |
| NC-11 | A scenario indicator (for example `Invoice_Q4_Review.hta`) is part of an alert that does not contain it | AS-08 |
| NC-13 | An ATT&CK ID that does not match its technique name | all |
| Decoy rule | Calling AS-06 an attack, giving it severity Medium or above, or recommending isolation of the DC or disabling the task is a **Critical error** | AS-06 |

---

## 4. Claim counting (needed for the hallucination rate)

v1.1 logged errors only, so no rate could be calculated. From Week 2, every output is split into claims before it is scored.

1. **One claim = one checkable statement** (who, what, when, where). A table row with three facts is three claims.
2. Number the claims per request, then verify each against the raw line: **Correct** or **Wrong**.
3. Error types and their counting category (same as v1.1):
   - *Wrong claim:* Wrong meaning, Bad reference, Wrong ATT&CK mapping.
   - *Unsupported claim:* Invented fact, Scenario as evidence, Overstated conclusion, Action claimed.
   - *Omission:* logged, but lowers Completeness only; it is not a claim.
4. **Hallucination rate = (wrong claims + unsupported claims) / (correct + wrong + unsupported claims).**
5. Log each claim in the `W2 Claims` tab of `W2_mina_hallucination-log_v2.0.xlsx`. Every wrong claim is also entered in the runner's tab, so the team Summary stays correct.
6. A "no error logged" run is **not** a verified run. Only fully claim-checked runs count in the rate.

### Triage metrics (computed in the `W2 Metrics` tab)

- Triage accuracy = correct dispositions / alerts with a verdict (accepted sets in section 2 count as correct).
- Severity agreement and escalation agreement, defined the same way.
- Key-item recall = key items found / key items total (Completeness).

---

## 5. Proposed PASS rule for Week 2 (needs team approval at Day 0)

With the rubric limit of zero wrong or unsupported claims, all ten Week 1 outputs failed, so PASS/FAIL separated nothing. Proposal for Llama-only Week 2 runs:

- **PASS** = no Critical error, no High error, at most two Medium or Low errors, and format and required findings both Pass.
- Keep the 1 to 5 scores unchanged.
- Report the zero-tolerance result as well, for continuity with Week 1.

This is a proposal. Do not change the workbook thresholds until the team agrees and records it in the Decisions Log.

---

## 6. Open items

| # | Item | Owner | Status |
|---|---|---|---|
| 1 | Week 2 raw logs for W, L and N (section 1) | Mahmoud, Adam, Nader | Open |
| 2 | Adam's raw evidence files, hashes and per-trial answer keys committed | Adam | Open (carried from v1.1 item 10) |
| 3 | **Timelines disagree across owners** (carried from v1.1 item 7): SSH accepted at 09:42:50 from 10.10.20.5 and `tar` at 10:05:19 by dev_admin in Mahmoud's and Adam's single-record files, against SSH at 11:42:31 from 10.10.10.15 and `sudo tar` at 11:44 in Adam's trial blocks. One reconciled timeline is needed before Weeks 3 and 4 | Adam, Mahmoud, Nader | Open |
| 4 | **New: attacker infrastructure differs from the scenario.** The scenario sends the mshta download to `198.51.100.45`; the TC-02 alert (AS-08) shows mshta connecting to `203.0.113.88`, the scan and exfiltration host. Either the scenario or the dataset should change, or the team records that the actor used both hosts | Mahmoud, Nader | Open |
| 5 | Add at least one more benign alert (section 2) | Log owners | Open |
| 6 | Run AS-06 (decoy) on Llama under Week 2 settings: request `20261111-w2-triage-as06-decoy.md` is ready | Mina queues, Mahmoud runs | Ready |
| 7 | Review the eight Adam Llama runs in full (five log analysis, three triage); only the errors relevant to TR-04 are logged so far | Mina | Blocked: branch archive was empty |
| 8 | Placeholder hash replaced by a real test hash; TC-05 manifest hash corrected | Mahmoud | Open (v1.1 items 3, 8) |
| 9 | Ollama version agreed; system-prompt wording in the Standard aligned with the no-SYSTEM Modelfiles | Mahmoud, Adam, Nader, Nada | Open |

---

## 7. How this key feeds scoring

| Scoring input | Taken from |
|---|---|
| Alert expected answers | Section 2 |
| Key items total (log analysis) | Per-test items in v1.1 Appendix A and B, or the Week 2 slice items once evidence exists |
| Claims, wrong, unsupported | `W2 Claims` tab and runner tabs |
| Critical error flag | NC violation, scenario indicator attached to evidence, invented IOC, or the decoy called an attack |

---

## 8. Files in this Week 2 package

| File | Purpose |
|---|---|
| `W2_mina_answer-key_v2.0.md` | This file |
| `W2_mina_alert-triage-summaries_v1.md` | Validated triage per alert, AI draft versus validated |
| `W2_mina_hallucination-log_v2.0.xlsx` | Team log (Week 1 tabs unchanged) plus the Week 2 tabs |
| `20261111-w2-triage-as06-decoy.md` | Ready-to-queue request for the decoy (rename the date part if it is queued on another day) |

---

## 9. Sign-off and cross-review request (Nader)

| Check | What to verify | Result |
|---|---|---|
| W1 | Integrity: section 2 carries v1.1 expectations unchanged; additions are only structural | |
| W2 | Section 2 values match v1.1 Appendix B for AS-01 to AS-05, AS-07 and the TC-02 key for AS-08 | |
| W3 | Spot-check three line references in section 2 against the files you hold | |
| W4 | Open item 4 (mshta to 203.0.113.88 against the scenario) is a real conflict | |
| W5 | Section 5 PASS rule is acceptable as a proposal | |

| Sign-off | Name | Date (UTC) | Status |
|---|---|---|---|
| Author | Mina | | `[to sign]` |
| Cross-reviewer | Nader | | `[pending]` |
