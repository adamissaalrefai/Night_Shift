# Week 2 — Nader Local LLM Run Sheet (Draft)

- **Project:** AI-Assisted SOC Automation & Full Incident Response
  Capstone
- **Owner / runner:** Nader — Network Operator
- **Version:** v1.0
- **Prepared:** 2026-11-25
- **Request ID (proposed):** `20261125-w2-nader-network-log-analysis`
- **Status:** Draft for local execution; not yet a Nada-issued queue
  item and not executed

> This run sheet is prepared for Nader's assigned network-analysis work.
> No request assigned to Nader by Nada was found in the repository
> snapshots reviewed. Do not represent this draft as a request authored
> or queued by Nada.

## 1. Execution Assignment

| Field                    | Value                                                                                                                                                           |
|--------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Requester / draft author | Nader                                                                                                                                                           |
| Assigned runner          | Nader                                                                                                                                                           |
| Backup runner            | Adam                                                                                                                                                            |
| Week                     | Week 2                                                                                                                                                          |
| Task type                | `log_analysis` + `ioc_extraction` + `detection_improvement`                                                                                                     |
| Purpose                  | Analyze the supplied Nader network evidence notes, extract observed IOC context, identify scenario conflicts, and propose evidence-based detection improvements |
| Primary model            | Llama 3.1 8B Instruct                                                                                                                                           |
| Quantization             | Q4_K_M                                                                                                                                                          |
| Model tag                | `llama3.1:8b`                                                                                                                                                   |
| Context size             | 8192                                                                                                                                                            |
| Temperature              | 0.2                                                                                                                                                             |
| Seed                     | 42                                                                                                                                                              |
| Modelfile SYSTEM line    | None                                                                                                                                                            |
| Ollama version           | **UNCONFIRMED — team-wide version must be agreed before execution**                                                                                             |
| Expected output filename | `20261125-w2-nader-network-log-analysis.md`                                                                                                                     |
| Cross-reviewer           | Adam                                                                                                                                                            |
| Second validator         | Nada (pending)                                                                                                                                                  |

**Ollama version note:** Nader's offline privacy test notes record
`0.40.13`, while Mahmoud's Week 2 outputs record `0.40.11`. The project
requires one agreed version for all runners. Do not choose one by
assumption.

## 2. Evidence Manifest

All three inputs are on branch `week1/mahmoud-benchmark` at commit
`7b34d77da5dd4713a7a81dc7f02aae4fa61707eb`.

| Source file                                                   | Owner | Source type                                                                    | Time range UTC                                | SHA-256 of retrieved UTF-8 content                                 |
|---------------------------------------------------------------|-------|--------------------------------------------------------------------------------|-----------------------------------------------|--------------------------------------------------------------------|
| `evidence/W2_nader_firewall-proxy-exfil_2026-11-06T0900Z.log` | Nader | Human-readable firewall/TLS evidence note (4 records; not raw export)          | `2026-11-06T08:31:06Z`–`2026-11-06T08:48:12Z` | `7bab2e24edc46212e2658f4c5b0831e5b23fadaae3f8f876babf01ea6bf930f7` |
| `evidence/W2_nader_suricata-ids-alerts_2026-11-06T0900Z.json` | Nader | Human-readable Suricata IDS evidence note (6 records; not JSON/EVE)            | `2026-11-06T08:31:06Z`–`2026-11-06T08:48:12Z` | `d78c2481b4b2a1f381ec1df55876e801dedab76d98461feeff402e3276ed6808` |
| `evidence/W2_nader_zeek-conn-flow_2026-11-06T0900Z.log`       | Nader | Normalized Zeek connection-flow evidence note (9 records; not native Zeek TSV) | `2026-11-06T08:31:05Z`–`2026-11-06T08:43:12Z` | `88b6e5565e7ef7fc152cc43610907e74d1626add11f02c602390ca3fe865dd7d` |

**Input handling:** use these exact source versions. Do not modify or
overwrite them. The model must be told that these are evidence notes,
not native raw Zeek/Suricata exports.

