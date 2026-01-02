# 🛡️ Intelligent Infrastructure Automation (Zabbix + n8n)

> **Enterprise-grade automation ecosystem for Observability, Self-Healing, and Reporting.**

This repository hosts a collection of advanced workflows designed to orchestrate IT infrastructure operations. By leveraging **n8n** as the central nervous system and **Zabbix** as the sensor array, this project automates incident response, ensures service availability, and provides high-level visibility into system health.

## 🧠 Workflows Breakdown

### 1. 🚑 Intelligent Auto-Healing (Multi-OS)
**File:** `auto Healing (1).json`
A sophisticated remediation pipeline that triggers automatically when a critical alert is detected via Webhook. It replaces manual Level 1 support tasks.

* **Operating System Agnostic:** Automatically routes logic for **Linux** (Debian/Ubuntu) or **Windows Server** based on the host type.
* **Service Dictionary:** Uses a lookup table to map alert names (e.g., "SQL Server", "Nginx") to their actual system service names (e.g., `MSSQLSERVER`, `nginx`).
* **Safety "Judge" Logic:** Before attempting a reboot, a "Judge" node evaluates system uptime and critical flags (e.g., Is it a Hyper-V Host? Are Veeam jobs running?) to prevent unsafe restarts.
* **Disk Cleanup Routine:** If a service fails to start, it checks for disk saturation.
    * *Windows:* Cleans Temp folders, Recycle Bin, and old Spooler files.
    * *Linux:* Vacuums `journalctl` logs and cleans `apt` cache.
* **Verification:** runs post-action checks (`systemctl is-active`, `Get-Service`) to validate success before closing the ticket.

### 2. 📊 Consolidated Infrastructure Report
**File:** `Relatórios Zabbix (1).json`
Generates a "Morning Coffee" report delivered to Microsoft Teams, giving a snapshot of the entire environment status without needing to open dashboards.

* **Zabbix API Integration:** Directly queries `item.get` and `problem.get` methods to fetch raw data.
* **Resource Analysis:** Aggregates CPU, RAM, and Disk usage, applying conditional formatting (Green/Yellow/Red) based on thresholds.
* **Backup Validation:** Specifically monitors Veeam and internal backup tasks, flagging failures immediately.
* **Mobile-First Design:** Uses Adaptive Cards to ensure the report is readable on both Desktop and Mobile devices.

### 3. 🌐 Connectivity & Throughput Monitor
**File:** `Monitoramento Link via CURL.json`
An "Out-of-Band" monitoring workflow that tests connectivity from the perspective of the server itself, bypassing standard agents.

* **Real Throughput Test:** Executes a `curl` download test against Cloudflare's speed test endpoint (HTTPS/443) to measure real-world bandwidth.
* **Performance Metrics:** Calculates Mbps speed and latency (Time Total).
* **Threshold Alerting:** Triggers an alert if download speed drops below 50% of the contracted baseline (defined in code variables).

### 4. 🔥 High-Load Watchdog (Crisis Prevention)
**File:** `WhatDogs Servers.json`
A proactive polling system that checks for dangerous resource exhaustion before it crashes a server.

* **Cluster Checks:** Iterates through a defined list of critical Host IDs.
* **Critical Thresholds:** filters for CPU or Memory usage above **90%**.
* **Crisis Alerting:** Unlike standard alerts, this sends a "CRISIS ALERT" card grouping all affected servers into a single high-priority notification.

### 5. 💓 System Heartbeat (Dead Man's Switch)
**File:** `System - Heartbeat (Watchdog) (1).json`
Monitors the monitor. Ensures the automation platform itself is running.

* **Mechanism:** Sends a recurring HTTP POST to an external health-check service (`hc-ping.com`).
* **Fail-Safe:** If n8n goes down or loses internet access, the external service alerts the team via a secondary channel.

---

## 🛠️ Tech Stack & Methods

* **Orchestrator:** n8n (Self-hosted)
* **Integrations:** Zabbix API (JSON-RPC), Microsoft Teams (Adaptive Cards), SSH
* **Scripting:** PowerShell (CIMInstances, Service Management), Bash (Systemctl, Grep/Awk)
* **Security:**
    * Credential abstraction (No hardcoded passwords).
    * SSH Key-based authentication.
    * Sanitized inputs for external commands.

---
*Maintained by [Matheus Ferrazzi](https://github.com/matheus-ferrazzi)*
