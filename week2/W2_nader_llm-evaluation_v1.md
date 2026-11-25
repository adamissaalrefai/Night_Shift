# Week 2 — Nader Llama Output Evaluation

- **Project:** AI-Assisted SOC Automation & Full Incident Response Capstone
- **Request ID:** `20261125-w2-nader-network-log-analysis`
- **Runner:** Nader
- **Model:** `llama3.1:8b`
- **Review status:** **REVISE — not accepted as a final analyst report**
- **Reviewed:** 2026-11-25
- **Review basis:** Nader's uploaded Llama output, uploaded `run-metadata.txt`, and the three source evidence notes at branch `week1/mahmoud-benchmark`.

> Preserve the original model output unchanged. This document is a separate human evaluation; it must not be substituted for, or presented as, the raw Llama response.

---

## 1. Execution Record

| Field | Recorded value | Review |
|---|---|---|
| Model tag | `llama3.1:8b` | Recorded |
| Ollama version | `0.40.18` | Run completed, but team-wide version approval was not verified |
| Quantization | `Q4_K_M` | Metadata records this, but `ollama-model-info.txt` was not supplied, so the reviewer cannot independently verify it |
| Context | `8192` | Recorded |
| Temperature | `0.2` | Recorded |
| Seed | `42` | Recorded |
| Custom system prompt | None | Recorded |
| Started UTC | `2026-11-25T17:46:40.8005242Z` | Recorded |
| Completed UTC | `2026-11-25T17:59:49.3658028Z` | Recorded |
| Measured duration | `788.45` seconds (about 13m 8s) | Recorded |
| Prompt tokens | `3,608` | Recorded |
| Output tokens | `2,051` | Recorded |
| Output file | `20261125-w2-nader-network-log-analysis.md` | Received and reviewed |

### Input SHA-256 values recorded by the runner

| Input | SHA-256 |
|---|---|
| `W2_nader_firewall-proxy-exfil_2026-11-06T0900Z.log` | `7bab2e24edc46212e2658f4c5b0831e5b23fadaae3f8f876babf01ea6bf930f7` |
| `W2_nader_suricata-ids-alerts_2026-11-06T0900Z.json` | `d78c2481b4b2a1f381ec1df55876e801dedab76d98461feeff402e3276ed6808` |
| `W2_nader_zeek-conn-flow_2026-11-06T0900Z.log` | `88b6e5565e7ef7fc152cc43610907e74d1626add11f02c602390ca3fe865dd7d` |

The three values match the source-file hash manifest used to prepare Nader's run sheet.

---

## 2. Overall Evaluation

| Evaluation criterion | Result | Notes |
|---|---|---|
| Local model execution completed | Pass | Ollama returned a non-empty response |
| Required model tag used | Pass | `llama3.1:8b` |
| Requested generation settings recorded | Pass | Context, temperature, and seed match the run sheet |
| Team-wide Ollama version standardized | Not verified | Runner proceeded with `0.40.18`; record this as an exploratory run |
| Required output formats A, E, and F present | Pass | All three sections are present |
| Evidence references | Partial | Several references are valid, but some conclusions overstate what those records establish |
| Timeline completeness and correlation | Fail | Important network events are omitted and the SSH alerts are incorrectly joined |
| IOC extraction accuracy | Fail | Internal asset IPs are labelled as IOCs; observed artifacts are omitted; SNI last-seen time is wrong |
| Scenario alignment | Partial | The date mismatch is identified, but several scenario-only indicators are omitted |
| ATT&CK mapping accuracy | Fail | Several mappings do not match the described behavior |
| Detection logic quality | Fail | Proposed queries are not executable as written; key behaviors are omitted |
| False-positive analysis and validation | Fail | False positives are poorly described and the 100% success target is unsupported |

**Overall decision: REVISE.** The run itself succeeded. The generated analysis is not sufficiently evidence-grounded to be accepted as a final SOC report.

---

## 3. Material Unsupported Claims and Errors

### 3.1 Compromise is stated as confirmed

**Model claim:** “A malicious actor has compromised WS-FIN-01.”

**Source evidence:** Firewall `FW-01` and `FW-02` plus Suricata `IDS-01` and `IDS-02` record suspicious downloads. `IDS-03` records internal scanning. Zeek `NET-08` and `NET-09` record WinRM and SMB/NTLM activity.

**Assessment:** These events strongly support suspicious activity and possible compromise, but the supplied network notes alone do not prove malware execution or conclusively establish host compromise.

**Required correction:** Use “suspected compromise” or “WS-FIN-01 exhibits multiple indicators consistent with compromise” until endpoint execution/forensic evidence confirms it.

### 3.2 Data exfiltration is stated as confirmed

