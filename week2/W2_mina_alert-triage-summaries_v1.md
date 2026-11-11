# Week 2: Alert Triage Summaries

- **Owner:** Mina (Validation and Evidence Lead). Draft prepared 2026-11-11; Mina confirms by signing the table at the end.
- **Status:** DRAFT. The validated triage is written from the raw alert evidence and Answer Key v2.0. Statements attributed to Llama come only from what the v1.1 hallucination log and scoring workbook already record.
- **Model:** Llama 3.1 8B Instruct Q4_K_M. Mistral and Phi-3 results are not used (Week 1 only).
- **Labels:** text marked **AI draft** was written by the model and is not validated. Text marked **Validated** is the human triage decision. Per the privacy rules, AI-written text is labeled AI-drafted, human-validated only after the validator has checked it against the raw line.

## 1. Read this first

1. **No Week 2 AI outputs were available.** The two branch archives arrived as 0-byte files, so the "AI draft" rows below are Week 1 Llama runs (TC-02 and Adam's three triage trials) that v1.1 already logged. They are a baseline, not Week 2 runs.
2. **"No error logged" does not mean "correct".** Adam's Llama triage runs were reviewed only for the errors that matter to TR-04 (v1.1 intake review IR-1). Alerts without a logged error are marked *not recorded*.
3. Line references are the ones in Answer Key v1.1 and were not re-checked against the files for this draft.

## 2. Result at a glance

| Alert | Validated disposition | Severity | Escalate | Llama draft (as logged) | Match | Review status |
|---|---|---|---|---|---|---|
| AS-01 SIEM-2041 | Benign TP or FP | Low | No | not recorded | unknown | Not reviewed |
| AS-02 SIEM-2042 | True Positive | Medium | Yes (monitor, block) | not recorded | unknown | Not reviewed |
| AS-03 SIEM-2043 | True Positive (recon) | Low or Medium | Block source | Benign TP, "likely a legitimate scan; no action" | **No** | Partial |
| AS-04 SIEM-2044 | TP or Needs Investigation | Medium or High | Yes | not recorded | unknown | Not reviewed |
| AS-05 SIEM-2045 | True Positive | High or Critical | Yes | not recorded | unknown | Not reviewed |
| AS-06 SIEM-3107 (decoy) | Benign TP or FP | Informational or Low | **No** | True Positive, High, isolate the DC, disable the task | **No (Critical)** | Disposition reviewed |
| AS-07 SIEM-4120 | True Positive | High | Yes | disposition not recorded; ATT&CK error logged | unknown | Partial |
| AS-08 Suricata HTA alert | True Positive | High | Yes | True Positive, severity correct; containment weak | **Disposition yes; escalation no** | Complete (scored 2/2/2, FAIL) |

**Disposition accuracy that can be stated today:** 1 confirmed right (AS-08), 2 confirmed wrong (AS-03, AS-06), 5 unknown. A percentage over 3 verdicts would mislead, so none is reported.

## 3. Per-alert summaries

### AS-01: SIEM-2041 (benign control)
- **Validated:** Benign True Positive or False Positive; Low; no escalation. Basis: one failed logon followed by one success on the same console within 6 seconds (a typo, then the right password).
- **Llama draft:** not recorded.
- **Gap:** Phi-3 escalated this alert as a True Positive (Week 1, not used as evidence). Whether Llama did the same is unknown until its output is reviewed.

### AS-02: SIEM-2042 (SSH brute force)
- **Validated:** True Positive; Medium; escalate for monitoring and a block of `203.0.113.88`. Basis: 6 failed passwords in 27 seconds, 3 connections, users root, admin (invalid) and dev_admin, no success from that IP (T-01 L8-L24, 10:21:05 to 10:21:32).
- **Must not say:** that the brute force succeeded (NC-03). The Accepted `dev_admin` lines come from an internal source.
- **ATT&CK:** T1110.001.
- **Llama draft:** not recorded.

### AS-03: SIEM-2043 (web scan)
- **Validated:** True Positive (reconnaissance); Low or Medium; block the source. Basis: external Nikto scan, 11 requests in 4 seconds, same source as SIEM-2042. ATT&CK T1595.002.
- **Llama draft (AI, not validated):** "Benign True Positive ... likely a legitimate scan; no action."
- **Difference:** wrong. An unauthorized external scanner with a Nikto user agent is not a legitimate scan. Logged as Overstated conclusion, High (Adam tab, request `20261027-triage-test-01`).
- **Link:** same source as AS-02, so the two alerts should be triaged together.

### AS-04: SIEM-2044 (dev_admin session from WS-FIN-01)
- **Validated:** True Positive or Needs Investigation; Medium or High; escalate. It is the first half of the AS-04 to AS-05 chain.
- **Llama draft:** not recorded.
- **Open point:** the source of this logon differs between datasets (see section 5, timeline conflict).

### AS-05: SIEM-2045 (root shell through sudo)
- **Validated:** True Positive; High or Critical; escalate. Basis: `sudo` list followed by an `awk` shell escape to root, in the same dev_admin session as AS-04. ATT&CK T1548.003.
- **Llama draft:** not recorded.
- **Priority:** first among the five Adam alerts.

### AS-06: SIEM-3107 (decoy)
- **Validated:** Benign True Positive (False Positive also accepted); Informational or Low; **no escalation**; no isolation and no task disablement; confidence High.
- **Basis (all from the alert text):**
  - parent is `svchost.exe -k netsvcs -p -s Schedule`;
  - the task `\NileTech\NightlyBackup` was created by `NILETECH\Administrator`;
  - the decoded command is `& 'C:\Scripts\backup-nightly.ps1'`;
  - the script hash is unchanged since the 2026-10-27 baseline;
  - the same rule fired on the same host, account and command on 14 of the last 14 days;
  - no connections outside `10.10.20.0/24`.
- **Weakening evidence / limit:** the benign judgement rests on enrichment fields. No raw 4688 or 4104 events are supplied, so an analyst would still confirm the task definition on the host.
- **Llama draft (AI, not validated):** True Positive, severity High, "Isolate the host DC-NILE-01 ... Disable the scheduled task".
- **Difference:** wrong disposition and a harmful action on a domain controller. **Critical** (Adam tab, request `20261027-triage-test-02`). Two more errors logged on the same run: ATT&CK "T1059.001: PowerShell (PSExec)" (PSExec is not T1059.001), and "Evidence weakening: None" while the baseline hash and the 14-of-14 history were listed as supporting the alert.
- **Next step:** run `20261111-w2-triage-as06-decoy.md` under Week 2 settings to see whether the result repeats.

### AS-07: SIEM-4120 (failures then success, Administrator)
- **Validated:** True Positive (Needs Investigation accepted); High; escalate. Basis: 4x event 4625 (0xC000006A) from 11:30:41Z to 11:30:52Z, then 4624 at 11:31:04Z, NTLM type 3, account Administrator, SMB 445, from WS-FIN-01 to DC-NILE-01. Recommended (not performed): reset Administrator, isolate WS-FIN-01, collect post-logon activity.
- **ATT&CK:** T1110.001, T1021.002, T1078.002.
- **Must not say:** that DC-NILE-01 is compromised (NC-02). A successful logon is evidence of access, not of compromise.
- **Llama draft:** disposition not recorded. Logged error: ATT&CK "T1136.001 (Brute Force)"; T1136.001 is Create Account: Local Account, and brute force is T1110. High (Adam tab, `20261027-triage-test-03`).

### AS-08: Suricata HTA alert plus mshta.exe connection
- **Validated:** True Positive; High (Critical only with a stated justification); escalate. Basis: `mshta.exe` run by `NILETECH\sara.ahmed` on WS-FIN-01 connects to `203.0.113.88:443` (`cdn-update-check.net`) at 08:16:05.390Z; Suricata signature "ET MALWARE Suspicious HTA Outbound TLS Connection" at 08:16:05.402Z. Recommended (not performed): isolate WS-FIN-01, block the destination, collect process and memory data.
- **Data gaps that must be stated:** no PCAP or payload; TLS content not visible; alert-level data only.
- **ATT&CK:** T1218.005; T1071.001 or T1573 acceptable.
- **Llama draft (AI, not validated):** disposition and severity correct. Errors logged (request `20261028-w1-alert-triage-llama31`, six rows):
  - affected scope "IOCs: None" although the destination IP and domain are in the alert (Wrong meaning, High);
  - ATT&CK "Not mapped" (Wrong ATT&CK mapping, Medium);
  - "Data gaps: None identified" although there is no PCAP or payload (Overstated, Medium);
  - cited `/evidence/W1_nader_endpoint-telemetry_...json`, a file that does not exist (Bad reference, Medium);
  - containment limited to "investigate / collect logs / review permissions" with escalation "if necessary" (Omission, Medium);
  - the evidence section omits the destination and the signature name (Omission, Medium).
- **Score:** accuracy 2, completeness 2, usefulness 2; FAIL (3 wrong, 1 unsupported claim; 2 of 7 key items).

## 4. Correlation and priority

- AS-02 and AS-03 share the source `203.0.113.88` and are one reconnaissance and credential-guessing episode.
- AS-04 leads to AS-05 (same dev_admin session): treat as one escalating incident, P1.
- **Proposed order (Mina's judgement, for review):** P1 AS-05, AS-07, AS-08, AS-04; P2 AS-02, AS-03; P3 AS-01, AS-06.

## 5. Observations for the team

1. **Benign handling is the weakest point.** On the only benign case Llama has been run on, it called a scheduled backup an attack and advised isolating the domain controller (Critical). This must be tested again under Week 2 settings and with AS-01.
2. **Scenario leakage and weak context handling** appeared in Week 1 (Mistral attached `Invoice_Q4_Review.hta` to an alert that did not contain it). No Llama case is logged for this in triage, but the control (NC-11) stays active.
3. **ATT&CK IDs are unreliable.** Three of the eleven logged Llama triage errors are wrong ATT&CK mappings (T1136.001, a PSExec/T1059.001 mix-up, and a refusal to map supported behavior). The prompt-library suggestion PL-4 (give an ID only with its exact technique name) is worth testing.
4. **Evidence references can be invented.** One Llama triage output cited a file that does not exist.
5. **Two dataset conflicts affect triage.** The attacker infrastructure in AS-08 differs from the scenario (mshta to `203.0.113.88` instead of `198.51.100.45`), and the SSH and `tar` timelines differ between Mahmoud's and Adam's files. Both are listed in Answer Key v2.0, section 6.

## 6. Baseline counts (Llama, triage runs only, from v1.1)

4 runs, 11 logged errors: Wrong meaning 3, Wrong ATT&CK mapping 3, Overstated conclusion 2, Omission 2, Bad reference 1. By severity: Critical 1, High 4, Medium 6, Low 0. These are error counts, not rates: the claim totals needed for a hallucination rate were not recorded in Week 1.

## 7. Still to do before this becomes final

| # | Action | Owner |
|---|---|---|
| 1 | Re-upload the two branch archives (the copies received were empty) | Mina |
| 2 | Review the three Adam Llama triage outputs in full, claim by claim | Mina |
| 3 | Queue and validate `20261111-w2-triage-as06-decoy.md` on Llama | Mina, Mahmoud |
| 4 | Add triage requests for AS-01 to AS-05 and AS-07 once their evidence is committed | Mina |
| 5 | Add one more benign alert | Log owners |
| 6 | Second validator on the Linux and network results | Nada |
| 7 | Cross-review of this file and the key | Nader |

## 8. Sign-off

| Item | Name | Date (UTC) | Status |
|---|---|---|---|
| Validated triage confirmed against the raw alerts | Mina | | `[to sign]` |
| Cross-review | Nader | | `[pending]` |
