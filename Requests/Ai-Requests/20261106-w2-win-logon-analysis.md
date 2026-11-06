# AI Request (Template v1.0)

## 1. Metadata
- **Request ID:** 20261106-w2-win-logon-analysis
- **Date (UTC):** 2026-11-06T09:15:00Z
- **Week:** Week 2 (Log Analysis, Triage and Detection)
- **Task Type:** Windows Logon Events Analysis & Timeline

## 2. Execution Assignment
- **Requester:** Mahmoud (Role 1: Windows & Endpoint Operator)
- **Assigned Runner:** Mahmoud (Main Runner - Windows & Endpoint)
- **Backup Runner:** Adam
- **Target Model:** llama3.1:8b (Q4_K_M)
- **Parameters:** num_ctx=8192, temperature=0.2, seed=42, system_prompt=None

## 3. Evidence Manifest
- **Source File(s):** evidence/W2_mahmoud_windows-logon-events_2026-11-06T0900Z.log
- **SHA-256 Hash:** 91705F2E6EC9124578AC974C7E90F40010E3E3851AC9DD28F7C4244B820964D5
- **Chunk Size:** Full log chunk (< 8192 tokens)

## 4. Prompt (SOC Analyst Prompt v1.0)
You are a Senior SOC Analyst assisting with Incident Response for NileTech Financial Services.
Strict Rules:
1. Base every finding strictly on the provided log lines. Do not invent or assume facts not present in the logs.
2. Cite the exact [LINE-XX] and ISO 8601 UTC timestamp for every event mentioned.
3. Structure your response into:
   - Executive Summary
   - Chronological Timeline (UTC)
   - Extracted IOCs & Artifacts
   - Detection Improvement Suggestions

Task Instructions:
Analyze the Windows Security Logon events. Identify normal vs malicious logons, brute-force attempts, lateral movement, and privilege assignment. Cite exact [LINE-XX] and UTC timestamps for every claim, build a chronological timeline, and propose 2 SIEM detection rules.

Raw Evidence Logs:
--- BEGIN LOGS ---
[LINE-01] 2026-11-06T08:15:10Z | Host: WS-FIN-01 | IP: 10.10.10.15 | EventID: 4624 | LogonType: 2 (Interactive) |TargetUser: NILETECH\sara.ahmed | SourceIP: 127.0.0.1 | Status: Success | Desc: Normal workstation logon
[LINE-02] 2026-11-06T08:42:05Z | Host: DC-NILE-01 | IP: 10.10.20.5 | EventID: 4625 | LogonType: 3 (Network) | TargetUser: NILETECH\admin_backup | SourceHost: WS-FIN-01 | SourceIP: 10.10.10.15 | Status: Failure (0xC000006A - Bad Password)
[LINE-03] 2026-11-06T08:42:08Z | Host: DC-NILE-01 | IP: 10.10.20.5 | EventID: 4625 | LogonType: 3 (Network) | TargetUser: NILETECH\admin_backup | SourceHost: WS-FIN-01 | SourceIP: 10.10.10.15 | Status: Failure (0xC000006A - Bad Password)
[LINE-04] 2026-11-06T08:43:12Z | Host: DC-NILE-01 | IP: 10.10.20.5 | EventID: 4624 | LogonType: 3 (Network) | TargetUser: NILETECH\admin_backup | SourceHost: WS-FIN-01 | SourceIP: 10.10.10.15 | AuthPackage: NTLM | Status: Success | Desc: Lateral movement network logon using stolen hash
[LINE-05] 2026-11-06T08:43:12Z | Host: DC-NILE-01 | IP: 10.10.20.5 | EventID: 4672 | TargetUser: NILETECH\admin_backup | Privileges: SeDebugPrivilege, SeBackupPrivilege, SeTakeOwnershipPrivilege | Desc: Special privileges assigned to new logon
[LINE-06] 2026-11-06T08:50:30Z | Host: DC-NILE-01 | IP: 10.10.20.5 | EventID: 4634 | LogonType: 3 (Network) | TargetUser: NILETECH\admin_backup | Status: Logoff

--- END LOGS ---
