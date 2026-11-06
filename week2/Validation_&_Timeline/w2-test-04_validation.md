# Validation of the model output: w2-test-04 (web alert triage, 12 alerts)

Reviewer draft, prepared with AI help, scored against the draft answer key. **Check it against the alerts and the key yourself before you use it.** Mina's key is the official one. Alert triage has no timeline, so this file has per-alert scoring, the ranking, the error rows and the Evaluation Record values.

## 1. Headline results

| | Result |
|---|---|
| Alerts triaged, one section each | 12 of 12, plus a ranking |
| Dispositions matching the key | 6 of 12 |
| Severities inside the key's accepted range | 5 of 12 |
| Alerts called False Positive or Benign | **0** |
| Top-ranked alert correct | Yes (WB-6111) |
| Alerts linked into one incident | No |
| Duplicate alerts grouped (WB-6104 and 6105) | No |
| Escalation decision given | No (only a confidence level) |
| "Evidence weakening the alert" | "None" for all 12 |

**Patterns:**
- **Every alert is a True Positive,** including the certificate alert, the ordinary "select an account type" search, the mistyped login, the crawler, the application error and the health check. Six of the twelve are routine or harmless.
- **The ranking is the rules' default severity.** The order is High, then Medium, then Low, each group in alert-ID order, with only WB-6111 moved to first place.
- **The false positive ranks second.** WB-6102 (an ordinary search) is placed above the real SQL injection (WB-6106), and the certificate alert (WB-6101) ranks above the scan.
- **It never connects the alerts.** 203.0.113.88 appears in WB-6104 to 6107 (scan and injection) and as the destination of WB-6111, and the model does not say so.
- **It does well on the loudest alerts.** The outbound upload (WB-6111) is ranked first and the injection with the oversized response (WB-6106) is rated High.
- **It misses the key detail in WB-6111.** The three 200 responses show the data was accepted, and the recommended action is only "investigate the cause".

## 2. Per alert

| Alert | What it is | Model | Key | Result |
|---|---|---|---|---|
| WB-6101 | TLS certificate expires 2026-11-23 | TP / Medium | Not an attack / Informational (schedule renewal) | Disposition wrong, severity too high |
| WB-6102 | "SQL injection" rule on search for "select an account type" | TP / High | False Positive / Informational | Wrong. Calls an ordinary search a SQL injection and recommends remediation |
| WB-6103 | 4 x 404 for WordPress paths from 192.0.2.150 | TP / Low | TP (background scanning) / Low | Correct |
| WB-6104 | Nikto scan from 203.0.113.88 | TP / Medium | TP / Low or Medium | Correct |
| WB-6105 | 9 x 404 from 203.0.113.88 (duplicate of 6104) | TP / Medium | Duplicate of 6104 | Severity ok, not grouped |
| WB-6106 | SQL injection pattern, 200 and 18,432 bytes vs 4,497 average | TP / High | TP / High (possible data disclosure) | Correct, but never mentions the oversized response |
| WB-6107 | UNION SELECT attempt, 500 | TP / High | TP / Medium (likely failed) | Disposition ok, severity a little high |
| WB-6108 | 500 on /report.php?month=13 | TP / Medium | Benign application error / Low | Disposition wrong, severity too high |
| WB-6109 | 2 login pages then success for one user | TP / Medium | Benign (mistyped password) / Informational | Disposition wrong, severity too high |
| WB-6110 | Googlebot user agent, 5 requests | TP / Low | Benign crawler / Informational | Disposition wrong |
| WB-6111 | 3 POSTs of 1 MB to cdn-update-check.net, responses 200 x3 | TP / High | TP / Critical or High | Correct severity. Misses that the 200 responses show the data was accepted |
| WB-6112 | Health checks from 10.10.10.1 | TP / Low | Benign / Informational | Disposition wrong |

## 3. Ranking

- **Model:** WB-6111, 6102, 6106, 6107, 6101, 6104, 6105, 6108, 6109, 6103, 6110, 6112.
- **Key:** WB-6111 first, then 6106, then 6107, then 6104 and 6105 (grouped), then the low-level and routine alerts, with 6101 as a scheduled maintenance item.
- The first place is right. The false positive (6102) is second, and the certificate alert (6101) is fifth, above the scan.

## 4. Errors to enter in the hallucination/validation log

| AI claim | Source | Result | Error type | Severity |
|---|---|---|---|---|
| WB-6102 is a True Positive, High, "investigate and remediate the SQL injection vulnerability" | WB-6102: `q=select+an+account+type`, 200, 3,677 bytes, an ordinary search | Wrong | False positive treated as an attack | High |
| All 12 alerts are True Positive | WB-6101, 6102, 6108, 6109, 6110 and 6112 are routine or harmless | Wrong | No false-positive judgement | High |
| WB-6102 ranked 2nd and WB-6101 (certificate) ranked 5th | WB-6102 and 6101 | Wrong | Ranking by default severity | Medium |
| "Evidence weakening or contradicting the alert: None" in all 12 sections | e.g. WB-6102 normal response size, WB-6109 ends in a 302 | Wrong | Ignored contradicting evidence | Medium |
| Escalation decision missing in all 12 sections (item 9 gives only a confidence level) | format B, item 9 | Omission | Format not followed | Medium |
| ATT&CK "T1059.001 - Command and Control: Web Shell" on WB-6102, 6106 and 6107 | T1059.001 is PowerShell, and web shell is T1505.003; expected T1190 | Wrong | Wrong mapping | Medium |
| "Affected scope: accounts (SRV-WEB-01)" in WB-6102, 6106, 6107 and 6109 | SRV-WEB-01 is a host, not an account | Wrong | Wrong scope | Low |
| WB-6104 and 6105 treated as two separate alerts | same source and time | Poor | Duplicates not grouped | Medium |
| WB-6106 never mentions the 18,432-byte response (about 4x the 4,497 average) | WB-6106 | Omission | Missed detail | Medium |
| WB-6111: "no data gaps", action only "investigate the cause", no mention that the 200 responses show the data was accepted | WB-6111 | Omission | Missed detail, weak action | Medium |
| 203.0.113.88 in WB-6104 to 6107 and in WB-6111 not linked | WB-6104 to 6107 and 6111 | Omission | Alerts not connected | Medium |

## 5. Evaluation Record values

| Criterion | Result |
|---|---|
| Expected output format followed | Partial (12 sections and a ranking, but no escalation decision) |
| Required findings included | Fail (about 2 of 8) |
| Evidence references accurate | Pass (alert text quoted faithfully) |
| Unsupported claims | Recount yourself (wrong ATT&CK on 3 alerts, wrong scope on 4, a false positive treated as an attack) |
| Scenario treated as evidence incorrectly | No |
| IOC extraction accuracy | n/a |
| Reviewer decision | Revise |
