# 🤖 AI-Assisted SOC Triage Pipeline – Python Automation + SOAR Simulation

![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Wireshark](https://img.shields.io/badge/tshark-1679A7?style=for-the-badge&logo=wireshark&logoColor=white)
![Kali Linux](https://img.shields.io/badge/Kali_Linux-557C94?style=for-the-badge&logo=kalilinux&logoColor=white)
![MITRE ATT&CK](https://img.shields.io/badge/MITRE_ATT%26CK-FF0000?style=for-the-badge)

A Python automation pipeline that simulates a basic SOAR-style alert triage workflow. The script captures live network traffic, detects suspicious IPs based on packet threshold logic, generates a structured JSON alert, and routes it to a published AI agent for automated triage analysis — returning a risk score, MITRE mapping, threat classification, and recommended SOC actions.

This lab demonstrates understanding of alert automation, API integration, structured alert formatting, and AI-assisted SOC investigation workflows.

---

## 🏗️ Lab Architecture

**Attacker machine (Ubuntu)** generates ICMP traffic toward the internal server. The internal server runs the Python script continuously in the background.

```
Attacker Machine (Ubuntu)
        ↓ ICMP traffic (ping 192.168.1.101)
Internal Server (Ubuntu)
        ↓ tshark captures traffic → traffic.pcap
        ↓ converts to CSV → traffic.csv
        ↓ analyzes source IPs → detects threshold breach (>40 packets)
        ↓ generates structured JSON alert → alert.json
        ↓ sends via API to Airia AI agent
Airia AI Agent (Junior SOC Analyst v2.00)
        ↓ applies SOC playbook logic
        ↓ returns structured triage report
```

**Lab architecture diagram:**

<img width="1280" height="720" alt="Lab Architecture" src="https://github.com/user-attachments/assets/467a4b3d-ac63-40db-b033-b5bf2a881cdc" />

<br>

---

## 📂 What Was Built

---

### 🔴 Python Automation Pipeline

Followed and implemented a five-stage Python automation workflow that runs end-to-end from traffic capture to AI triage without manual intervention.

**Stage 1 – Traffic Capture**
Uses tshark to capture ICMP traffic on eth0 targeting the internal server for a defined time window. Saves output as traffic.pcap.

**Stage 2 – PCAP to CSV Conversion**
Converts the packet capture to CSV using tshark field extraction — frame timestamp, source IP, destination IP, protocol, and frame length.

**Stage 3 – IP Analysis**
Reads the CSV, counts packets per source IP using Python Counter, and flags any IP exceeding the packet threshold (set to 40) as suspicious.

**Stage 4 – JSON Alert Generation**
Generates a structured alert JSON with a unique SOC alert ID, threat type, indicator value, destination details, evidence block, and an analyst question for the AI agent.

**Stage 5 – API Submission to Airia**
Posts the JSON alert to the published Airia AI agent API endpoint. Receives and prints the structured triage response.

---

### 🔴 SOC Playbook – AI Agent System Prompt

Wrote a custom SOC playbook used to train the Airia AI agent. The playbook defines the agent's behavior across ten sections:

- Input validation – required JSON fields check
- Threat classification – Brute Force, Scanning, Suspicious Volume, Malware Communication, Benign Noise
- Risk scoring model – 0 to 100 with defined contribution rules (packet count thresholds, ICMP flood indicators, privileged service targets)
- MITRE ATT&CK mapping – tactic and technique ID output
- SOC analyst action plan – Monitor, Enrich, Block IP, Escalate, Isolate
- Escalation logic – automatic escalation recommendation at risk score 80+
- Executive summary – plain language 2-3 sentence business impact statement
- Confidence level – Low / Medium / High based on input completeness
- Strict JSON output format – no markdown, no conversational filler
- Guardrails – no attack instructions, no fabricated intelligence, defensive analysis only

**Published Airia AI agent – Junior SOC Analyst v2.00:**

![1771994211300](https://github.com/user-attachments/assets/62dfdfce-73d3-4250-b15f-5edd422a1e54)

<br>

---

### 🔴 End-to-End Pipeline Working – Live Output

**Attacker machine pinging internal server at 192.168.1.101 across two terminal windows — generating cumulative ICMP traffic that crossed the 40-packet threshold:**

<img width="1456" height="816" alt="Ping traffic generation" src="https://github.com/user-attachments/assets/0c1f84bb-515f-4117-bfc1-2bb6b5cb4648" />

<br>

**Complete pipeline execution – Python script detected 70 packets from 192.168.1.100, generated alert SOC-CC95AA8A, sent to Airia API, received HTTP 200 response with full triage report:**

<img width="1763" height="1080" alt="Screenshot 2026-02-25 093349" src="https://github.com/user-attachments/assets/522493e9-b50e-4e74-9332-fa2261dbcfe2" />

<br>

**AI triage report returned – threat classification: Suspicious Network Volume, risk score: 50 (Medium), escalation: false:**

```json
{
  "alert_id": "SOC-CC95AA8A",
  "threat_classification": "Suspicious Network Volume",
  "risk_score": 50,
  "risk_level": "Medium",
  "confidence_level": "Low",
  "mitre_mapping": "Uncertain based on available evidence",
  "analysis_reasoning": "Input validation failed: missing required fields source_host and protocol. Packet count 70 over 100 seconds yields base risk contributions for packet_count >30 (+20) and >50 (+30), totaling +50.",
  "recommended_actions": [
    "Monitor the activity for repetition or escalation",
    "Request missing fields from data source",
    "Review traffic patterns and logs for related activity"
  ],
  "escalation_required": false,
  "executive_summary": "There is elevated network activity from an internal host toward another internal host, flagged as suspicious. Essential data is missing so we should continue monitoring and collect the missing details."
}
```

- 📌 MITRE: `T1498` Network Denial of Service · `T1046` Network Service Scanning
- Note: Confidence level was Low because the alert JSON was missing source_host
  and protocol fields. In a real SOC environment these fields would need to be
  included for a complete triage assessment.

---

## 📁 Repository Structure

```
AIRIA-AI-SOC-Triage-Lab/
├── new_SOC_ai_capture.py       (main automation script)
├── SOC_playbook.txt            (AI agent system prompt)
├── alert.json                  (sample generated alert output)
└── screenshots/
    ├── lab-architecture.png
    ├── airia-agent-published.png
    ├── pipeline-execution.png
    └── ping-traffic-generation.png
```

---

## 🔎 What This Lab Demonstrates
- Python script comprehension and implementation for security automation
- Structured alert generation following SOC alert format standards
- REST API integration for alert routing
- Custom AI agent training using a structured SOC playbook
- Understanding of SOAR concepts — automated detection, alert enrichment, and triage routing
- Risk scoring model design and threat classification logic

---

⬅️ [Back to SOC Home Lab](https://github.com/rohithbaggu56-dot/Home-SOC-Lab-Detection-Log-Analysis) · [Back to Portfolio](https://github.com/rohithbaggu56-dot)
