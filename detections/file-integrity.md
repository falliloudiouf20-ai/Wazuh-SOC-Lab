# 📁 File Integrity Monitoring (FIM)

## Overview

File Integrity Monitoring (FIM) is one of the core capabilities of a Security Operations Center (SOC). It allows security analysts to detect unauthorized modifications to critical files, monitor sensitive directories in real time, and investigate potential indicators of compromise.

In this scenario, Wazuh Syscheck was configured to monitor a custom sensitive directory on the Ubuntu endpoint. The objective was to validate that any modification performed on protected files would immediately generate an alert inside the Wazuh SIEM.

This test simulates a common post-compromise activity where an attacker modifies configuration files, web application files, or system resources after gaining access to a Linux server.

---

# Lab Environment

| Component | Value |
|-----------|-------|
| Attacker Machine | Kali Linux |
| Target Machine | Ubuntu 26.04 LTS |
| Monitoring Engine | Wazuh Syscheck |
| Agent | Wazuh Agent 4.12.0 |
| SIEM | Wazuh Manager 4.12.0 |
| Sensitive Directory | `/var/www/sensitive` |

---

# Objective

Validate that Wazuh is capable of:

- Detecting file creation
- Detecting file modification
- Detecting permission changes
- Detecting checksum changes
- Recording file differences
- Mapping events to MITRE ATT&CK

---

# Initial Configuration

By default, Wazuh was monitoring standard Linux directories such as:

- /etc
- /usr/bin
- /usr/sbin
- /bin
- /sbin
- /boot

However, the project required monitoring a custom directory containing sensitive files.

---

# FIM Configuration

The following configuration was added to `ossec.conf` on the Ubuntu endpoint.

```xml
<directories realtime="yes" report_changes="yes">
/var/www/sensitive
</directories>
```

Parameter description:

| Parameter | Description |
|------------|-------------|
| realtime="yes" | Detect changes immediately |
| report_changes="yes" | Store file differences |

The Wazuh Agent was restarted.

```bash
sudo systemctl restart wazuh-agent
```

---

# Creation of the Sensitive Directory

A directory dedicated to FIM testing was created.

```bash
sudo mkdir -p /var/www/sensitive
```

A test configuration file was then created.

```bash
echo "CONFIDENTIAL_DATA=v1.0" | sudo tee /var/www/sensitive/config.php
```

This file represents a critical application configuration file.

---

# Attack Simulation

## Step 1 — File Modification

The file content was modified.

```bash
echo "MODIFICATION_DE_TEST_WAZUH=true" | sudo tee -a /var/www/sensitive/config.php
```

The objective was to simulate unauthorized tampering after a system compromise.

---

## Step 2 — Permission Modification

File permissions were modified.

```bash
sudo chmod 600 /var/www/sensitive/config.php
```

This reproduces an attacker attempting to restrict access to a compromised file.

---

# Wazuh Detection

The modification generated a File Integrity Monitoring alert.

Rule ID

```
550
```

Description

```
Integrity checksum changed
```

Severity

```
Level 7
```

Monitored file

```
/var/www/sensitive/config.php
```

Monitoring mode

```
Realtime
```

---

# Evidence Collected

Wazuh recorded:

- MD5 checksum
- SHA1 checksum
- SHA256 checksum
- File size
- Owner
- Group
- Permissions
- Modification date
- File differences

Because `report_changes="yes"` was enabled, Wazuh also stored the modified content.

Observed difference:

```text
MODIFICATION_DE_TEST_WAZUH=true
```

This provides valuable forensic information during an incident investigation.

---

# Permission Change Detection

After executing:

```bash
sudo chmod 600 /var/www/sensitive/config.php
```

Wazuh detected:

- Permission modification
- Metadata update
- New checksum
- File attribute change

This demonstrates that Wazuh monitors both file contents and metadata.

---

# Timeline

```
Directory created
        │
        ▼
Sensitive file created
        │
        ▼
Realtime monitoring enabled
        │
        ▼
File modified
        │
        ▼
Checksum changed
        │
        ▼
Rule 550 generated
        │
        ▼
File diff stored
        │
        ▼
Permissions modified
        │
        ▼
Metadata updated
```

---

# Security Analysis

File Integrity Monitoring is essential because attackers frequently modify files after compromising a system.

Examples include:

- Web shells
- Configuration files
- Startup scripts
- Cron jobs
- SSH configuration
- Backdoor installation

Without FIM, these modifications may remain undetected for long periods.

In this laboratory, Wazuh immediately detected every modification performed on the monitored file.

---

# MITRE ATT&CK Mapping

| Technique | Description |
|------------|-------------|
| T1565.001 | Stored Data Manipulation |

---

# Investigation Summary

Observed indicators:

- New checksum
- Modified content
- Permission changes
- File metadata changes
- Real-time detection

Evidence collected by Wazuh:

- File path
- Previous state
- New state
- Timestamp
- Agent ID
- Hostname
- Rule ID
- Alert level

---

# Skills Demonstrated

- Linux Administration
- File Integrity Monitoring
- Wazuh Syscheck
- Real-Time Monitoring
- Log Analysis
- Security Investigation
- Threat Detection
- Incident Response
- Linux Permissions
- MITRE ATT&CK Mapping

---

# Evidence

Screenshots associated with this scenario:

```
screenshots/
│
├── file-integrity-alert.png
├── mitre-attck.png
└── agent-connected.png
```

---

# Lessons Learned

During this scenario, several important concepts were validated:

- Real-time monitoring significantly reduces detection time.
- Monitoring only system directories is insufficient; custom application directories must also be protected.
- The `report_changes` option provides valuable forensic evidence by recording file differences.
- File permission changes are as important as content modifications because attackers often alter access rights to hide their actions.
- Wazuh provides comprehensive visibility into file system changes, making it an effective host-based intrusion detection component.

---

# Conclusion

This File Integrity Monitoring scenario successfully demonstrated the ability of Wazuh Syscheck to detect unauthorized changes to sensitive files in real time.

The platform identified checksum modifications, file content changes, permission updates, and metadata alterations while preserving detailed forensic evidence for investigation.

This experiment highlights the importance of FIM as a critical layer of defense within a modern SOC, enabling analysts to rapidly detect post-compromise activity and respond before attackers can establish persistence or further manipulate critical system resources.