## 3. Known Limitations to Preserve

1.  The unified scenario window is 2026-11-10 08:00–16:00 UTC; the
    supplied Nader network records are dated 2026-11-06. Do not shift
    timestamps.
2.  The observed external destination is `185.220.101.45`, while the
    scenario lists `198.51.100.45` and `203.0.113.88`. Do not substitute
    these IPs.
3.  `cdn-update-check.net` appears as TLS SNI with destination
    `185.220.101.45`; no DNS records are supplied to verify name
    resolution.
4.  No `dns.log`, `http.log`, native Zeek `conn.log`, raw Suricata
    `eve.json`, or PCAP is supplied.
5.  The Suricata file has a `.json` extension but contains
    human-readable note lines, not JSON objects.
6.  Firewall note `FW-03` reports a 60-second interval, but the
    underlying repeated flow records are not supplied.
7.  The two SSH alerts have different source IPs: `IDS-04` is from
    `10.10.10.15`; `IDS-05` is from `10.10.20.5`. Do not describe them
    as one successful brute-force sequence.
8.  The SHA-256 value in `IDS-02` is the empty-input hash placeholder,
    not a verified malware hash.
9.  No DNS exfiltration or file-content inspection is evidenced.
10. Do not claim containment, blocking, isolation, password resets, or
    recovery actions were performed.

## 4. Prompt for Local Llama

Paste the following prompt into the approved local Llama model after the
team confirms the Ollama version. The project kickoff specifies no
`SYSTEM` line in the Modelfile; the request prompt is the source of
instructions.

