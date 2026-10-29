# IR Capstone: Answer Key (Week 1 Benchmark)

- **Owner:** Mina (Validation and Evidence Lead)
- **Version:** v1.0 (DRAFT, derived from Attack Scenario v1.0 dated 2026-10-20)
- **Time standard:** UTC, ISO 8601 (`YYYY-MM-DDTHH:MM:SSZ`)
- **Scope:** the five shared test tasks: log analysis, alert triage, threat summarization, report writing, IOC extraction
- **Reviewer (cross-review circle):** Nader reviews Mina

---

## 0. Rules for using this key

1. **Written before any AI output is run or read.** This key comes from the scenario and the raw datasets only. Nobody edits it after seeing a model's answer. If an error in the key is found later, create a new version (v1.1) and record why.
2. **The scenario is context, not evidence.** The SOC prompt tells the model to label statements as *Confirmed / Suspected / Scenario context only / Unknown*. This key uses the same labels. A finding is only "expected" for a given test input if that input actually contains the supporting log lines.
3. **Each finding has an ID** (for example `LA-W-03`). Use the ID in the scoring sheet and in the hallucination log.
4. **Dataset reference column.** The datasets are not final yet. The "Dataset ref" column is left blank on purpose. Fill it with the real file name and event ID or line number as soon as the log owner delivers the data. A finding with no dataset ref cannot be scored.
5. **Negative controls (NC-xx)** list things the model must NOT claim. Making one of these claims counts as an unsupported claim and is logged in the hallucination log.

### Status labels

| Label | Meaning |
|---|---|
| Confirmed | Directly supported by a log line in the supplied input |
| Suspected | Reasonable inference; the basis must be stated |
| Scenario only | True in the scenario but not visible in the supplied input |
| Unknown | Cannot be determined from the supplied input |

### Input slices (proposed, owners to confirm)

| Slice | Content | Log owner |
|---|---|---|
| W | Windows logs from `WS-FIN-01` and `DC-NILE-01` (Security, Sysmon, PowerShell, Task Scheduler) | Mahmoud |
| L | Linux and web logs from `SRV-WEB-01` (`auth.log`, `syslog`, web access/error log, `auditd` if available) | Adam |
| N | Network logs and alerts from `FW-NILE-GW` (Zeek `conn`, `dns`, `http`; Suricata `eve.json`) | Nader |

---

## 1. Task 1: Log analysis (`log_analysis`)

Total key items: 14 (W: 6, L: 6, N: 2). Score each slice against its own items only. For example, a Windows-only input has 6 key items.

### 1A. Windows slice (W)

| ID | Expected finding | Expected evidence source | Status | ATT&CK | Dataset ref |
|---|---|---|---|---|---|
| LA-W-01 | User `sara.ahmed` on `WS-FIN-01` (`10.10.10.15`) opened/executed `Invoice_Q4_Review.hta` | Sysmon EID 1 or Security 4688 (process creation) | Confirmed | T1566.001 | |
| LA-W-02 | `mshta.exe` spawned `powershell.exe` with an encoded/obfuscated command | Sysmon EID 1 / Security 4688 command line; PowerShell 4104 | Confirmed | T1059.001 (T1218.005 for mshta also acceptable) | |
| LA-W-03 | PowerShell made an outbound HTTP/HTTPS connection to `198.51.100.45` to fetch `updater.ps1` | Sysmon EID 3 / PowerShell 4104 | Confirmed | T1059.001 (T1105 acceptable) | |
| LA-W-04 | Scheduled task `NileTechUpdater` created on `WS-FIN-01` | Security 4698, Task Scheduler 106, or Sysmon | Confirmed | T1053.005 | |
| LA-W-05 | Process access to `lsass.exe` memory on `WS-FIN-01` (credential dumping) | Sysmon EID 10 (target `lsass.exe`) | Confirmed | T1003.001 | |
| LA-W-06 | SMB logon attempts from `10.10.10.15` to `DC-NILE-01` (`10.10.20.5`), both failed and successful | Security 4625 and 4624 (logon type 3) on DC | Confirmed (the logons); compromise of the DC is **Unknown** (see NC-02) | T1021.002 | |