**Model claim:** Data was exfiltrated from NileTech Financial Services.

**Source evidence:** `FW-04` records an outbound HTTPS POST to `185.220.101.45:8443`, with `54,829,100` bytes outbound and the filename `fin_records_exfil.7z`. `IDS-06` raises a Critical large-outbound-POST alert.

**Assessment:** A large outbound transfer is directly recorded. The archive contents were not inspected, so the actual data contents and customer-data exposure are not established by these files.

**Required correction:** State “large outbound transfer; suspected exfiltration” and explicitly say the archive contents are unknown.

### 3.3 Malware deployment/execution is not demonstrated

**Model claim:** Malware was deployed on WS-FIN-01.

**Source evidence:** `FW-02` and `IDS-02` record a suspicious PE binary download named `svchost_update.exe`. No process-creation or execution event is present in Nader's three network files.

**Assessment:** Download is supported; execution or deployment is not.

**Required correction:** Use “suspicious executable download” rather than “malware deployed.”

### 3.4 C2 beaconing confidence is overstated

**Model claim:** A C2 beaconing connection was established.

**Source evidence:** `FW-03` records TLS to `185.220.101.45:443`, SNI `cdn-update-check.net`, and an `Interval: 60s` note, categorized as `C2-Beaconing`.

**Assessment:** The source labels the behavior as beaconing and reports a 60-second interval. The supplied note does not include the individual repeated flow records needed to independently validate periodicity.

**Required correction:** Call it “suspected periodic TLS/C2 beaconing, reported by FW-03”; request the underlying connection series for validation.

### 3.5 The two SSH alerts are incorrectly treated as one attack chain

**Model claim:** The SSH brute-force attempt on `SRV-WEB-01` succeeded.

**Source evidence:**
- `IDS-04` at `08:44:10Z`: source `10.10.10.15` (`WS-FIN-01`) to destination `10.10.20.10` (`SRV-WEB-01`), 15 failed logins in 10 seconds, target user `dev_admin`.
- `IDS-05` at `08:44:25Z`: source `10.10.20.5` (`DC-NILE-01`) to destination `10.10.20.10` (`SRV-WEB-01`), successful SSH login alert for `dev_admin`.

**Assessment:** The source IPs differ. The second alert must not be presented as proof that the first source successfully authenticated.

**Required correction:** Report two separate alerts. State that `IDS-05` reports a successful SSH login from `DC-NILE-01` to `SRV-WEB-01`; the relationship to `IDS-04` is unverified.

### 3.6 Important network activity is omitted

The model's timeline and findings omit these supplied records:

- `NET-02`–`NET-07`: internal probes from `WS-FIN-01` to `DC-NILE-01` and `SRV-WEB-01`, including ports 22, 88, 445, 3389, and 5985.
- `FW-03` at `08:38:00Z`: TLS/SNI event and reported 60-second interval.
- `NET-08` at `08:40:50Z`: WinRM/HTTP connection from `WS-FIN-01` to `DC-NILE-01:5985`.
- `NET-09` at `08:43:12Z`: SMB/NTLM connection from `WS-FIN-01` to `DC-NILE-01:445`.

These omissions materially weaken the lateral-movement and network timeline analysis.

### 3.7 Timeline duration is misstated

**Model claim:** The evidence files cover a two-hour window.

**Source evidence:** Nader's network records span `2026-11-06T08:31:05Z` (`NET-01`) through `2026-11-06T08:48:12Z` (`FW-04`/`IDS-06`), approximately 17 minutes and 7 seconds.

**Required correction:** State the actual observed interval. Do not claim a two-hour evidence window.

---

## 4. IOC Extraction Review

### Correctly observed

| Type | Value | Evidence | Correction / note |
|---|---|---|---|
| External IPv4 | `185.220.101.45` | `FW-01`–`FW-04`, `IDS-01`, `IDS-02`, `IDS-06`, `NET-01` | Observed external destination; context should be described from the specific records |
| TLS SNI | `cdn-update-check.net` | `FW-03` | Observed as SNI at `08:38:00Z`; this is not proof of DNS resolution |
| URL | `http://185.220.101.45/stage1.ps1` | `FW-01`, `IDS-01` | Omitted by the model |
| URL | `http://185.220.101.45/svchost_update.exe` | `FW-02`, `IDS-02` | Omitted by the model |
| URL | `https://185.220.101.45:8443/upload` | `FW-04`, `IDS-06` | Omitted by the model |
| File artifact | `stage1.ps1` | `FW-01`, `IDS-01` | Omitted by the model |
| File artifact | `svchost_update.exe` | `FW-02`, `IDS-02` | Omitted by the model |
| File artifact | `fin_records_exfil.7z` | `FW-04` | Omitted by the model; filename only, contents unknown |

