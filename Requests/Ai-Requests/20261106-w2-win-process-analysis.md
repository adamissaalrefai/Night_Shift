# AI Request (Template v1.0)

## 1. Metadata
- **Request ID:** 20261106-w2-win-process-analysis
- **Date (UTC):** 2026-11-06T09:15:00Z
- **Week:** Week 2 (Log Analysis, Triage and Detection)
- **Task Type:** Windows Process Creation Analysis & Timeline

## 2. Execution Assignment
- **Requester:** Mahmoud (Role 1: Windows & Endpoint Operator)
- **Assigned Runner:** Mahmoud (Main Runner - Windows & Endpoint)
- **Backup Runner:** Adam
- **Target Model:** llama3.1:8b (Q4_K_M)
- **Parameters:** num_ctx=8192, temperature=0.2, seed=42, system_prompt=None

## 3. Evidence Manifest
- **Source File(s):** evidence/W2_mahmoud_windows-process-creation_2026-11-06T0900Z.log
- **SHA-256 Hash:** F1945CD6C19E56B3C1C78943EF5EC18116907A4CA1EFC40A57D48AB1DB7ADFC5
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
Analyze the Windows Process Creation events (EventID 4688). Trace the execution chain from initial access (macro document) to encoded PowerShell, credential dumping, NTDS dumping, and data archiving. Cite exact [LINE-XX] and UTC timestamps for every claim, list all IOCs (hashes, file paths, commands), and propose 2 detection rules.

Raw Evidence Logs:
--- BEGIN LOGS ---

--- END LOGS ---