### 1B. Linux and web slice (L)

| ID | Expected finding | Expected evidence source | Status | ATT&CK | Dataset ref |
|---|---|---|---|---|---|
| LA-L-01 | Automated web vulnerability scan against `SRV-WEB-01` from `203.0.113.88` (many requests, many 404s, scanner user agent such as Nikto) | Web access log | Confirmed | T1595 | |
| LA-L-02 | High-volume SSH brute force against port 22 from `203.0.113.88` | `auth.log` ("Failed password" bursts) | Confirmed | T1110.001 | |
| LA-L-03 | Successful SSH login as `dev_admin` on `SRV-WEB-01` from `10.10.10.15` (an internal source, different from the brute-force source) | `auth.log` ("Accepted ...") | Confirmed | T1021.004 | |
| LA-L-04 | `dev_admin` escalated to `root` through `sudo` (`sudo -l`, then a root shell or command) | `auth.log` sudo entries / `auditd` | Confirmed | T1548.003 | |
| LA-L-05 | `root` created `/tmp/.db_dump.tar.gz` (database dump compressed with `tar`) | `auditd` / shell history / syslog | Confirmed (or Suspected if only file listing exists) | T1560.001 | |
| LA-L-06 | Chunked HTTP POST requests from `SRV-WEB-01` to `203.0.113.88` carrying the archive | Web server/proxy log | Confirmed (or Suspected if only size anomalies are visible) | T1041 | |

### 1C. Network slice (N)

| ID | Expected finding | Expected evidence source | Status | ATT&CK | Dataset ref |
|---|---|---|---|---|---|
| LA-N-01 | Beaconing/C2-style traffic from `10.10.10.15` to `198.51.100.45` (repeated HTTP/HTTPS connections at regular intervals) | Zeek `conn.log` / `http.log`; Suricata alert | Confirmed | T1071.001 acceptable (not in scenario list) | |
| LA-N-02 | Anomalous DNS queries from `10.10.20.10` toward `203.0.113.88` / `cdn-update-check.net`, plus large outbound transfer to the same host | Zeek `dns.log`, `conn.log` | Confirmed | T1048 | |

### 1D. Required structure (format check for task A)

The output must contain: executive finding (max 3 sentences), event timeline table, findings table, observed IOCs, scenario alignment/contradictions/unknowns, data gaps, prioritized actions (P1 to P3). Every material finding needs an evidence reference and a confidence value.

---

## 2. Task 2: Alert triage (`alert_triage`)

Four test alerts. Alert TR-04 is a **decoy** and must be added to the dataset by the dataset owner. Without a benign case, the test cannot detect a model that labels everything "True Positive".

| ID | Alert (input) | Expected disposition | Expected severity | Expected escalation | Key evidence the answer must cite |
|---|---|---|---|---|---|
| TR-01 | Endpoint alert: `mshta.exe` launched `powershell.exe` with an encoded command on `WS-FIN-01` (user `sara.ahmed`) | True Positive | High (Critical accepted if justified) | Yes | Parent/child process, encoded command line, `Invoice_Q4_Review.hta` |
| TR-02 | Suricata alert: large outbound HTTP POST from `SRV-WEB-01` (`10.10.20.10`) to `203.0.113.88` | True Positive | Critical | Yes | Source/destination, POST size/count, destination matches `cdn-update-check.net` |
| TR-03 | Authentication alert: repeated failed SSH logins on `SRV-WEB-01` from `203.0.113.88` | True Positive | Medium (High only if a successful login from that IP is in the evidence) | Yes (monitor and block) | Count of failures, source IP, time range, no "Accepted" from that IP |
| TR-04 | **Decoy (to add):** `svc_backup` network logon (type 3) to `DC-NILE-01` at a regular scheduled time | Benign True Positive (or False Positive) | Informational or Low | No | Known service account, regular schedule, no failures, no other suspicious activity |

