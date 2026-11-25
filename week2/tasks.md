# Week 2 — Nader Task Tracker

**Owner:** Nader — Network Operator  
**Branch for upload:** `Nader`  
**Project:** AI-Assisted SOC Automation & Full Incident Response Capstone

| Task | Status | Deliverable / note |
|---|---|---|
| Review Nader firewall, Suricata, and Zeek evidence notes | Complete | `week2/W2_nader_network-analysis_v1.md` |
| Prepare network timeline and findings | Complete | `week2/W2_nader_network-analysis_v1.md` |
| Prepare observed IOC and scenario-only IOC context | Complete | `week2/W2_nader_network-analysis_v1.md` |
| Prepare detection improvement proposals | Complete — proposed only | Not deployed or tested |
| Preserve the actual local Llama request and prompt | Complete | `ai-requests/20261125-w2-nader-network-log-analysis.md` |
| Run local Llama 3.1 8B | Complete | `llama3.1:8b`; Ollama `0.40.18`; team-wide approval not verified |
| Preserve verbatim AI response and execution metadata | Complete | `ai-outputs/20261125-w2-nader-network-log-analysis.md` and metadata file |
| Perform preliminary technical QA | Complete — Revise | `week2/W2_nader_llm-evaluation_v1.md` |
| Prepare claim-level validation / hallucination log | Complete — preliminary | `week2/W2_nader_hallucination-log_v1.csv`; Mina remains owner of the master log |
| Adam cross-review | Pending | Required by the team cross-review circle |
| Nada second validation | Pending | Required by the Week 2 work plan |
| Confirm team-wide Ollama version | Pending | Local run used 0.40.18; team-wide approval not verified |

## Important status rules

- The raw model response is preserved verbatim. Do not overwrite or silently correct it.
- The human-prepared network analysis and the AI-generated response are separate artifacts.
- Detection ideas are proposals only; they have not been deployed or tested.
- No containment, blocking, isolation, password reset, or recovery action is claimed as completed.
- The archived request record preserves the prompt used for Nader's run. It is not represented as a Nada-authored queue item.
- Do not mark Adam's or Nada's review complete until those people have actually reviewed the deliverables.
