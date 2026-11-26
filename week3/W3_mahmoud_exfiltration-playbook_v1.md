# Week 3 Deliverable: SOAR Data Exfiltration Playbook & Test Results

- **Document ID:** W3_mahmoud_exfiltration-playbook_v1.md
- **Playbook ID:** PB-SOAR-001 (Windows & Endpoint Data Exfiltration)
- **Owner:** Mahmoud Hatem (Role 1: Operator - Windows & Endpoint)
- **Cross-Reviewer:** Nada (Role 5: Playbook, Detection & Reporting Lead)
- **Date (UTC):** 2026-11-13T11:00:00Z
- **Integration Depth:** Deterministic SOAR Workflow + Mock Ticketing Flow (TheHive Schema)

---

## 1. Trigger
The playbook triggers automatically when the SIEM correlates suspicious local archiving or PowerShell web upload activity with anomalous outbound data transfer:
- **Condition A (Endpoint Archiving + PowerShell Upload):** Windows EventID `4688` spawning `7z.exe` or `rar.exe` targeting sensitive directories (`C:\Finance\Confidential\*`) followed within 10 minutes by PowerShell EventID `4104` containing `Invoke-RestMethod` or `Invoke-WebRequest` with `-Method Post` or `-InFile`.
- **Condition B (Network Egress Anomaly):** Suricata IDS Alert `2031500` or Firewall egress log showing outbound transfer `> 10 MB` to an external non-standard HTTPS port (e.g., `8443`) from the workstation subnet (`10.10.10.0/24`).

---

## 2. Enrichment
Once triggered, the workflow gathers host, identity, and threat intelligence context before reaching the decision points:
1. **Endpoint Artifact Extraction [Automated]:** Query Sysmon EventID `11` and Windows EventID `4688` on the source host to pull the staging archive path, file size, SHA-256 hash, parent process, and full command line.
2. **Asset & User Context Lookup [Automated]:** Query Active Directory and the internal asset inventory for the source host (`WS-FIN-01`) and user (`NILETECH\sara.ahmed`) to identify department criticality (Finance) and privilege level.
3. **Threat Intelligence Lookup [Automated - Simulated]:** Check destination IP (`185.220.101.45`) and archive SHA-256 hash against VirusTotal and AbuseIPDB feeds to retrieve reputation score and C2 categorization.

---

## 3. Decision Points
- **Decision Gate 1 (Destination IP Malicious OR Transfer > 25 MB to Non-Standard Port?):**
  - **YES (High-Confidence Exfiltration):** Immediately execute automated C2 IP block and endpoint isolation, generate a Critical (P1) mock ticket, and pause for analyst approval on account disablement.
  - **NO (Low-Confidence / Possible Authorized Upload):** Create a Medium (P3) ticket and route to Tier-1 SOC for manual business-justification check.
- **Decision Gate 2 (Does Source Asset Belong to Finance/Executive Group or Sensitive Directory?):**
  - **YES:** Auto-tag case as `Data-Breach-Risk`, assign SLA `< 15 minutes`, and trigger Incident Commander / DPO escalation path.
  - **NO:** Follow standard Tier-2 endpoint containment SLA (`< 1 hour`).

---

## 4. Containment
1. **Firewall C2 Egress Block [Automated]:** Push immediate block rule for destination IP `185.220.101.45` (all ports, including `8443`) on `FW-NILE-GW` and host Windows Firewall.
2. **Endpoint Network Isolation [Automated]:** Isolate `WS-FIN-01 (10.10.10.15)` at the switch/EDR level, allowing only SOC management traffic to stop further data bleed.
3. **Process Termination & Staging File Quarantine [Manual Approval Required]:** Terminate active `powershell.exe` upload process and move `C:\Users\Public\fin_records_exfil.7z` to forensic quarantine while preserving timestamps and hash integrity.
4. **AD Account Disablement & Session Revocation [Manual Approval Required]:** Disable `NILETECH\sara.ahmed` in Active Directory and revoke active Kerberos/NTLM sessions after analyst verification.

---

## 5. Ticket Creation (Mock TheHive Ticketing Flow)
The SOAR workflow maps enriched SIEM fields directly into a standardized mock TheHive case object:

```json
{
  "case_id": "CASE-2026-1106-EXFIL01",
  "title": "[CRITICAL] Confirmed Confidential Data Exfiltration from WS-FIN-01 to 185.220.101.45:8443",
  "severity": "Critical (P1)",
  "tlp": "TLP:AMBER",
  "status": "Open - Containment In Progress",
  "owner": "SOC-Tier2-IR",
  "affected_assets": ["WS-FIN-01 (10.10.10.15)"],
  "affected_users": ["NILETECH\\sara.ahmed"],
  "mitre_attack": ["T1560.001 - Archive via Utility", "T1041 - Exfiltration Over C2 Channel"],
  "observables": [
    {"type": "ip", "value": "185.220.101.45", "ioc": true, "reputation": "Malicious C2 (100%)"},
    {"type": "filename", "value": "C:\\Users\\Public\\fin_records_exfil.7z", "size_bytes": 54829100},
    {"type": "sha256", "value": "9f86d081884c7d659a2feaa0c55ad015a3bf4f1b2b0b822cd15d6c15b0f00a08"}
  ],
  "evidence_refs": ["[EXFIL-01]", "[EXFIL-02]", "[EXFIL-03]", "[EXFIL-04]"]
}
```

