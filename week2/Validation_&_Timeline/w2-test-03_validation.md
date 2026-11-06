# Validation of the model output: w2-test-03 (Linux alert triage, 12 alerts)

Reviewer draft, prepared with AI help, scored against the draft answer key. **Check it against the alerts and the key yourself before you use it.** Mina's key is the official one. Alert triage has no timeline, so this file has per-alert scoring, the ranking, the error rows and the Evaluation Record values.

## 1. Headline results

| | Result |
|---|---|
| Alerts triaged, one section each | 12 of 12, plus a ranking |
| Dispositions matching the key | 9 of 12 |
| Severities inside the key's accepted range | 4 of 12 (LX-5108 partial) |
| Alerts called False Positive or Benign | **0** |
| Top-ranked alert correct | Yes (LX-5111) |
| Alerts linked into one incident | No |
| Duplicate alerts grouped (LX-5105 and 5106) | No |
| Escalation decision given | No (only a confidence level) |
| "Evidence weakening the alert" | "None" for all 12 |

**Patterns:**
- **Every alert is a True Positive,** including the routine backup, the CPU alert and the apt update. LX-5103 and LX-5104 say in the alert text that the same alert fired on 14 of 14 and 6 of 14 days, and the model ignored that.
- **The ranking follows severity, not risk.** The model's own closing note says the ranking is "based on the severity of the alerts".
- **It never connects the alerts.** The chain LX-5107, 5108, 5109, 5110, 5111 and 5112 is one incident on one host and account, but the model ranks those alerts 7th, 5th, 2nd, 8th, 1st and last. Meanwhile a failed brute force (LX-5105) is rated Critical and ranked 3rd.
- **It does well on the loudest alerts.** The shell escape (LX-5109) and the outbound upload (LX-5111) are rated Critical and ranked 1st and 2nd.
- **It did not overclaim.** It does not say the upload completed or what the archive contained.
- **It added an opening sentence and a closing note,** which format B does not allow.

## 2. Per alert

| Alert | What it is | Model | Key | Result |
|---|---|---|---|---|
| LX-5101 | apt update by dev_admin from 10.10.10.30 | TP / Medium | Benign / Informational | Disposition wrong, severity too high |
| LX-5102 | 7 outside sources, 1 failed password each | TP / High | TP (background scanning) / Informational or Low | Disposition ok, severity too high. Lists dev_admin as affected (not in the alert) |
| LX-5103 | cron backup tar, same on 14 of 14 days | TP / Medium | Benign / Informational | Disposition wrong, severity too high. Ignored the 14-day history in the alert |
| LX-5104 | CPU above 90%, same window on 6 of 14 days | TP / Low | Benign / Informational or Low | Disposition wrong, severity ok |
| LX-5105 | 203.0.113.88: 6 failed passwords, 0 successes | TP / Critical | TP / Low or Medium | Disposition ok, severity far too high |
| LX-5106 | Same source: 3 usernames tried (duplicate of 5105) | TP / Medium | TP / Low or Medium, grouped with 5105 | Ok, but not grouped |
| LX-5107 | dev_admin password logon from 10.10.10.15, new source | TP / Medium | TP or Needs Investigation / High | Disposition ok, severity too low |
| LX-5108 | sudo -l | TP / Low | TP / Medium in context | Partial (Low alone is defensible) |
| LX-5109 | sudo vim shell escape, root session opened | TP / Critical | TP / High or Critical | Correct |
| LX-5110 | Hidden 3.0 MB archive in /tmp, created by tar as root | TP / Medium | TP / High | Disposition ok, severity too low |
| LX-5111 | root curl, 3 POSTs of 1 MB to cdn-update-check.net | TP / Critical | TP / Critical or High | Correct |
| LX-5112 | Hidden file deleted at 11:52:20 | TP / Low | TP / Medium (cleanup, part of the chain) | Disposition ok, severity too low |

## 3. Ranking

- **Model:** LX-5111, 5109, 5105, 5102, 5108, 5106, 5107, 5110, 5103, 5104, 5101, 5112.
- **Key:** LX-5111, 5109 and 5110 first (any order), then 5107, then 5112 and 5108, then 5105 and 5106 (grouped), then 5102, then 5101, 5103 and 5104 last.
- The first two are right. Then a failed brute force (5105) and background scanning (5102) rank above the staging (5110) and the new-source logon (5107), and the cleanup (5112) is last.

## 4. Errors to enter in the hallucination/validation log

| AI claim | Source | Result | Error type | Severity |
|---|---|---|---|---|
| LX-5101, 5103 and 5104 are True Positive | LX-5103 and 5104 state the same alert fired on 14 of 14 and 6 of 14 days | Wrong | Ignored the evidence in the alert | Medium |
| LX-5105 (6 failed logons, 0 successes) is Critical and ranked 3rd | LX-5105 | Wrong | Wrong severity | High |
| LX-5110 (hidden archive) is Medium and ranked 8th; LX-5107 (new-source logon) is Medium and ranked 7th; LX-5112 (cleanup) is ranked last | LX-5107, 5110 and 5112 are part of the incident chain | Wrong | Missed the incident chain | High |
| "Evidence weakening or contradicting the alert: None" in all 12 sections | e.g. the 14-day and 6-day history in LX-5103 and 5104 | Wrong | Ignored contradicting evidence | Medium |
| Escalation decision missing in all 12 sections (item 9 gives only a confidence level) | format B, item 9 | Omission | Format not followed | Medium |
| LX-5105 and LX-5106 treated as two separate alerts with no link | same source and time | Poor | Duplicates not grouped | Medium |
| LX-5102 lists "accounts (dev_admin)" as affected | LX-5102 has no account | Wrong | Wrong scope | Low |
| LX-5102: "Block external IP addresses" for 7 single failed attempts | LX-5102 | Over-reaction | Disproportionate action | Low |
| Opening sentence and closing note added | format B says return only the format | Format | Extra text | Low |

## 5. Evaluation Record values

| Criterion | Result |
|---|---|
| Expected output format followed | Partial (12 sections and a ranking, but extra text and no escalation decision) |
| Required findings included | Fail (2 of 8) |
| Evidence references accurate | Pass (alert text quoted faithfully) |
| Unsupported claims | Recount yourself (wrong scope, "None" for contradicting evidence, wrong severities) |
| Scenario treated as evidence incorrectly | No |
| IOC extraction accuracy | n/a |
| Reviewer decision | Revise |
