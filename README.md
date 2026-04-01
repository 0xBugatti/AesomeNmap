<div align="center">

# 🔍 Awesomenmap

**A Curated Knowledge Base for Nmap Scripts, CVE Intelligence & Automated Reconnaissance Pipelines**

[![Nmap](https://img.shields.io/badge/Nmap-7.x%2B-46A046?logo=nmap&logoColor=white)](https://nmap.org/)
[![NSE](https://img.shields.io/badge/NSE-Scripts-FF6600?style=flat)](https://nmap.org/book/nse.html)
[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Platform](https://img.shields.io/badge/Platform-Linux%20%7C%20macOS%20%7C%20Windows-informational)]()

*A living reference of essential Nmap extensions, vulnerability feeds, and reporting tooling — maintained for security analysts, pentesters, and blue teams.*

</div>

---

## 📑 Table of Contents

- [Overview](#-overview)
- [Repository Structure](#-repository-structure)
- [Core NSE Script Updates](#-core-nse-script-updates)
- [Alternative: Vulscan Integration](#-alternative-vulscan-integration)
- [NSE Script Collections](#-nse-script-collections)
- [CVE Search CLI Tools](#-cve-search-cli-tools)
- [Specialized Scanners — DVRs & SCADA](#-specialized-scanners--dvrs--scada)
- [Dashboard & Task Automation](#-dashboard--task-automation)
- [Reporting & Notifications](#-reporting--notifications)
- [Quick Start](#-quick-start)
- [Workflow](#-workflow)
- [Contributing](#-contributing)
- [License](#-license)

---

## 🎯 Overview

**Awesomenmap** is a knowledge-base repository that centralizes the most valuable Nmap NSE scripts, CVE search utilities, and post-scan reporting pipelines into a single, well-organized reference. Rather than hunting across dozens of GitHub repos and forum posts, security practitioners can use this project as a definitive index and automation hub for all things Nmap.

The repository is intentionally kept lightweight — it contains structured `README` files, configuration templates, and automation wrappers. Large binary or third-party script files are referenced via their upstream sources and pulled in through a simple sync mechanism described below.

Key capabilities this project enables:

| Capability | Description |
|---|---|
| **Script Management** | Pull and update the latest vulnerability, default-account, and version-detection NSE scripts from trusted upstream sources |
| **CVE Intelligence** | Search known CVEs and exploits directly from the command line without leaving your terminal |
| **Automated Scanning** | A dashboard-driven workflow assigns scanning targets, executes Nmap with the curated script set, and produces structured output |
| **Rich Reporting** | Generate HTML scan reports and Graphviz-compatible dependency graphs for every completed scan |
| **Real-time Alerts** | Push critical findings to a Telegram bot for instant visibility |

---

## 📂 Repository Structure

```
Awesomenmap/
│
├── README.md                          # ← You are here
├── LICENSE
├── .gitignore
│
├── scripts/
│   ├── nse/                           # Curated NSE scripts (symlinks or copies)
│   │   ├── vulners.nse                # Vulners.com vulnerability detection
│   │   ├── nndefaccts.nse             # Default account credential checker
│   │   ├── vicarius-nmap/             # Vicarius CVE mapping scripts
│   │   ├── vulscan/                   # Vulscan alternative engine
│   │   ├── log4shell_nse.nse          # Log4Shell (CVE-2021-44228) detector
│   │   ├── ms-exchange-version.nse    # MS Exchange version enumeration
│   │   ├── gitlab-version.nse         # GitLab version enumeration
│   │
│   └── cli/                           # CVE & exploit search tools
│       ├── getsploit/                 # Vulners.com exploit downloader
│       └── search_vulns/              # Multi-source CVE searcher
│
│
└── visualize/
    ├── nmap-dashboard.xsl
    ├── nmap-formatter/
```

---

## 🔧 Core NSE Script Updates

These scripts form the backbone of the vulnerability detection workflow. Keep them up to date by pulling from their official upstream repositories.

### 1. Vulners — Vulnerability Detection

> **Source:** [vulnersCom/nmap-vulners](https://github.com/vulnersCom/nmap-vulners/blob/master/vulners.nse)

The Vulners NSE script queries the [Vulners.com](https://vulners.com/) API to match detected service versions against a comprehensive vulnerability database, returning associated CVEs, CVSS scores, and available exploit references in real time.

```bash
# Clone / update
cd scripts/nse/
git clone https://github.com/vulnersCom/nmap-vulners.git
cp nmap-vulners/vulners.nse ./
```

**Example usage:**

```bash
nmap -sV --script vulners.nse --script-args vulnersdebug=1 <target>
```

---

### 2. Default Accounts — Credential Auditing

> **Source:** [nnposter/nndefaccts](https://github.com/nnposter/nndefaccts)

This script checks network services for default or well-known credentials. It ships with an extensive, regularly updated dictionary of default username/password pairs covering routers, cameras, IoT devices, industrial controllers, and enterprise software. It is invaluable during initial reconnaissance when assessing whether exposed services are using factory-default authentication that could be trivially compromised by an attacker.

```bash
# Clone / update
cd scripts/nse/
git clone https://github.com/nnposter/nndefaccts.git
cp nndefaccts/nndefaccts.nse ./
```

**Example usage:**

```bash
nmap -p 22,23,80,443,8080 --script nndefaccts.nse <target>
```

---

### 3. Vicarius Nmap — CVE Mapping

> **Source:** [VicariusInc/vicarius-nmap](https://github.com/VicariusInc/vicarius-nmap)

Vicarius provides an alternative CVE mapping engine that cross-references detected software versions with their vulnerability database. It complements the Vulners script by offering a second opinion on CVE coverage and can surface discrepancies or additional vulnerabilities that one database might miss. The script is particularly strong for detecting known vulnerabilities in less common or niche software packages that may not receive immediate coverage in larger databases.

```bash
# Clone / update
cd scripts/nse/
git clone https://github.com/VicariusInc/vicarius-nmap.git
cp -r vicarius-nmap/scripts/* ./
```

**Example usage:**

```bash
nmap -sV --script vicarius-cve <target>
```

---

## 🔄 Alternative: Vulscan Integration

> **Source:** [scipag/vulscan](https://github.com/scipag/vulscan?tab=readme-ov-file)

If you prefer a self-contained, offline-capable alternative to the API-dependent Vulners script, **Vulscan** is an excellent choice. It ships with local vulnerability databases and can be extended with custom CSV/JSON feeds. Vulscan performs version-string matching against its bundled databases, making it suitable for air-gapped environments or scenarios where internet access during scanning is restricted or undesirable.

```bash
# Clone / update
cd scripts/nse/
git clone https://github.com/scipag/vulscan.git
```

**Example usage:**

```bash
nmap -sV --script=vulscan/vulscan.nse <target>
```

> **Tip:** You can use Vulners *and* Vulscan together for maximum coverage — Vulscan as the offline baseline and Vulners for real-time API enrichment.

---

## 📦 NSE Script Collections

These community-maintained collections provide additional NSE scripts that expand Nmap's detection capabilities beyond the official script library. Each collection has been vetted for quality and relevance. Periodically pull the latest versions to ensure you have the most current detection signatures and vulnerability checks available.

| Collection | Source | Focus |
|---|---|---|
| **nmap-extra-nse** | [sighook/nmap-extra-nse](https://github.com/sighook/nmap-extra-nse) | General-purpose auxiliary scripts not yet merged into the official Nmap distribution, including custom service probes and brute-force modules |
| **NSE_scripts** | [icarot/NSE_scripts](https://github.com/icarot/NSE_scripts/tree/master) | Vulnerability detection and information-gathering scripts tailored for web application auditing and service enumeration |
| **Custom-Nse** | [ibrahmsql/Custom-Nse](https://github.com/ibrahmsql/Custom-Nse) | Individually crafted NSE scripts targeting specific vulnerabilities, unusual protocols, and emerging threat landscapes |
| **log4shell_nse** | [righel/log4shell_nse](https://github.com/righel/log4shell_nse) | Dedicated detector for Log4Shell (CVE-2021-44228) in Java-based services, performing both header-based and DNS-based verification |
| **ms-exchange-version** | [righel/ms-exchange-version-nse](https://github.com/righel/ms-exchange-version-nse) | Precise Microsoft Exchange Server version fingerprinting, essential for rapidly assessing Patch Tuesday compliance and ProxyShell/ProxyLogon exposure |
| **gitlab-version** | [righel/gitlab-version-nse](https://github.com/righel/gitlab-version-nse) | GitLab instance version detection and associated CVE identification, helping prioritize remediation for self-hosted DevOps infrastructure |
| **Official Nmap Scripts** | [nmap/nmap/scripts](https://github.com/nmap/nmap/tree/master/scripts) | The canonical upstream repository — always ensure your local scripts are at least as recent as this tree |


This script iterates over every collection listed in `automation/nmap_presets.yaml`, clones or updates each repository, and copies compatible `.nse` files into the `scripts/nse/` directory while preserving directory attribution in each file's header comments.

---

## 🔎 CVE Search CLI Tools

These command-line utilities let you search for CVEs and exploits without switching context to a browser. They integrate seamlessly into shell scripts, CI pipelines, and the Awesomenmap automation workflow described later in this document.

### GetSploit — Vulners.com Exploit Finder

> **Source:** [vulnersCom/getsploit](https://github.com/vulnersCom/getsploit)

A lightweight Python-based CLI that queries the Vulners.com database for exploit code, shellcode, and proof-of-concept implementations associated with a given CVE identifier or software version string. It returns direct links to exploit source code along with metadata such as publication date, exploit platform, and reliability rating.

```bash
# Install
pip install getsploit

# Search
getsploit -s "Apache Struts 2"
getsploit -s CVE-2021-44228
```

---

### search_vulns — Multi-Source CVE Search

> **Source:** [ra1nb0rn/search_vulns](https://github.com/ra1nb0rn/search_vulns?tab=readme-ov-file)

This tool aggregates results from multiple vulnerability databases (NVD, ExploitDB, Vulners, and others) into a unified, deduplicated output. It is particularly useful when you need a broad overview of all known vulnerabilities for a given product without manually checking each database separately.

```bash
# Install
git clone https://github.com/ra1nb0rn/search_vulns.git
cd search_vulns && pip install -r requirements.txt

# Search
python search_vulns.py -q "OpenSSH 7.2"
```

## 📊 Dashboard & Task Automation

### Nmap DID WHAT? — Scan Intelligence Dashboard

> **Source:** [hackertarget/nmap-did-what](https://github.com/hackertarget/nmap-did-what)

This dashboard provides a web-based interface for managing, reviewing, and analyzing Nmap scan results at scale. It parses Nmap XML output and presents findings in a structured, searchable format that makes it easy to track scan history, identify recurring vulnerabilities, and prioritize remediation efforts across multiple target networks.

Key features include scan result comparison, trend analysis over time, and role-based access control for team environments. The dashboard supports tagging and categorization of scans, allowing teams to organize their reconnaissance workflow by client, engagement, or network segment.

### Task-Driven Scanning Workflow

The Awesomenmap automation layer bridges the dashboard and the scanning engine:

```
┌─────────────────┐      New Task       ┌──────────────────┐
│   Dashboard     │ ──────────────────▶ │  Task Poller     │
│ (Web Interface) │                     │  (cron job)      │
└─────────────────┘                      └────────┬─────────┘
                                                  │
                                                  ▼
┌─────────────────┐      nmap XML       ┌──────────────────┐
│  Scan Runner    │ ◀────────────────── │  Target List     │
│  (scan_runner)  │ ──────────────────▶ │  + Presets       │
└────────┬────────┘                      └──────────────────┘
         │
         ▼
┌──────────────────────────────────────────────────────┐
│                  Post-Processing Pipeline             │
│                                                       │
│  ┌─────────────┐  ┌───────────┐  ┌────────────────┐  │
│  │ HTML Report │  │  Graphize │  │ Telegram Alert │  │
│  │  Generator  │  │  (GraphViz)│  │   (Bot Push)  │  │
│  └─────────────┘  └───────────┘  └────────────────┘  │
│                                                       │
└──────────────────────────────────────────────────────┘
```

**How it works:**

1. **Task Assignment** — A new scanning task is created in the dashboard (target IPs, port range, scan profile/preset).
2. **Polling** — The `task_poller.sh` cron job checks the dashboard API every `N` minutes for unassigned tasks.
3. **Execution** — When a task is found, `scan_runner.sh` executes the appropriate Nmap command using the selected profile from `nmap_presets.yaml`.
4. **Post-Processing** — The XML output is fed through three parallel handlers:
   - **HTML Report** — A human-readable, styled HTML report with sortable tables and severity breakdown.
   - **Graphize** — [nmap-formatter](https://github.com/vdjagilev/nmap-formatter) converts the scan data into Graphviz DOT format for visual network topology mapping.
   - **Telegram Notification** — A summary of critical findings is pushed to a configured Telegram bot for real-time alerting.

---

## 📄 Reporting & Notifications

### HTML Report Generation

Scan results are transformed into polished, interactive HTML reports that can be shared with stakeholders or archived for compliance purposes. The reports include:

- Executive summary with total hosts, open ports, and severity distribution
- Per-host detailed findings with service banners and detected vulnerabilities
- CVE reference links with CVSS scores and exploit availability indicators
- Sortable and filterable tables for efficient triage

### Graphviz Network Graphs

> **Source:** [vdjagilev/nmap-formatter](https://github.com/vdjagilev/nmap-formatter?tab=readme-ov-file)

Using nmap-formatter, raw Nmap XML output is converted into Graphviz DOT files that can be rendered as network topology diagrams. These visualizations make it immediately clear which hosts are interconnected, which services are exposed, and how vulnerabilities cluster across the target network.

```bash
# Generate graph
python post_process/graphize.py -i scan_output.xml -o topology.dot
dot -Tpng topology.dot -o topology.png
```

### Telegram Bot Notifications


Critical findings are pushed to a Telegram bot in real time, ensuring that security teams are alerted the moment a high-severity vulnerability is discovered. The notification includes the target host, affected service, CVE identifiers, CVSS scores, and direct links to the full report.

**Configuration:**

```yaml
# automation/nmap_presets.yaml
telegram:
  bot_token: "YOUR_BOT_TOKEN"
  chat_id: "YOUR_CHAT_ID"
  severity_threshold: "high"     # Only notify for high/critical
  include_cve_links: true
  report_base_url: "https://your-dashboard.example.com/reports/"
```



---

<div align="center">

**Built with 🔥 by @0xbugatti the security community — for the security community.**

</div>
