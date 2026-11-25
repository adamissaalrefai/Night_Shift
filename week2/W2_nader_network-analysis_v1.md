# Week 2 — Network Log Analysis and IOC Context

- **Project:** AI-Assisted SOC Automation & Full Incident Response
  Capstone
- **Owner:** Nader — Network Operator
- **Version:** v1.0
- **Prepared:** 2026-11-25
- **Time standard:** UTC / ISO 8601
- **Status:** Analyst-prepared evidence review; local Llama run and
  cross-review are pending
- **Source evidence commit:** `7b34d77da5dd4713a7a81dc7f02aae4fa61707eb`
  on `week1/mahmoud-benchmark`

> **Status and integrity note:** This is a human evidence review
> prepared from the repository's existing evidence notes. It is **not**
> a recorded Llama 3.1 output, and it must not be placed in the
> AI-Outputs folder or counted in model metrics. The local model
> request/run sheet is provided separately.

------------------------------------------------------------------------

## 1. Executive Finding

The available network evidence notes show HTTP payload downloads from
`WS-FIN-01` to `185.220.101.45`, a firewall-reported periodic TLS
connection using SNI `cdn-update-check.net`, internal TCP port scanning,
WinRM/SMB connections to `DC-NILE-01`, an SSH brute-force alert, and a
large outbound HTTPS POST of `fin_records_exfil.7z` to the same external
IP. Several network events correlate with Mahmoud's Windows PowerShell
and logon evidence, but the dataset contains material date, source-IP,
and destination-IP conflicts with the unified scenario. The supplied
files are evidence summaries rather than native Zeek/Suricata raw logs;
there is no DNS query evidence, PCAP, or payload inspection.

## 2. Scope and Evidence Inventory

### 2.1 Nader network evidence

All three Nader evidence files are present on branch
`week1/mahmoud-benchmark`, at commit
`7b34d77da5dd4713a7a81dc7f02aae4fa61707eb`. The links below are pinned
to that commit so the source version is reproducible.