### Hash caveat

`IDS-02` contains:

`e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855`

This is the SHA-256 value for empty input. Treat it as a placeholder or invalid sample hash, not as a validated malware hash.

### Internal asset addresses misclassified as threat IOCs

The model labels these as IOCs and assigns roles such as “Compromised Host” or “SSH Server”:

- `10.10.10.15` — `WS-FIN-01`
- `10.10.20.5` — `DC-NILE-01`
- `10.10.20.10` — `SRV-WEB-01`

These are valid observed asset addresses and should be retained in an **Affected Assets / Internal Observables** table. They should not automatically be classified as malicious IOCs. In particular, `10.10.20.5` is the source of `IDS-05`, not the destination SSH server.

### Incorrect SNI last-seen time

The model reports `cdn-update-check.net` last seen at `08:48:12Z`. The supplied evidence records it only in `FW-03` at `08:38:00Z`. `FW-04` at `08:48:12Z` is an HTTPS POST to `185.220.101.45:8443`; it does not include the SNI field.

### Scenario-context-only indicators incorrectly omitted

The model says there are no scenario-context-only IOCs. The following scenario indicators are not present in Nader's three network evidence notes:

- `198.51.100.45`
- `mail.niletech-support.com`
- `203.0.113.88`
- `Invoice_Q4_Review.hta`
- `updater.ps1`
- `/tmp/.db_dump.tar.gz`

The domain `cdn-update-check.net` is present as TLS SNI in `FW-03`, but the observed destination is `185.220.101.45`. The scenario's association of that domain with `203.0.113.88` is therefore unresolved by the supplied evidence. Do not infer DNS resolution without DNS records.

---

## 5. ATT&CK Mapping Review

The mappings in the Llama output do not consistently match the behavior described:

| Model finding | Model mapping | Review | More appropriate candidate (subject to team validation) |
|---|---|---|---|
| Host compromise via downloaded payload | `T1190` | Not supported by these records; no public-facing application exploit is shown | `T1105` Ingress Tool Transfer for the download behavior |
| Data exfiltration | `T1020` | Automation is not established; exfiltration content is not confirmed | Consider `T1041` only if exfiltration over the C2 channel is validated |
| Malware deployment | `T1204` | User execution is not shown | `T1105` for transfer; do not map execution without execution evidence |
| C2 beaconing | `T1210` | Does not describe beaconing | `T1071.001` may fit web-protocol C2 if validated |
| SSH brute-force attempt | `T1210` | Does not describe password guessing | `T1110.001` Password Guessing |
| Successful SSH access | `T1210` | Does not describe the reported SSH remote-access method | `T1021.004` SSH may fit the access behavior, while keeping the source discrepancy explicit |

Additional candidate mappings for omitted evidence include `T1046` Network Service Discovery for the internal scan, `T1021.006` Windows Remote Management for WinRM, and `T1021.002` SMB/Windows Admin Shares for SMB activity. These are analyst suggestions, not mappings stated in the evidence files.

---

## 6. Detection Improvement Review

The model's detection section is too generic to implement or validate as written.

### Main problems

1. It focuses only on C2 and SSH; it omits suspicious downloads, the large outbound POST, internal scanning, and WinRM/SMB activity.
2. “Detect and prevent” mixes detection with response. A detection rule should alert; blocking or containment belongs in a separately approved response action.
3. The two candidate queries are not valid, executable queries for a named SIEM. They use undefined syntax and do not specify a query language or schema.
4. The false-positive examples (“legitimate C2 beaconing” and “SSH brute force attempts”) are not useful tuning categories.
5. A target of 100% detection is unsupported without a labelled test set, event counts, baseline, and false-positive criteria.

### Better evidence-based detection proposals

These are proposals only; they have not been deployed or tested.

| Detection | Evidence basis | Suggested logic to validate |
|---|---|---|
| Suspicious external script/executable download | `FW-01`, `FW-02`, `IDS-01`, `IDS-02` | Egress HTTP GET for `.ps1` or `.exe` from an unapproved external destination; correlate user agent and endpoint process telemetry |
| Periodic TLS beacon candidate | `FW-03` | Group repeated TLS flows by source, destination, port, and SNI; evaluate near-regular inter-arrival intervals. Obtain raw flow records first |
| Large outbound POST | `FW-04`, `IDS-06` | Alert on unusually large egress POSTs to external destinations, especially non-standard ports; tune against normal business-transfer baselines |
| Internal multi-port scan | `IDS-03`, `NET-02`–`NET-07` | Detect one internal source contacting multiple destination ports/hosts in a short window; allowlist approved scanners |
| WinRM/SMB remote administration | `NET-08`, `NET-09` | Correlate workstation-to-DC TCP 5985/445 connections with Windows logon and privilege events |
| SSH password guessing | `IDS-04` | Detect repeated failed SSH authentication attempts by source, destination, and account; validate with raw SSH authentication logs |

