# NOVA: Network Observation & Vigilance Archive

A comprehensive threat detection framework for identifying Advanced Persistent Threats (APTs) in enterprise environments using the ELK Stack with custom Event Query Language (EQL) rules aligned to the MITRE ATT&CK framework.

## Overview

Advanced Persistent Threats (APTs) are sophisticated, long-term cyberattacks that target specific organizations using advanced techniques to remain undetected. This project implements a behavior-based detection mechanism capable of identifying malicious Tactics, Techniques, and Procedures (TTPs) across different stages of the APT attack lifecycle.

**Key Components:**
- Sysmon & Winlogbeat for endpoint telemetry collection
- Logstash for log parsing and normalization
- Elasticsearch for log storage and real-time analysis
- Kibana for visualization and alerting with EQL queries
- Atomic Red Team for APT behavior simulation

## Architecture

<div align="center">
  <img height="600" alt="Group 100" src="https://github.com/user-attachments/assets/39d48f4e-851a-4299-aafa-876b86829fa1" />
</div>

## Tech Stack

| Component | Technology |
|-----------|-----------|
| Virtualization | VirtualBox |
| Log Collection | Sysmon, Winlogbeat |
| Containerization | Docker, Docker Compose |
| APT Simulation | Atomic Red Team |
| Log Processing | Logstash |
| Storage & Search | Elasticsearch |
| Visualization & Alerting | Kibana with EQL queries |
| Framework Alignment | MITRE ATT&CK |

## Prerequisites

- Windows 10/11 or Windows Server (for endpoints)
- VirtualBox 6.0+
- Docker Engine 20.0+ and Docker Compose
- Minimum 8GB RAM (16GB recommended)
- Minimum 50GB free disk space
- Sysmon v14+, Winlogbeat 8.x, Elasticsearch 8.x, Logstash 8.x, Kibana 8.x

## Installation

### Clone the Repository

```bash
git clone https://github.com/luv29/NOVA.git
cd NOVA
```

### Deploy ELK Stack

```bash
docker-compose up -d
docker-compose ps
```

### Install Sysmon on Windows Endpoint

```powershell
Invoke-WebRequest -Uri "https://download.sysinternals.com/files/Sysmon.zip" -OutFile "Sysmon.zip"
Expand-Archive Sysmon.zip
.\Sysmon64.exe -accepteula -i sysmonconfig.xml
```

### Configure Winlogbeat

```powershell
.\winlogbeat.exe setup
.\winlogbeat.exe -e
```

### Install Atomic Red Team

```powershell
IEX (IWR 'https://raw.githubusercontent.com/redcanaryco/invoke-atomicredteam/master/install-atomicredteam.ps1' -UseBasicParsing);
Install-AtomicRedTeam -getAtomics
```

## Usage

### Running APT Simulations

```powershell
Import-Module "C:\AtomicRedTeam\invoke-atomicredteam\Invoke-AtomicRedTeam.psd1"

# Test credential dumping (T1003)
Invoke-AtomicTest T1003.001

# Test PowerShell execution (T1059.001)
Invoke-AtomicTest T1059.001

# Test data exfiltration (T1041)
Invoke-AtomicTest T1041
```

### Configuring Detection Rules

1. Navigate to Kibana → Security → Rules
2. Import rule from `/rules` directory or create new EQL rule
3. Configure alert actions and schedules
4. Enable the rule

### Example EQL Query

```eql
process where event.type == "start" and
  process.name : ("mimikatz.exe", "procdump.exe", "comsvcs.dll") or
  process.command_line : ("*lsass*", "*sekurlsa*", "*logonpasswords*")
```

## Methodology

**1. Log Collection and Forwarding**
- Capture endpoint telemetry using Sysmon
- Forward logs to Logstash via Winlogbeat
- Monitor process creation, network connections, file operations

**2. Log Ingestion and Preparation**
- Parse and normalize logs through Logstash pipeline
- Enrich data with contextual information
- Store in Elasticsearch for indexing and search

**3. Threat Rule Triggering**
- Continuous monitoring with EQL queries
- Real-time correlation of events across attack stages
- Automated alert generation for detected threats

**4. Analysis and Response**
- Visualize detections in Kibana dashboards
- Investigate alerts with drill-down capabilities
- Execute incident response procedures

## Project Structure

```
APT-Detection-Rules/
│
├── docker-compose.yml           # ELK Stack deployment configuration
├── kibana.conf                  # Kibana configuration file
├── logstash.conf                # Logstash main configuration
│
├── logstash-pipeline/
│   └── winlogbeat.conf         # Winlogbeat pipeline configuration
│
└── rules/                       # MITRE ATT&CK detection rules
    ├── T1003/                   # Credential Dumping
    │   └── rule.ndjson         # Detection rule in NDJSON format
    ├── T1041/                   # Exfiltration Over C2 Channel
    │   └── rule.ndjson         # Detection rule in NDJSON format
    └── T1059/                   # Command and Scripting Interpreter
        └── rule.ndjson         # Detection rule in NDJSON format
```

## Results

Successfully developed and validated threat detection rules for T1003 (Credential Dumping), T1059 (PowerShell execution abuse), and T1041 (Data exfiltration over C2 channels). The system provides high-fidelity alerts with minimized false positives and real-time detection capabilities.

## License

This project is licensed under the MIT License.

## Acknowledgments

- MITRE Corporation for the ATT&CK Framework
- Red Canary for Atomic Red Team
- Elastic for the ELK Stack
- Microsoft for Sysmon
