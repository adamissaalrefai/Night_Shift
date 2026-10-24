# IR Capstone: Unified Attack Scenario (Weeks 1–4)

* **Document Version:** v1.0

* **Date:** 2026-10-20

* **Timezone & Format Standard:** UTC / ISO 8601 (`YYYY-MM-DDTHH:MM:SSZ`)

* **Project:** Project 5 — AI-Assisted SOC Automation & Full Incident Response Capstone

## 1. Fictional Company, Hosts, Users & IP Addresses

* **Company Name:** NileTech Financial Services (`niletech.local`)

* **Industry:** FinTech / Financial Services

* **Simulated Incident Window:** `2026-11-10T08:00:00Z` to `2026-11-10T16:00:00Z`

### Network & Asset Inventory

| Asset Role | Hostname | IP Address | OS / Platform | Key Users / Accounts | Log Owner | 
| ----- | ----- | ----- | ----- | ----- | ----- | 
| **Finance Workstation** | `WS-FIN-01` | `10.10.10.15` | Windows 10/11 Pro | `sara.ahmed` | **Mahmoud** (Windows/Endpoint) | 
| **Domain Controller** | `DC-NILE-01` | `10.10.20.5` | Windows Server 2022 | `Administrator`, `svc_backup` | **Mahmoud** (Windows/Endpoint) | 
| **Web & App Server** | `SRV-WEB-01` | `10.10.20.10` | Ubuntu Server 22.04 | `www-data`, `dev_admin`, `root` | **Adam** (Linux/Web) | 
| **Network Gateway / Sensor** | `FW-NILE-GW` | `10.10.10.1` | pfSense / Zeek / Suricata | `net_admin` | **Nader** (Network) | 
| **Attacker Phishing & C2** | `mail.niletech-support.com` | `198.51.100.45` | External Attacker Host | N/A (External Threat Actor) | **Nader** & **Mahmoud** | 
| **Attacker Scan & Exfil** | `cdn-update-check.net` | `203.0.113.88` | External Attacker Host | N/A (External Threat Actor) | **Nader** & **Adam** | 

### Known Malicious Indicators (IOCs) for Enrichment & Triage

* **Phishing Attachment Name:** `Invoice_Q4_Review.hta`

* **Simulated Malware SHA-256:** `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` *(to be replaced with actual test hash or public known-malicious hash for VirusTotal enrichment in Week 3)*

* **Staging Archive Path:** `/tmp/.db_dump.tar.gz`

## 2. The Attack Chain (End-to-End Kill Chain)

This scenario spans endpoint, web/Linux, and network stages to fulfill all requirements across Weeks 1–4 (including Week 3 SOAR workflows: Phishing, Malware, Brute Force, and Data Exfiltration).

### Stage 1: Initial Access & Malware Execution (Phishing -> Windows Endpoint)

1. **Phishing Delivery (`T1566.001`):** Finance employee `sara.ahmed` on `WS-FIN-01` (`10.10.10.15`) receives a spear-phishing email originating from `198.51.100.45` (`mail.niletech-support.com`) containing a malicious attachment named `Invoice_Q4_Review.hta`.

2. **Malicious Execution (`T1059.001`):** Opening the attachment spawns `mshta.exe`, which executes an obfuscated/encoded `PowerShell` command that reaches out to `198.51.100.45` over HTTP/HTTPS to download a secondary payload (`updater.ps1`).

3. **Persistence (`T1053.005`):** The script creates a Windows Scheduled Task named `NileTechUpdater` to maintain persistence on `WS-FIN-01`.

### Stage 2: Credential Theft & Brute Force (Windows & Linux/Web)

1. **Credential Dumping (`T1003.001`):** On `WS-FIN-01`, the attacker accesses `lsass.exe` memory to dump credentials and discovers cached credentials/SSH keys belonging to the shared administrator account `dev_admin`.

2. **Web Scanning & Brute Force (`T1595` & `T1110.001`):** Concurrently, from external IP `203.0.113.88`, the attacker runs an automated web vulnerability scan against `SRV-WEB-01` (`10.10.20.10`) followed by a high-volume SSH brute-force attack against port `22`.

### Stage 3: Lateral Movement & Privilege Escalation (Windows -> Linux & DC)

1. **SMB Probing (`T1021.002`):** From `WS-FIN-01` (`10.10.10.15`), the attacker attempts lateral movement to the Domain Controller `DC-NILE-01` (`10.10.20.5`) using failed and successful SMB logons.

2. **SSH Lateral Movement (`T1021.004`):** Using the stolen `dev_admin` credentials from `WS-FIN-01`, the attacker successfully logs into `SRV-WEB-01` (`10.10.20.10`) via SSH.

3. **Privilege Escalation (`T1548.003`):** On `SRV-WEB-01`, the attacker exploits misconfigured `sudo` permissions (`sudo -l` / GTFOBins) to escalate privileges from `dev_admin` to `root`.

### Stage 4: Collection & Data Exfiltration (Linux -> External Network)

1. **Data Staging (`T1560.001`):** Operating as `root` on `SRV-WEB-01`, the attacker dumps sensitive customer financial records and compresses them into `/tmp/.db_dump.tar.gz`.

2. **Exfiltration Over Web/C2 (`T1041` & `T1048`):** The archive is exfiltrated from `SRV-WEB-01` (`10.10.20.10`) to the external host `203.0.113.88` (`cdn-update-check.net`) via chunked `HTTP POST` requests and anomalous `DNS` queries.

## 3. Lab Environment & Attack Generation Tools

* **Lab Approach:** Hybrid environment combining local Virtual Machines (Windows 10/Server VM + Ubuntu Server VM) with normalized public/synthetic log datasets rewritten to match the exact hostnames, usernames, IPs, and UTC timestamps above.

* Attack Simulation **Tools:**

  * **Windows (`Mahmoud`):** `Atomic Red Team` (`Invoke-AtomicTest` for `T1059.001`, `T1053.005`, `T1003.001`) and native PowerShell commands.

  * **Linux & Web (`Adam`):** `Hydra` (for SSH brute-force logs), `Nikto` / `curl` (for web server access/error logs), and standard Linux utilities (`sudo`, `tar`, `curl`) for escalation and staging.

  * **Network (`Nader`):** `Zeek` and `Suricata` inspecting PCAP traffic generated between the VMs, supplemented by `curl` and `dig` to simulate C2 beacons and data exfiltration.

## 4