---

## 6. Escalation
- **Tier-2 Incident Response Lead:** Immediate escalation (`SLA: < 15 minutes`) upon confirmed exfiltration of `C:\Finance\Confidential\*`.
- **CISO & Data Protection Officer (DPO):** Escalate within 1 hour due to confirmed egress of `54.8 MB` of password-protected financial records (`fin_records_exfil.7z`) to an external malicious IP.
- **Parallel Lateral Movement Check:** Notify Network & AD leads to audit subsequent connections from `10.10.10.15` to `DC-NILE-01 (10.10.20.5)`.

---

## 7. Manual vs Automated Step Summary

| Step # | Playbook Phase | Action Description | Execution Mode | Guardrail / Justification |
| :--- | :--- | :--- | :--- | :--- |
| 1 | Trigger | Correlate EventID `4688` (`7z.exe`), `4104` (`Invoke-RestMethod`), and IDS `2031500` | **Automated** | Deterministic SIEM correlation rule |
| 2 | Enrichment | Collect archive path, SHA-256, file size, AD user role, and Threat Intel score | **Automated** | Read-only lookups with zero operational impact |
| 3 | Decision | Evaluate C2 reputation (`100%`), transfer size (`54.8 MB`), and Finance asset tag | **Automated** | Rule-based threshold gates |
| 4 | Containment | Block C2 IP `185.220.101.45` on `FW-NILE-GW` and host firewall | **Automated** | High-confidence malicious external IP |
| 5 | Containment | Isolate workstation `WS-FIN-01 (10.10.10.15)` from internal VLANs | **Automated** | Prevents immediate further exfiltration or lateral spread |
| 6 | Containment | Kill active PowerShell session and quarantine `fin_records_exfil.7z` | **Manual (Approval)** | Ensures forensic evidence integrity before cleanup |
| 7 | Containment | Disable `NILETECH\sara.ahmed` and revoke active domain sessions | **Manual (Approval)** | Prevents accidental business disruption on false positives |
| 8 | Ticketing | Generate Mock TheHive Case `CASE-2026-1106-EXFIL01` with observables | **Automated** | Standardized schema mapping from SIEM fields |
| 9 | Escalation | Notify Tier-2 IR Lead, CISO, and DPO regarding financial data exposure | **Manual** | Requires human verification of affected records and legal scope |

---

## 8. Scenario Test Case & Execution Results

### 8.1 Test Input
- **Source File:** `evidence/W3_mahmoud_soar-exfil-testcase_2026-11-13T1000Z.log`
- **NileTech Scenario Slice:** Between `2026-11-06T08:46:40Z` and `08:48:12Z`, compromised user `NILETECH\sara.ahmed` on `WS-FIN-01 (10.10.10.15)` archived `C:\Finance\Confidential\*` into `C:\Users\Public\fin_records_exfil.7z` (`54.8 MB`) using `7z.exe` (`[EXFIL-01]`, `[EXFIL-02]`) and uploaded it via PowerShell `Invoke-RestMethod` (`[EXFIL-03]`) over HTTPS port `8443` to C2 IP `185.220.101.45` (`[EXFIL-04]`).

### 8.2 Step-by-Step Test Results

| Test Step | Evidence Cited | Expected Playbook Behavior | Observed Test Outcome | Status |
| :--- | :--- | :--- | :--- | :--- |
| **1. Trigger Match** | `[EXFIL-01]`, `[EXFIL-03]`, `[EXFIL-04]` | Trigger fires within 10-minute window on archiving + PowerShell POST + IDS `2031500` | Trigger matched at `2026-11-06T08:48:12Z` across endpoint and firewall logs | **PASS** |
| **2. Artifact & TI Enrichment** | `[EXFIL-02]`, `[EXFIL-04]` | Extract `54.8 MB` size, SHA-256 hash, and flag `185.220.101.45` as Malicious C2 | All observables extracted accurately; AbuseConfidence confirmed at `100%` | **PASS** |
| **3. Decision Gates 1 & 2** | `[EXFIL-01]`, `[EXFIL-04]` | Take YES branch on Gate 1 (`>25 MB` + C2 IP) and Gate 2 (Finance directory) | Routed directly to Critical (P1) automated containment and DPO escalation | **PASS** |
| **4. Automated Containment** | `[EXFIL-04]` | Block `185.220.101.45` and isolate `WS-FIN-01 (10.10.10.15)` | Firewall block rule and host isolation action queued cleanly without blocking SOC access | **PASS** |
| **5. Manual Approval Gates** | `[EXFIL-01]`, `[EXFIL-02]` | Pause for analyst confirmation before disabling `sara.ahmed` and quarantining archive | Playbook held account disablement and file quarantine at human approval checkpoint | **PASS** |
| **6. Mock Ticket Generation** | `[EXFIL-01]` to `[EXFIL-04]` | Create `CASE-2026-1106-EXFIL01` populated with IPs, file hash, MITRE tags, and evidence refs | Structured JSON case populated with 0 missing fields and exact UTC timestamps | **PASS** |