``` text
You are a senior SOC and Incident Response analyst supporting a controlled academic lab simulation.

Analyze only the three authorized Nader network evidence files listed in the Evidence Manifest. The files are human-readable evidence notes, not native raw Zeek or Suricata exports. Do not pretend they contain fields or records that are not present.

PROJECT CONTEXT — BACKGROUND ONLY, NOT INCIDENT EVIDENCE
Project: AI-Assisted SOC Automation & Full Incident Response Capstone
Organization: NileTech Financial Services (niletech.local)
Unified scenario window: 2026-11-10T08:00:00Z to 2026-11-10T16:00:00Z
Time standard: UTC / ISO 8601.

Known assets:
- WS-FIN-01 / 10.10.10.15
- DC-NILE-01 / 10.10.20.5
- SRV-WEB-01 / 10.10.20.10
- FW-NILE-GW / 10.10.10.1

Scenario-context indicators (not automatically observed):
- 198.51.100.45 / mail.niletech-support.com
- 203.0.113.88 / cdn-update-check.net
- Invoice_Q4_Review.hta
- e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855
- /tmp/.db_dump.tar.gz

CRITICAL EVIDENCE RULES
1. Scenario context is not evidence. Only mark an indicator observed if it appears in the supplied evidence.
2. Label findings as Confirmed, Suspected, Scenario context only, or Unknown.
3. Do not invent events, timestamps, hosts, users, IPs, domains, hashes, severity, impact, actions, approvals, or test results.
4. Every material finding must cite the exact source filename and record ID (FW-xx, IDS-xx, or NET-xx).
5. Preserve timestamps exactly. Do not change 2026-11-06 timestamps to fit the 2026-11-10 scenario window.
6. Do not claim DNS activity, DNS exfiltration, or DNS resolution. No DNS query log is supplied.
7. Do not claim the scenario IPs 198.51.100.45 or 203.0.113.88 were observed unless they appear in the supplied files.
8. Treat cdn-update-check.net as an observed TLS SNI only; do not infer DNS resolution.
9. The 60-second interval is reported in one firewall summary record. State that the underlying repeated connection series is unavailable.
10. The Suricata file is a text evidence note despite its .json extension; do not describe it as raw eve.json.
11. The hash in IDS-02 is the SHA-256 of empty input and is a placeholder, not a validated malware hash.
12. Do not join IDS-04 and IDS-05 into one failed-then-successful SSH chain: the source IPs differ.
13. Do not claim that DC-NILE-01 or the domain is compromised solely because a privileged logon is recorded.
14. Do not claim customer financial records were exposed; the archive contents are unknown.
15. Recommend actions only. Never claim that an action was performed unless evidence says so.
16. Keep credentials and secrets redacted. Do not ask for, repeat, or infer any password.
17. Assign confidence: High (directly supported), Medium (supported inference), Low (incomplete or ambiguous).
18. State all missing evidence and contradictions explicitly.

REQUEST METADATA
Request ID: 20261125-w2-nader-network-log-analysis
Task type: log_analysis + ioc_extraction + detection_improvement
Assigned runner: Nader
Model: Llama 3.1 8B Instruct Q4_K_M
Context: 8192
Temperature: 0.2
Seed: 42
Modelfile SYSTEM: None

AUTHORIZED INPUT FILES
- evidence/W2_nader_firewall-proxy-exfil_2026-11-06T0900Z.log
- evidence/W2_nader_suricata-ids-alerts_2026-11-06T0900Z.json
- evidence/W2_nader_zeek-conn-flow_2026-11-06T0900Z.log

SELECTED OUTPUT FORMAT A — LOG ANALYSIS
1. Executive finding — maximum three sentences.
2. Event timeline:
   timestamp UTC | source | host/user | observed event | evidence reference | confidence
3. Findings:
   finding ID | classification | description | severity | ATT&CK mapping | evidence reference | confidence
4. Observed IOCs.
5. Scenario alignment, contradictions, and unknowns.
6. Data gaps.
7. Recommended analyst actions, prioritized P1 to P3.

SELECTED OUTPUT FORMAT E — IOC EXTRACTION
1. Deduplicated IOC table:
   type | value | first seen UTC | last seen UTC | source/evidence reference | confidence | status | context
2. Newly observed IOCs.
3. Scenario-context-only IOCs not found in the supplied evidence.
4. Validation/enrichment priorities.
5. Extraction limitations.

SELECTED OUTPUT FORMAT F — DETECTION IMPROVEMENT
1. Detection objective and observed behavior.
2. Evidence-supported detection logic proposal.
3. Required log sources and fields.
4. Candidate query/pseudocode, clearly labeled as proposed and not deployed.
5. Expected true-positive indicators.
6. Likely false positives and tuning recommendations.
7. Validation procedure and success criteria.
8. Limitations and missing telemetry.

Return only formats A, E, and F. Use concise, evidence-based language. Do not fill missing data with assumptions.
```

## 5. Output and Evaluation Record

The output filename must match the request ID exactly:

`20261125-w2-nader-network-log-analysis.md`

Do not create or label an AI output until the local Llama run has
actually completed. Append and complete the following record after the
run:

``` markdown
## Evaluation Record

- Request ID: 20261125-w2-nader-network-log-analysis
- Model/version: [record exact model tag]
- Ollama version: [team-approved version]
- Context size: 8192
- Temperature: 0.2
- Seed: 42
- Modelfile SYSTEM: None
- Input files/hashes: [copy the three SHA-256 values above]
- Output generated at: [UTC ISO 8601]
- Execution duration: [measured value]

| Evaluation criterion | Result | Notes |
|---|---|---|
| Expected output format followed | Pass/Fail | |
| Required findings included | Pass/Fail | |
| Evidence references accurate | Pass/Fail | |
| Unsupported claims | Count | |
| Scenario treated as evidence incorrectly | Yes/No | |
| IOC extraction accuracy | Value / N/A | |
| Human reviewer decision | Accept/Revise | |
| Reviewer and review date | Name / UTC date | |
```

## 6. Queue and Review Status

- No Nader-assigned Week 2 request from Nada was found in the repository
  branches inspected. This run sheet is not a substitute for a
  Nada-authored queue item.
- Adam is the assigned cross-reviewer for Nader under the team review
  circle.
- Nada is the second validator for the network results under the Week 2
  validation checklist.
- Do not mark either review complete until the named reviewer has
  checked the actual local Llama output against the source evidence.