Required structure (format B): disposition, severity, trigger and evidence, affected scope, supporting evidence, weakening/contradicting evidence, ATT&CK mapping, immediate containment and evidence-collection actions, escalation decision with confidence and data gaps.

Scoring notes:
- Wrong disposition on TR-04 (calling the decoy a confirmed attack) is a **Critical** error.
- For TR-03, claiming the brute force succeeded without an "Accepted" line is NC-03.

---

## 3. Task 3: Threat summarization (`threat_summarization`)

Input: a mixed evidence pack covering all three slices (W + L + N). Expected content elements:

| ID | Expected element | Notes |
|---|---|---|
| TS-01 | Situation summary of **150 words or fewer** | Hard format check; count the words |
| TS-02 | Initial access through the phishing attachment to `sara.ahmed` on `WS-FIN-01` | Confirmed |
| TS-03 | Execution and persistence: encoded PowerShell and scheduled task `NileTechUpdater` | Confirmed |
| TS-04 | Credential access: LSASS access on `WS-FIN-01` | Confirmed |
| TS-05 | Lateral movement: SSH into `SRV-WEB-01` as `dev_admin`, then privilege escalation to `root` | Confirmed if L slice present |
| TS-06 | Collection and exfiltration: `/tmp/.db_dump.tar.gz` sent to `203.0.113.88` | Confirmed or Suspected, depending on evidence strength |
| TS-07 | Affected scope lists `WS-FIN-01`, `sara.ahmed`, `dev_admin`, `SRV-WEB-01`; `DC-NILE-01` listed as **suspected/unknown**, not compromised | See NC-02 |
| TS-08 | Separate sections for Confirmed, Suspected, Scenario-context-only activity | Format check |
| TS-09 | Impact split into confirmed versus potential (customer financial records are **potential** unless the archive contents are evidenced) | NC-09 |
| TS-10 | Priority actions framed as recommendations, none claimed as done | NC-06 |

---

## 4. Task 4: Report writing (`report_writing`)

Input: consolidated evidence and findings (the validated set from Task 1). Expected report sections (format D):

| ID | Required section | Pass condition |
|---|---|---|
| RW-01 | Report metadata and evidence scope | Lists input files and the time range; no invented files |
| RW-02 | Executive summary | Plain language; no unsupported impact claims |
| RW-03 | Confirmed timeline | Ordered by UTC timestamp; each entry cites evidence; no event outside `2026-11-10T08:00:00Z` to `2026-11-10T16:00:00Z` unless the evidence shows one |
| RW-04 | Technical findings with evidence references | Covers LA-W-01 to LA-W-06 and LA-L-01 to LA-L-06 that are present in the input |
| RW-05 | Affected assets, accounts, and observed IOCs | Matches the IOC key in section 5 |
| RW-06 | ATT&CK mapping | Only mappings supported by evidence; "not mapped" where unsupported |
| RW-07 | Confirmed versus potential impact | Separated clearly |
| RW-08 | Recommended containment, eradication, recovery, follow-up | Recommendations only; none stated as completed |
| RW-09 | Limitations, unresolved questions, required approvals | Names missing evidence (for example no PCAP, no memory image) |

---

## 5. Task 5: IOC extraction (`ioc_extraction`)

Input: the full mixed evidence pack. Expected deduplicated IOC table (9 items):

| ID | Type | Value | Expected status | Context | Dataset ref |
|---|---|---|---|---|---|
| IOC-01 | ipv4 | `198.51.100.45` | observed | Phishing source and C2 | |
| IOC-02 | domain | `mail.niletech-support.com` | observed | Resolves to `198.51.100.45` | |
| IOC-03 | ipv4 | `203.0.113.88` | observed | Scan, brute force, exfiltration destination | |
| IOC-04 | domain | `cdn-update-check.net` | observed | Resolves to `203.0.113.88` | |
| IOC-05 | filename | `Invoice_Q4_Review.hta` | observed | Phishing attachment | |
| IOC-06 | filename | `updater.ps1` | observed | Secondary payload | |
| IOC-07 | other (scheduled task) | `NileTechUpdater` | observed | Persistence | |
| IOC-08 | filepath | `/tmp/.db_dump.tar.gz` | observed | Staging archive | |
| IOC-09 | sha256 | `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` | scenario_context_only unless the exact hash appears in the evidence | Simulated malware hash | |