---

## 7. Corrected Evidence-Based Summary

The supplied network evidence notes record suspicious HTTP downloads from `WS-FIN-01` (`10.10.10.15`) to `185.220.101.45`, a TLS connection with SNI `cdn-update-check.net` and a reported 60-second interval, internal multi-port scanning, WinRM and SMB/NTLM connections to `DC-NILE-01`, SSH authentication alerts, and a large outbound HTTPS POST to `185.220.101.45:8443`.

The large outbound transfer is directly recorded, but the archive contents are unknown. The SSH failed-attempt alert (`IDS-04`) and successful-login alert (`IDS-05`) have different source IPs and must not be joined into one successful brute-force sequence. The evidence is dated 2026-11-06, while the unified scenario window is 2026-11-10; this discrepancy remains unresolved. The supplied Nader files are evidence notes rather than native raw Zeek/Suricata logs, and no DNS query log or PCAP is provided.

---

## 8. Required Corrections Before Acceptance

- [ ] Downgrade confirmed host compromise to suspected compromise unless corroborating endpoint evidence is provided.
- [ ] Downgrade confirmed data exfiltration to suspected exfiltration / large outbound transfer.
- [ ] Replace “malware deployed” with “suspicious executable downloaded” unless execution evidence exists.
- [ ] Keep `IDS-04` and `IDS-05` as separate SSH alerts because their source IPs differ.
- [ ] Add `FW-03` and `NET-02`–`NET-09` to the timeline and findings where relevant.
- [ ] Correct the evidence duration to approximately 17 minutes and 7 seconds.
- [ ] Correct the SNI last-seen timestamp to `08:38:00Z`.
- [ ] Separate external IOCs from internal asset addresses.
- [ ] Add the three observed URLs and the three observed file names.
- [ ] Include the empty-input SHA-256 caveat for the hash in `IDS-02`.
- [ ] List scenario-only indicators that are absent from the network evidence.
- [ ] Correct or remove unsupported ATT&CK mappings.
- [ ] Rewrite detection proposals as testable logic with defined fields, time windows, tuning, and a named query language if executable queries are required.
- [ ] Replace the unsupported 100% success criterion with measurable precision/recall or true-positive/false-positive criteria on a labelled test set.
- [ ] Obtain `ollama-model-info.txt` or other evidence confirming Q4_K_M if the project requires independent quantization verification.
- [ ] Record the run as exploratory until the team confirms whether Ollama `0.40.18` is acceptable for the shared benchmark.

## 9. Evaluation Decision

**Decision: REVISE — do not submit the raw Llama response as a validated SOC analysis.**

The execution is successful and useful for the project: the model produced a response using the recorded local settings. However, the output demonstrates material overconfidence, incomplete event correlation, IOC extraction errors, incorrect ATT&CK mappings, and weak detection proposals. Preserve the raw output for model evaluation, then prepare a separate corrected analyst report and complete the team review process.

- **Human reviewer:** Pending
- **Reviewer date:** Pending
- **Adam cross-review:** Pending
- **Nada second validation:** Pending


---

## 10. Preliminary Scoring and Formal Review Status

These are preliminary scores for the model output, not a team-approved benchmark score.

| Dimension | Score (1–5) | Rationale |
|---|---:|---|
| Accuracy | 2 | The model identifies some core network events, but overstates compromise, exfiltration, malware deployment, and C2; it also incorrectly joins the SSH alerts and uses weak ATT&CK mappings. |
| Completeness | 2 | The output omits several network events, URLs, file artifacts, and scenario-context-only indicators; its timeline coverage is incomplete. |
| Usefulness | 2 | The response provides a starting summary, but its detection proposals and unsupported conclusions require substantial analyst correction before operational use. |

### Evaluation Record Summary

- **Major unsupported / materially overstated incident claims:** 5.
- **Additional documented defects:** timeline omissions and duration error, IOC classification/coverage errors, incorrect SNI last-seen time, ATT&CK mapping errors, and untestable detection logic.
- **Scenario treated as evidence incorrectly:** No direct scenario-only indicator was presented as observed; however, the model incorrectly states that no scenario-context-only IOCs were missing.
- **Preliminary decision:** Revise.
- **Formal human reviewer:** Pending.
- **Adam cross-review:** Pending.
- **Nada second validation:** Pending.

This preliminary evaluation is not a substitute for the team's human cross-review and second validation.
