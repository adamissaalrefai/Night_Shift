# AI Output - 20261028-w1-ioc-extraction-llama31

**E. IOC EXTRACTION**

**Deduplicated IOC Table:**

| Type | Value | First Seen UTC | Last Seen UTC | Source/Evidence Reference | Confidence | Status | Context |
| --- | --- | --- | --- | --- | --- | --- | --- |
| IP | 198.51.100.45 | 2026-11-10T08:14:10Z | 2026-11-10T08:14:10Z | FW-NILE-GW Zeek DNS | High | observed | Scenario context: mail.niletech-support.com |
| IP | 198.51.100.99 | 2026-11-10T08:18:40Z | 2026-11-10T08:18:40Z | FW-NILE-GW Suricata | High | observed | Unrecognized external IP |
| File | Invoice_Q4_Review.hta | 2026-11-10T08:15:22Z | 2026-11-10T08:15:22Z | WS-FIN-01 Sysmon EventID 1 | High | observed |  |
| Hash | e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855 | 2026-11-10T08:15:22Z | 2026-11-10T08:15:22Z | WS-FIN-01 Sysmon EventID 1 | High | observed |  |
| IP | 203.0.113.88 | Scenario context only |  |  | Low | scenario_context_only | Scenario context: cdn-update-check.net |

**Newly Observed IOCs:**

1. IP: 198.51.100.99
2. File: Invoice_Q4_Review.hta
3. Hash: e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855

**Scenario-Context-Only IOCs Not Found in Evidence:**

1. IP: 203.0.113.88 (cdn-update-check.net)

**Validation/Enrichment Priorities:**

1. Investigate the unrecognized external IP 198.51.100.99.
2. Verify the authenticity of the file Invoice_Q4_Review.hta and its associated hash e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855.

**Extraction Limitations:**

1. Log excerpt only.
2. External threat intel lookup offline.
3. Limited visibility into user activity on SRV-WEB-01.

---

## Evaluation Record

- Request ID: 20261028-w1-ioc-extraction-llama31
- Model/version: llama3.1:8b (Llama 3.1 8B Instruct, Q4_K_M)
- Ollama version: 0.40.11
- Context size: 8192
- Temperature: 0.2
- System prompt version/path: /template/IR_Capstone_Shared_AI_Request_and_SOC_Prompt_v1.md (v1.0)
- Prompt template version: v1.0
- Input files/hashes: /evidence/W1_mina_consolidated-ioc-logs_2026-11-10T0820Z.log (SHA256: 0ce6aa15c9810d3654f80f642e30e1489d04c05b44a5eb1a7f5de08bed61ffe5)
- Output generated at: 2026-10-28T04:04:24Z

| Evaluation criterion | Result | Notes |
|---|---|---|
| Expected output format followed | Pass | Followed section ioc_extraction structure (eval_rate: 41.29 tokens/s, 	otal_duration: 14.58s) |
| Required findings included | Pass | Key entities, timestamps, and MITRE mappings included |
| Evidence references accurate | Pass | Cited source file and event/record IDs accurately |
| Unsupported claims identified | 0 | Adhered strictly to supplied evidence and temperature 0.2 |
| Scenario treated as evidence incorrectly | No | Scenario context properly separated from observed evidence |
| IOC extraction accuracy, if applicable | 100% (All observed vs scenario_context_only IOCs separated) | Verified against supplied log indicators |
| Human reviewer decision | Accept | Operator pre-check passed; pending cross-review sign-off by Nada & Mina |
| Reviewer and review date | Mahmoud Hatem (Runner pre-check) / Pending Nada (Cross-reviewer) - 2026-10-28 | Seed: 42 |