| Source file                                                                                                                                                                                                    | Content actually present                                                                             |               Records | SHA-256 of retrieved UTF-8 content                                 |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|----------------------:|--------------------------------------------------------------------|
| [W2_nader_firewall-proxy-exfil_2026-11-06T0900Z.log](https://github.com/adamissaalrefai/Night_Shift/blob/7b34d77da5dd4713a7a81dc7f02aae4fa61707eb/evidence/W2_nader_firewall-proxy-exfil_2026-11-06T0900Z.log) | Human-readable firewall/TLS evidence note, not raw firewall export                                   |   4 (`FW-01`–`FW-04`) | `7bab2e24edc46212e2658f4c5b0831e5b23fadaae3f8f876babf01ea6bf930f7` |
| [W2_nader_suricata-ids-alerts_2026-11-06T0900Z.json](https://github.com/adamissaalrefai/Night_Shift/blob/7b34d77da5dd4713a7a81dc7f02aae4fa61707eb/evidence/W2_nader_suricata-ids-alerts_2026-11-06T0900Z.json) | Human-readable IDS evidence note. Despite the `.json` extension, the content is not JSON/EVE records | 6 (`IDS-01`–`IDS-06`) | `d78c2481b4b2a1f381ec1df55876e801dedab76d98461feeff402e3276ed6808` |
| [W2_nader_zeek-conn-flow_2026-11-06T0900Z.log](https://github.com/adamissaalrefai/Night_Shift/blob/7b34d77da5dd4713a7a81dc7f02aae4fa61707eb/evidence/W2_nader_zeek-conn-flow_2026-11-06T0900Z.log)             | Human-readable normalized connection-flow note, not native Zeek TSV `conn.log`                       | 9 (`NET-01`–`NET-09`) | `88b6e5565e7ef7fc152cc43610907e74d1626add11f02c602390ca3fe865dd7d` |

### 2.2 Supplemental cross-source evidence

These Mahmoud-owned files were used only to corroborate timestamps and
activity across sources. They were not modified.

| Source file                                                                                                                                                                                                        | Use in this review                                                                       | SHA-256 of retrieved UTF-8 content                                 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|--------------------------------------------------------------------|
| [W2_mahmoud_windows-powershell_2026-11-06T0900Z.log](https://github.com/adamissaalrefai/Night_Shift/blob/7b34d77da5dd4713a7a81dc7f02aae4fa61707eb/evidence/W2_mahmoud_windows-powershell_2026-11-06T0900Z.log)     | Correlates download, remote PowerShell execution, and outbound POST with network records | `657d5aaae0b9aea7cba36f1b8ac825964d26096bbc17429a877420f721afdb63` |
| [W2_mahmoud_windows-logon-events_2026-11-06T0900Z.log](https://github.com/adamissaalrefai/Night_Shift/blob/7b34d77da5dd4713a7a81dc7f02aae4fa61707eb/evidence/W2_mahmoud_windows-logon-events_2026-11-06T0900Z.log) | Correlates failed and successful network logons and privilege assignment                 | `91705f2e6ec9124578ac974c7e90f40010e3e3851ac9dd28f7c4244b820964d5` |

The Windows process-creation file in the same evidence folder is only a
3-byte UTF-8 BOM and contains no event records. It was not used as
evidence.

### 2.3 Evidence window

The supplied records run from `2026-11-06T08:31:05Z` (first Zeek
connection record) to `2026-11-06T08:50:30Z` (Windows logoff record).
The unified attack scenario specifies `2026-11-10T08:00:00Z` to
`2026-11-10T16:00:00Z`. The four-day difference is unresolved.
Timestamps have not been shifted or rewritten.

## 3. Correlated Event Timeline

| Timestamp (UTC)                        | Source reference                                          | Observed event                                                                                                                                       | Assessment                                                                                        |
|----------------------------------------|-----------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|
| `2026-11-06T08:31:05Z`                 | Zeek note `NET-01`                                        | `WS-FIN-01` (`10.10.10.15`) connects to `185.220.101.45:80` over HTTP; the note records 420 bytes sent and 18,450 received                           | Correlates with the download at 08:31:06Z                                                         |
| `2026-11-06T08:31:06Z`                 | Firewall `FW-01`; Suricata `IDS-01`; PowerShell `LINE-01` | HTTP GET for `/stage1.ps1` from `185.220.101.45`; Suricata labels it PowerShell HTTP DownloadString C2 activity                                      | Strong cross-source correlation                                                                   |
| `2026-11-06T08:33:18Z`                 | Firewall `FW-02`; Suricata `IDS-02`; PowerShell `LINE-02` | HTTP GET for `svchost_update.exe`, saved under `C:\Users\Public\Music\`                                                                              | Strong cross-source correlation; supplied hash is a placeholder (see IOC section)                 |
| `2026-11-06T08:36:10Z`–`08:36:18Z`     | Zeek `NET-02`–`NET-07`; Suricata `IDS-03` at `08:36:15Z`  | TCP probes from `WS-FIN-01` to internal systems on ports 22, 88, 445, 3389, 5985; Suricata reports a rapid internal port scan across `10.10.20.0/24` | Internal service-discovery activity is supported                                                  |
| `2026-11-06T08:38:00Z`                 | Firewall `FW-03`                                          | TLS 1.2 egress from `WS-FIN-01` to `185.220.101.45:443`, SNI `cdn-update-check.net`; note reports a 60-second interval and labels it C2 beaconing    | Periodicity is reported in a summary; individual repeated flows are not supplied                  |
| `2026-11-06T08:40:50Z`                 | Zeek `NET-08`; PowerShell `LINE-03`                       | WinRM/HTTP connection from `WS-FIN-01` to `DC-NILE-01:5985`; PowerShell uses `Invoke-Command` with `NILETECH\admin_backup` credentials               | Remote administration activity is supported; the credential value is intentionally not reproduced |
| `2026-11-06T08:42:05Z` and `08:42:08Z` | Windows logon `LINE-02` and `LINE-03`                     | Two failed network logons for `NILETECH\admin_backup` from `WS-FIN-01` to `DC-NILE-01`                                                               | Failed authentication is recorded                                                                 |
| `2026-11-06T08:43:12Z`                 | Zeek `NET-09`; Windows logon `LINE-04` and `LINE-05`      | SMB/NTLM connection from `WS-FIN-01` to `DC-NILE-01`; Windows note records a successful type-3 NTLM logon and special privileges for `admin_backup`  | Successful privileged network logon is recorded; domain-controller compromise is not established  |
| `2026-11-06T08:44:10Z`                 | Suricata `IDS-04`                                         | Alert reports 15 failed SSH attempts in 10 seconds from `WS-FIN-01` to `SRV-WEB-01:22`, targeting `dev_admin`                                        | Alert-reported brute-force activity; raw SSH authentication records are absent                    |
| `2026-11-06T08:44:25Z`                 | Suricata `IDS-05`                                         | Alert reports a successful SSH login as `dev_admin` to `SRV-WEB-01`, but source IP is `DC-NILE-01` (`10.10.20.5`)                                    | Source differs from `IDS-04`; do not describe this as the same failed-to-success sequence         |
| `2026-11-06T08:48:12Z`                 | Firewall `FW-04`; Suricata `IDS-06`; PowerShell `LINE-04` | HTTPS POST to `185.220.101.45:8443/upload`; file name `fin_records_exfil.7z`; firewall records 54,829,100 bytes outbound                             | Large outbound transfer is supported; file contents are not inspected                             |
| `2026-11-06T08:50:30Z`                 | Windows logon `LINE-06`                                   | Network logoff for `NILETECH\admin_backup` on `DC-NILE-01`                                                                                           | Session logoff is recorded                                                                        |

## 4. Network Findings

| Finding                                                | Classification                                                    | Evidence-based statement                                                                                                                                                                                                          | Severity                                              | ATT&CK mapping                                                                                              | Confidence                                        |
|--------------------------------------------------------|-------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|---------------------------------------------------|
| N-01 — External payload downloads                      | Confirmed activity; malicious purpose strongly suspected          | HTTP GET requests for `stage1.ps1` and `svchost_update.exe` from `185.220.101.45` are recorded by the firewall, Suricata, and the correlated PowerShell events                                                                    | High                                                  | T1105 — Ingress Tool Transfer; T1071.001 — Web Protocols (for web-channel activity)                         | High                                              |
| N-02 — Periodic TLS / possible C2 beaconing            | Suspected                                                         | Firewall note records TLS to `185.220.101.45:443`, SNI `cdn-update-check.net`, a 60-second interval, and category `C2-Beaconing`; the underlying repeated connection series is absent                                             | High if validated; currently Medium confidence        | T1071.001 — Web Protocols                                                                                   | Medium                                            |
| N-03 — Internal TCP port scan                          | Confirmed scan activity                                           | Suricata reports a rapid scan from `10.10.10.15` across `10.10.20.0/24`; Zeek note contains matching probes to ports 22, 88, 445, 3389, and 5985                                                                                  | Medium                                                | T1046 — Network Service Discovery                                                                           | High                                              |
| N-04 — Remote access to the domain controller          | Confirmed network/logon activity; malicious context suspected     | WinRM to `10.10.20.5:5985` and SMB/NTLM to `10.10.20.5:445` are recorded from `10.10.10.15`; Windows notes record successful type-3 NTLM logon and special privileges for `admin_backup`                                          | High                                                  | T1021.006 — Windows Remote Management; T1021.002 — SMB/Windows Admin Shares                                 | High                                              |
| N-05 — SSH password-guessing alert                     | Suspected / alert-reported                                        | Suricata reports 15 failed SSH attempts in 10 seconds from `10.10.10.15` to `10.10.20.10:22`, targeting `dev_admin`; raw SSH authentication logs are not supplied                                                                 | Medium                                                | T1110.001 — Password Guessing                                                                               | Medium                                            |
| N-06 — Successful SSH alert from the domain controller | Confirmed only as an IDS-note entry; relationship to N-05 unknown | Suricata reports successful SSH from `10.10.20.5` to `10.10.20.10` as `dev_admin`; this source differs from the `10.10.10.15` source in `IDS-04`                                                                                  | Medium                                                | T1021.004 — SSH                                                                                             | Medium                                            |
| N-07 — Large outbound HTTPS POST                       | Confirmed outbound transfer; data exfiltration strongly suspected | Firewall records a POST to `185.220.101.45:8443` with `54,829,100` bytes outbound and file name `fin_records_exfil.7z`; Suricata raises a Critical large-outbound-POST alert; PowerShell records the corresponding upload command | Critical alert in source; High confidence in transfer | T1041 — Exfiltration Over C2 Channel (provisional; same external destination is used for staging/beaconing) | High for transfer; Medium for actual data content |

### 4.1 What is not established

- The contents of `fin_records_exfil.7z` were not inspected. The
  filename suggests financial data, but customer-data loss is not
  confirmed.
- The `DC-NILE-01` privileged logon is evidence of access and privilege
  assignment, not proof that the domain controller or domain was fully
  compromised.
- The successful SSH alert at 08:44:25Z is from `DC-NILE-01`, not the
  source in the 08:44:10Z failed-attempt alert. The two alerts must not
  be joined into one successful brute-force chain without raw
  authentication evidence.
- No DNS exfiltration is demonstrated by the supplied network evidence.

## 5. IOC and Artifact Context

### 5.1 Observed in the supplied evidence

| Type             | Value                                                              | First seen (UTC) | Last seen (UTC) | Evidence                                                | Status / confidence                                                                                          |
|------------------|--------------------------------------------------------------------|------------------|-----------------|---------------------------------------------------------|--------------------------------------------------------------------------------------------------------------|
| IPv4             | `185.220.101.45`                                                   | `08:31:05Z`      | `08:48:12Z`     | `NET-01`, `FW-01`–`FW-04`, `IDS-01`, `IDS-02`, `IDS-06` | Observed; High                                                                                               |
| Domain / TLS SNI | `cdn-update-check.net`                                             | `08:38:00Z`      | `08:38:00Z`     | `FW-03`                                                 | Observed as SNI only; no DNS resolution evidence; Medium                                                     |
| URL              | `http://185.220.101.45/stage1.ps1`                                 | `08:31:06Z`      | `08:31:06Z`     | `FW-01`, `IDS-01`, PowerShell `LINE-01`                 | Observed; High                                                                                               |
| URL              | `http://185.220.101.45/svchost_update.exe`                         | `08:33:18Z`      | `08:33:18Z`     | `FW-02`, `IDS-02`, PowerShell `LINE-02`                 | Observed; High                                                                                               |
| URL              | `https://185.220.101.45:8443/upload`                               | `08:48:12Z`      | `08:48:12Z`     | `FW-04`, `IDS-06`, PowerShell `LINE-04`                 | Observed; High                                                                                               |
| File artifact    | `stage1.ps1`                                                       | `08:31:06Z`      | `08:31:06Z`     | `FW-01`, PowerShell `LINE-01`                           | Observed                                                                                                     |
| File artifact    | `svchost_update.exe`                                               | `08:33:18Z`      | `08:33:18Z`     | `FW-02`, `IDS-02`, PowerShell `LINE-02`                 | Observed                                                                                                     |
| File artifact    | `fin_records_exfil.7z`                                             | `08:48:12Z`      | `08:48:12Z`     | `FW-04`, PowerShell `LINE-04`                           | Observed filename; content unknown                                                                           |
| SHA-256 field    | `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` | `08:33:18Z`      | `08:33:18Z`     | `IDS-02`                                                | Appears in evidence, but it is the SHA-256 of empty input and is a placeholder, not a validated malware hash |

Internal addresses and hostnames (`10.10.10.15`, `10.10.20.5`,
`10.10.20.10`, `WS-FIN-01`, `DC-NILE-01`, `SRV-WEB-01`) are affected
assets, not external IOCs.

### 5.2 Scenario-context-only indicators not observed in Nader's supplied network files

| Indicator                   | Scenario role                  | Status in this evidence slice                                    |
|-----------------------------|--------------------------------|------------------------------------------------------------------|
| `198.51.100.45`             | Scenario phishing/C2 host      | Not observed                                                     |
| `mail.niletech-support.com` | Scenario phishing domain       | Not observed                                                     |
| `203.0.113.88`              | Scenario scan/exfil host       | Not observed in Nader's supplied network files                   |
| `Invoice_Q4_Review.hta`     | Scenario phishing attachment   | Not observed                                                     |
| `updater.ps1`               | Scenario secondary payload     | Not observed; the supplied evidence instead records `stage1.ps1` |
| `/tmp/.db_dump.tar.gz`      | Scenario Linux staging archive | Not observed                                                     |

The domain `cdn-update-check.net` is present as TLS SNI in `FW-03`, but
the evidence associates that TLS record with destination IP
`185.220.101.45`. The scenario associates the same domain with
`203.0.113.88`. Without `dns.log` or a packet capture, this mapping
conflict cannot be resolved.

## 6. Scenario Alignment and Unresolved Conflicts

| Issue                    | Scenario / expected value                                          | Supplied evidence                                                            | Safe conclusion                                                                                                        |
|--------------------------|--------------------------------------------------------------------|------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------|
| Date                     | Scenario window: 2026-11-10 08:00–16:00 UTC                        | Nader evidence records are dated 2026-11-06                                  | Four-day date mismatch; preserve timestamps and do not score as the same in-window activity until the team resolves it |
| C2 destination           | `198.51.100.45`                                                    | `185.220.101.45` in firewall, IDS, and Zeek notes                            | Different destination IP; do not substitute one for the other                                                          |
| Domain mapping           | `cdn-update-check.net` associated with `203.0.113.88` in scenario  | `FW-03` records SNI `cdn-update-check.net` with destination `185.220.101.45` | Mapping conflict; DNS evidence is absent                                                                               |
| Exfil source/destination | `SRV-WEB-01` (`10.10.20.10`) to `203.0.113.88`                     | `WS-FIN-01` (`10.10.10.15`) to `185.220.101.45:8443`                         | Different source and destination; the supplied network evidence does not prove the scenario's expected path            |
| SSH brute force          | Scenario describes external source `203.0.113.88`                  | `IDS-04` reports source `10.10.10.15`                                        | Internal source in this alert; do not relabel it as external                                                           |
| SSH success              | Scenario expects successful SSH from `10.10.10.15` to `SRV-WEB-01` | `IDS-05` reports source `10.10.20.5` to `10.10.20.10`                        | Source mismatch; successful session is not shown from the same source as `IDS-04`                                      |
| DNS exfiltration         | Expected in scenario                                               | No `dns.log` or DNS query records supplied                                   | Unknown / not evidenced                                                                                                |

These conflicts are material to the Week 1 answer key's network items
(`LA-N-01` and `LA-N-02`). On the supplied Nader evidence, beacon-like
activity is recorded but to a different IP than `LA-N-01` expects;
`LA-N-02`'s DNS activity and expected source/destination path are not
evidenced.

## 7. Detection Improvement Suggestions

These are **proposed detection ideas only**. They have not been deployed
or tested against raw telemetry.

### D-01 — Suspicious PowerShell HTTP downloads

- **Objective:** Detect workstation egress HTTP GET requests for script
  or executable payloads using PowerShell-like user agents.
- **Candidate logic:**
  `direction = egress AND method = GET AND user_agent contains "PowerShell" AND (URL path ends with ".ps1" OR URL path ends with ".exe") AND destination not in approved software-source allowlist`.
- **Evidence basis:** `FW-01`, `FW-02`, `IDS-01`, `IDS-02`;
  corroborating PowerShell `LINE-01` and `LINE-02`.
- **Tuning:** Allowlist approved software repositories and management
  tools; correlate with endpoint process telemetry before raising
  severity.
- **Limitation:** Native Zeek `http.log` and endpoint process-creation
  data are not available in this evidence pack.

### D-02 — Periodic outbound TLS beacon candidate

- **Objective:** Identify repeated low-volume TLS connections from an
  internal workstation to the same external IP/SNI at near-regular
  intervals.
- **Candidate logic:** Group by
  `source IP + destination IP + destination port + TLS SNI`; alert when
  at least five connections occur within a ten-minute window and
  inter-arrival times cluster around a configured interval (for example,
  50–70 seconds).
- **Evidence basis:** `FW-03` reports a 60-second interval to
  `185.220.101.45:443` with SNI `cdn-update-check.net`.
- **Tuning:** Establish host-specific baselines and exclude approved
  monitoring/health-check traffic.
- **Limitation:** The current file contains a single summary entry, not
  the individual connection series; validate the logic against raw
  `conn.log`/TLS records before deployment.

### D-03 — Large outbound POST on an unapproved port

- **Objective:** Detect unusually large outbound POST transfers to
  external destinations on non-standard or unapproved ports.
- **Candidate logic:**
  `direction = egress AND method = POST AND destination is external AND destination port is not approved AND bytes_out > 10 MB`.
- **Evidence basis:** `FW-04` records 54,829,100 outbound bytes to
  `185.220.101.45:8443`; `IDS-06` raises a Critical large-POST alert.
- **Tuning:** The 10 MB threshold is an initial proposal, not a
  team-approved threshold. Tune using normal business transfer baselines
  and destination allowlists.
- **Limitation:** Payload contents and the actual archive contents are
  not available.

### D-04 — Internal multi-port scan

- **Objective:** Detect one internal source contacting multiple service
  ports or hosts in a short interval.
- **Candidate logic:** Alert when one source contacts at least five
  distinct destination ports within ten seconds, with optional
  subnet-wide fan-out scoring.
- **Evidence basis:** `IDS-03` and Zeek note `NET-02`–`NET-07`.
- **Tuning:** Exclude approved vulnerability scanners and management
  systems by owner and schedule.

### D-05 — WinRM/SMB access followed by privileged logon

- **Objective:** Correlate workstation-to-DC remote administration
  connections with a successful network logon and special privilege
  assignment.
- **Candidate logic:** Correlate egress from a workstation to a domain
  controller on TCP 5985 or 445 with a type-3 successful logon and
  subsequent privileged-logon event for the same account and source.
- **Evidence basis:** Zeek `NET-08` and `NET-09`; Windows logon
  `LINE-04` and `LINE-05`.
- **Tuning:** Maintain an approved admin workstation/account baseline
  and require a human review for exceptions.

## 8. Data Gaps and Evidence Limitations

1.  No native Zeek `conn.log`, `dns.log`, or `http.log` files are
    supplied; only a normalized connection-flow evidence note is
    available.
2.  No raw Suricata `eve.json` is supplied. The file with the `.json`
    extension is a text evidence note with six `IDS-*` entries.
3.  No PCAP or TLS/HTTP payload is available.
4.  No DNS query records are available; DNS tunneling/exfiltration and
    domain-to-IP resolution cannot be assessed.
5.  No raw SSH `auth.log` is available to verify the failed and
    successful SSH alerts.
6.  The Windows process-creation evidence file contains no event
    records.
7.  The `60s` beacon interval is reported in one firewall note;
    individual flows are needed to validate periodicity.
8.  The contents of `fin_records_exfil.7z` are unknown; no customer
    record count or confirmed data loss can be stated.
9.  The evidence date and attacker IP/domain mappings conflict with the
    unified scenario.
10. The Suricata hash value is a known empty-input placeholder, not a
    valid malware sample hash.

## 9. Recommended Analyst Actions

### P1 — Preserve and validate

- Preserve the original evidence notes and record their hashes; do not
  overwrite the source files.
- Obtain native Zeek connection/TLS, DNS, and HTTP logs plus the
  original Suricata EVE JSON and PCAP from the lab storage if they
  become available.
- Preserve the firewall/proxy record for the 54,829,100-byte POST and
  validate the file transfer in the isolated lab.
- Keep `185.220.101.45` separate from the scenario IPs until the team
  records a reconciliation decision.

### P2 — Investigate access paths

- Review `WS-FIN-01` for the downloaded files and correlate the network
  events with endpoint process telemetry.
- Review the `admin_backup` logon and privilege assignment on
  `DC-NILE-01`; do not state that the DC is compromised based only on
  these records.
- Obtain SSH authentication records for `SRV-WEB-01` to verify the two
  Suricata SSH alerts and their different source IPs.

### P3 — Improve detection and documentation

- Validate D-01 to D-05 against raw telemetry and benign administrative
  traffic before deployment.
- Reconcile the 2026-11-06 evidence timestamps with the 2026-11-10
  scenario window.
- Record the observed-vs-scenario-only IOC distinction in the shared IOC
  register.

**No containment, blocking, password reset, isolation, or recovery
action is recorded as completed in the supplied evidence.**

## 10. Week 2 Completion and Review Status

| Requirement                               | Status                                    | Note                                                                                         |
|-------------------------------------------|-------------------------------------------|----------------------------------------------------------------------------------------------|
| Network evidence review                   | Analyst review prepared                   | Based on the three Nader evidence notes                                                      |
| IOC context                               | Prepared                                  | Observed and scenario-only indicators separated                                              |
| Timeline                                  | Prepared                                  | Uses the exact timestamps in the supplied files                                              |
| Cross-source correlation                  | Prepared                                  | Windows logon and PowerShell notes used as corroboration                                     |
| Detection suggestions                     | Proposed                                  | Not deployed or tested                                                                       |
| Local Llama 3.1 run                       | Pending                                   | Must be run locally under the team's agreed settings                                         |
| Nada request queue                        | Not found in reviewed repository branches | No Nader-assigned Week 2 request from Nada was present in the inspected repository snapshots |
| Evaluation Record / hallucination scoring | Pending                                   | Requires the actual local Llama output and claim-by-claim validation                         |
| Cross-review by Adam                      | Pending                                   | Per the team cross-review circle                                                             |
| Second validation by Nada                 | Pending                                   | Per Week 2 validation checklist                                                              |

### Model environment items to confirm before the run

- Nader's Week 1 offline test notes record Ollama `0.40.13`.
- Mahmoud's Week 2 output records Ollama `0.40.11`.
- The project requires one agreed Ollama version for all runners; no
  team-wide decision is evidenced in the reviewed files. Do not silently
  choose one.
- Use Llama 3.1 8B Instruct Q4_K_M, context 8192, temperature 0.2, seed
  42, and no `SYSTEM` line in the Modelfile, as specified by the
  kickoff/work-plan rules.

------------------------------------------------------------------------

## Appendix A — Source links

- [Nader network evidence folder — branch
  `week1/mahmoud-benchmark`](https://github.com/adamissaalrefai/Night_Shift/tree/week1/mahmoud-benchmark/evidence)
- [Unified Attack Scenario
  v1.0](https://github.com/adamissaalrefai/Night_Shift/blob/7b34d77da5dd4713a7a81dc7f02aae4fa61707eb/KICK_mahmoud_attack-scenario_v1.md)
- [Week 1 Answer Key
  v1.0](https://github.com/adamissaalrefai/Night_Shift/blob/5e0a1206a2f0ba0526818fe5b06c4c5939547a82/week1/W1_mina_answer-key_v1.md)
- [Shared AI Request and SOC Prompt Standard
  v1.0](https://github.com/adamissaalrefai/Night_Shift/blob/7b34d77da5dd4713a7a81dc7f02aae4fa61707eb/template/IR_Capstone_Shared_AI_Request_and_SOC_Prompt_v1.md)