Rules for scoring this task:

- **Deduplicate.** The same IP seen in five logs is one row, with first-seen and last-seen times.
- **Status per input.** If a test input does not contain a given IOC, the expected status is `scenario_context_only`, not `observed`. Marking it `observed` is NC-04 or NC-05.
- **Do not list internal assets as IOCs.** `10.10.10.15`, `10.10.20.5`, `10.10.20.10`, `10.10.10.1`, hostnames and user accounts are assets and accounts, not indicators. Listing them is a precision error.
- **Hash caveat.** The scenario hash `e3b0c442...b855` is the well-known SHA-256 of an **empty input**. It is a placeholder. It must not be presented as a real malware hash, and an enrichment lookup on it is meaningless. Replace it with a real test or public hash before Week 3 and update this key (v1.1).
- IOC extraction accuracy is reported as: correct IOCs found ÷ 9, with false IOCs counted separately.

---

## 6. Negative controls (what the model must NOT claim)

| ID | Must NOT claim | Why |
|---|---|---|
| NC-01 | That exfiltration, lateral movement, or any stage happened when the supplied input does not contain it | Scenario is context only |
| NC-02 | That `DC-NILE-01` or the domain was compromised | The scenario only describes SMB probing with failed and successful logons; compromise is not established |
| NC-03 | That the SSH brute force from `203.0.113.88` succeeded | The successful login in the scenario comes from `10.10.10.15` with stolen credentials, a different source |
| NC-04 | That the SHA-256 hash is a verified malware hash, or observed when it is absent | Placeholder hash, see section 5 |
| NC-05 | Any invented user, host, IP, domain, CVE, timestamp, or event ID not in the evidence | Hallucination |
| NC-06 | That containment, blocking, password resets, or recovery were performed | No evidence records any response action |
| NC-07 | That `198.51.100.45` and `203.0.113.88` are one actor or one server | The scenario assigns them different roles |
| NC-08 | Attribution to a named threat group or country | No supporting evidence |
| NC-09 | Confirmed customer data loss or record counts | Contents of the archive are not evidenced |
| NC-10 | Timestamps outside the simulation window, or local-time timestamps presented as UTC | Time standard is UTC |

---

## 7. How this key feeds scoring

| Scoring input | Taken from this key |
|---|---|
| Key items total (Completeness) | Number of IDs for the slice or task (Task 1: W 6, L 6, N 2; Task 2: 4; Task 3: 10; Task 4: 9; Task 5: 9) |
| Key items found | IDs the output correctly reports |
| Wrong claims | Statements contradicted by the evidence |
| Unsupported claims | Statements not found in the evidence, including any NC-xx |
| Critical error flag | An NC-xx violation, a wrong disposition on TR-04, or an invented IOC |

---

## 8. Open items before this key is final

| # | Item | Owner |
|---|---|---|
| 1 | Fill every "Dataset ref" cell with the real file and event ID or line number | Mina, with Mahmoud, Adam, Nader |
| 2 | Add the TR-04 decoy alert (and optionally one more benign alert) to the alert dataset | Mina with the log owners |
| 3 | Replace the placeholder hash with a real test hash and bump the key to v1.1 | Mahmoud (scenario owner) |
| 4 | Confirm the key-item counts per slice once datasets are final | Mina |
| 5 | Scenario file ends with an empty "## 4" heading; confirm nothing is missing | Mahmoud |
| 6 | Cross-review of this key | Nader |

| Sign-off | Name | Date (UTC) |
|---|---|---|
| Author | Mina | |
| Cross-reviewer | Nader | |
