# 🛡️ Wazuh SOC Lab — Building a Security Operations Center from Scratch

<p align="center">

![Platform](https://img.shields.io/badge/Platform-Wazuh%204.12-blue?style=for-the-badge)
![OS](https://img.shields.io/badge/Linux-Kali%20%26%20Ubuntu-red?style=for-the-badge)
![SIEM](https://img.shields.io/badge/SIEM-Wazuh-success?style=for-the-badge)
![SOC](https://img.shields.io/badge/SOC-Blue%20Team-informational?style=for-the-badge)
![MITRE](https://img.shields.io/badge/MITRE-ATT%26CK-orange?style=for-the-badge)
![Status](https://img.shields.io/badge/Project-Completed-brightgreen?style=for-the-badge)
![License](https://img.shields.io/badge/License-MIT-black?style=for-the-badge)

</p>

---

# 📖 Project Overview

**Wazuh SOC Lab** is a complete hands-on Security Operations Center (SOC) laboratory built from scratch using **Wazuh SIEM**, **Kali Linux**, and **Ubuntu Linux**.

Unlike projects based on pre-recorded datasets or simulated logs, every alert presented in this repository was generated from **real attack simulations** performed against a monitored Linux endpoint.

The objective was to understand how a Security Operations Center operates in practice by deploying a SIEM platform, collecting endpoint telemetry, detecting malicious activities, investigating alerts, and producing professional incident response documentation.

This repository demonstrates practical skills in:

- SIEM deployment
- Endpoint monitoring
- Linux administration
- SSH monitoring
- File Integrity Monitoring (FIM)
- Privilege escalation detection
- Event correlation
- Incident investigation
- MITRE ATT&CK mapping
- Security reporting

---

# 🎯 Project Goals

The primary objective of this project was to build a fully functional SOC environment capable of detecting common attack techniques on Linux systems.

The laboratory was designed to validate the following capabilities:

- Deploy Wazuh from scratch
- Configure Linux endpoint monitoring
- Collect authentication logs
- Detect SSH attacks
- Monitor sensitive files
- Detect administrative activity
- Correlate security events
- Produce professional incident reports
- Understand the SOC investigation workflow

---

# 🏗️ Laboratory Architecture

```
                         INTERNET
                              │
                              │
                    ───────────────────
                              │
                     VirtualBox Network
                              │
        ┌─────────────────────┴─────────────────────┐
        │                                           │
        │                                           │
┌─────────────────────┐                 ┌─────────────────────────┐
│     Kali Linux      │                 │     Ubuntu 26.04 LTS    │
│─────────────────────│                 │─────────────────────────│
│ Wazuh Manager       │◄──────────────►│ Wazuh Agent             │
│ Wazuh Dashboard     │                 │ OpenSSH Server          │
│ Filebeat            │                 │ Syscheck (FIM)          │
│ Wazuh Indexer       │                 │ Authentication Logs     │
│ SOC Analyst         │                 │ Monitored Endpoint      │
└─────────────────────┘                 └─────────────────────────┘
```

---

# 🖥️ Laboratory Environment

| Component | Description |
|------------|-------------|
| SIEM | Wazuh 4.12.0 |
| Manager OS | Kali Linux |
| Endpoint OS | Ubuntu 26.04 LTS |
| Endpoint Agent | Wazuh Agent |
| Dashboard | Wazuh Dashboard |
| Network | VirtualBox Host-Only |
| Monitoring | Real-Time |
| Log Collection | Authentication + File Integrity |
| Framework | MITRE ATT&CK |

---

# 🛠 Technologies Used

| Technology | Purpose |
|------------|---------|
| Wazuh | Security Information and Event Management (SIEM) |
| Kali Linux | Wazuh Manager & SOC Workstation |
| Ubuntu Linux | Monitored Endpoint |
| OpenSSH | Remote Access Monitoring |
| Syscheck | File Integrity Monitoring |
| PAM | Authentication Monitoring |
| VirtualBox | Virtual Infrastructure |
| Git | Version Control |
| GitHub | Project Hosting |
| MITRE ATT&CK | Threat Classification |

---

# 🎯 Learning Objectives

Throughout this project, the following practical objectives were achieved:

- Deploy a complete SIEM infrastructure.
- Configure a monitored Linux endpoint.
- Collect security telemetry.
- Detect authentication failures.
- Detect successful SSH logins.
- Detect privilege escalation.
- Monitor critical files in real time.
- Investigate security incidents.
- Correlate security events.
- Produce professional SOC documentation.

---

# 🔍 Detection Scenarios

Several realistic attack scenarios were reproduced during this laboratory.

| Scenario | Status |
|-----------|--------|
| SSH Authentication Monitoring | ✅ |
| Failed SSH Login Detection | ✅ |
| Successful SSH Login Detection | ✅ |
| File Integrity Monitoring | ✅ |
| User Account Creation | ✅ |
| Privilege Escalation | ✅ |
| Password Modification | ✅ |
| sudo Activity Monitoring | ✅ |
| Event Correlation | ✅ |

Each scenario generated genuine alerts collected and analyzed by Wazuh.

---

# 📂 Repository Structure

```text
Wazuh-SOC-Lab/
│
├── README.md
│
├── architecture/
│   └── architecture.md
│
├── setup/
│
├── detections/
│   ├── ssh-bruteforce.md
│   ├── file-integrity.md
│   ├── privilege-escalation.md
│   └── suspicious-activity.md
│
├── incident-response/
│   ├── incident-01-ssh.md
│   ├── incident-02-file-modification.md
│   └── incident-03-privilege-event.md
│
├── reports/
│   ├── security-findings.md
│   └── final-report.md
│
└── screenshots/
```

---

# 🚀 What This Project Demonstrates

This project reproduces the workflow followed by a Security Operations Center when monitoring Linux endpoints.

The complete process includes:

```
Infrastructure Deployment
          │
          ▼
Agent Enrollment
          │
          ▼
Log Collection
          │
          ▼
Threat Detection
          │
          ▼
Alert Analysis
          │
          ▼
Incident Investigation
          │
          ▼
MITRE Mapping
          │
          ▼
Security Reporting
```

Unlike simple demonstrations, every stage of this workflow was executed in a real environment.

---

# ⭐ Why This Project Matters

Modern organizations generate thousands of security events every day.

Without a SIEM solution, identifying malicious activity becomes extremely difficult.

This laboratory demonstrates how Wazuh helps analysts:

- Centralize logs
- Detect attacks
- Monitor endpoints
- Correlate events
- Investigate incidents
- Improve visibility
- Reduce response time

These capabilities form the foundation of a modern Blue Team and Security Operations Center.

---

# 📌 Next Sections

The remainder of this README covers:

- Attack scenarios
- MITRE ATT&CK mapping
- Incident response investigations
- Security findings
- Screenshots
- Skills demonstrated
- Lessons learned
- Future improvements
- Author information
- Final conclusions

---

---

# ⚔️ Detection Scenarios

One of the main objectives of this laboratory was to validate Wazuh's ability to detect common attack techniques targeting Linux systems.

Instead of importing external datasets, every security event documented in this repository was generated through hands-on attack simulations performed against the monitored Ubuntu endpoint.

Each scenario followed the same methodology:

1. Configure monitoring
2. Execute the attack simulation
3. Observe the generated logs
4. Analyze Wazuh alerts
5. Investigate the incident
6. Map alerts to the MITRE ATT&CK framework

This approach reproduces the workflow followed by a SOC analyst when investigating suspicious activity.

---

# 🔐 Scenario 1 — SSH Authentication Monitoring

## Description

Secure Shell (SSH) is one of the most frequently targeted remote administration services.

Attackers commonly attempt to:

- Guess passwords
- Reuse stolen credentials
- Perform brute-force attacks
- Access remote systems

The objective of this scenario was to verify that Wazuh detects authentication events generated by the SSH service.

---

## Activities Performed

- Installed OpenSSH Server
- Enabled SSH service
- Verified port 22 availability
- Configured authentication log monitoring
- Performed failed authentication
- Performed successful authentication
- Investigated generated alerts

---

## Detection Results

| Detection | Status |
|-----------|--------|
| Failed Login | ✅ |
| Successful Login | ✅ |
| PAM Authentication | ✅ |
| Session Creation | ✅ |

---

## Wazuh Rules Observed

| Rule ID | Description |
|----------|-------------|
| 5503 | PAM Login Failed |
| 5501 | PAM Session Opened |
| 5715 | SSH Authentication Success |
| 5760 | SSH Authentication Failure |

---

## MITRE ATT&CK

| Technique | Description |
|------------|-------------|
| T1110.001 | Password Guessing |
| T1021 | Remote Services |
| T1078 | Valid Accounts |

---

## Security Value

SSH monitoring allows security analysts to identify:

- Unauthorized remote access
- Password guessing
- Credential abuse
- Suspicious administrator activity

---

# 📁 Scenario 2 — File Integrity Monitoring (FIM)

## Description

File Integrity Monitoring is a critical defensive capability that continuously monitors protected files for unauthorized modifications.

The objective of this scenario was to validate Wazuh Syscheck by monitoring a custom sensitive directory.

---

## Monitored Directory

```text
/var/www/sensitive
```

---

## Activities Performed

- Enabled Syscheck realtime monitoring
- Enabled report_changes
- Created a sensitive configuration file
- Modified file contents
- Changed file permissions
- Investigated generated alerts

---

## Detection Results

| Detection | Status |
|-----------|--------|
| File Creation | ✅ |
| File Modification | ✅ |
| Checksum Change | ✅ |
| Permission Change | ✅ |
| Metadata Update | ✅ |

---

## Wazuh Rule

| Rule ID | Description |
|----------|-------------|
| 550 | Integrity Checksum Changed |

---

## MITRE ATT&CK

| Technique | Description |
|------------|-------------|
| T1565.001 | Stored Data Manipulation |

---

## Security Value

File Integrity Monitoring enables SOC analysts to detect:

- Malware installation
- Web shell deployment
- Configuration tampering
- Unauthorized modifications
- Persistence mechanisms

---

# 👑 Scenario 3 — Privilege Escalation Detection

## Description

After gaining initial access, attackers frequently attempt to obtain administrative privileges.

This scenario reproduced the complete privilege escalation workflow using native Linux administration commands.

---

## Activities Performed

- Created a new user
- Added the account to the sudo group
- Assigned a password
- Executed privileged commands
- Logged in remotely using the new account

---

## Detection Results

| Detection | Status |
|-----------|--------|
| User Creation | ✅ |
| Group Modification | ✅ |
| Password Change | ✅ |
| sudo Execution | ✅ |
| SSH Authentication | ✅ |

---

## Wazuh Rules

| Rule ID | Description |
|----------|-------------|
| 5902 | User Created |
| 5901 | Group Modified |
| 5555 | Password Changed |
| 5402 | Successful sudo Execution |
| 5715 | SSH Authentication Success |

---

## MITRE ATT&CK

| Technique | Description |
|------------|-------------|
| T1136 | Create Account |
| T1548.003 | Sudo and Sudo Caching |
| T1078 | Valid Accounts |
| T1021 | Remote Services |

---

## Security Value

Monitoring privileged activity allows organizations to quickly identify:

- Unauthorized administrator creation
- Privilege abuse
- Persistence
- Insider threats
- Compromised administrator accounts

---

# 🚨 Scenario 4 — Suspicious Activity Correlation

## Description

A single alert rarely confirms a cyber attack.

Security analysts instead correlate multiple events to understand the complete attack lifecycle.

This final scenario combined every previous activity into a realistic investigation.

---

## Correlated Events

```
Failed SSH Login
        │
        ▼
Successful Authentication
        │
        ▼
User Creation
        │
        ▼
Privilege Escalation
        │
        ▼
Password Change
        │
        ▼
sudo Activity
        │
        ▼
Sensitive File Modification
```

---

## Detection Results

| Activity | Status |
|-----------|--------|
| Authentication Monitoring | ✅ |
| File Monitoring | ✅ |
| Privileged Activity | ✅ |
| Event Correlation | ✅ |

---

## SOC Investigation Outcome

The generated alerts clearly demonstrated a complete attack chain.

Although intentionally executed within a laboratory environment, the resulting telemetry closely resembles attacker behavior observed in production environments.

---

# 🎯 MITRE ATT&CK Coverage

Throughout the project, Wazuh automatically mapped detected events to the MITRE ATT&CK framework.

| Technique | Name | Laboratory Scenario |
|------------|------|---------------------|
| T1110.001 | Password Guessing | Failed SSH Login |
| T1078 | Valid Accounts | Successful Authentication |
| T1021 | Remote Services | SSH Access |
| T1136 | Create Account | User Creation |
| T1548.003 | Sudo and Sudo Caching | Privilege Escalation |
| T1565.001 | Stored Data Manipulation | File Integrity Monitoring |

---

# 📊 Detection Summary

| Security Capability | Validated |
|----------------------|-----------|
| SSH Monitoring | ✅ |
| Authentication Monitoring | ✅ |
| PAM Monitoring | ✅ |
| File Integrity Monitoring | ✅ |
| Privileged Command Detection | ✅ |
| User Creation Detection | ✅ |
| Group Modification Detection | ✅ |
| Password Change Detection | ✅ |
| Event Correlation | ✅ |
| MITRE ATT&CK Mapping | ✅ |

---

# 🚑 Incident Response Reports

Every major detection scenario was documented as a professional incident response report.

| Report | Description |
|---------|-------------|
| **IR-001** | SSH Authentication Investigation |
| **IR-002** | File Integrity Investigation |
| **IR-003** | Privilege Escalation Investigation |

Each report contains:

- Executive Summary
- Detection Timeline
- Indicators of Compromise (IOCs)
- Root Cause Analysis
- Impact Assessment
- Containment Actions
- Recovery Strategy
- Lessons Learned
- Recommendations

These reports reproduce the documentation standards commonly used within enterprise Security Operations Centers.

---

# 📈 Security Findings Overview

Across all attack simulations, Wazuh successfully detected every expected security event.

| Category | Result |
|-----------|--------|
| Authentication Security | ✅ Successful |
| Endpoint Monitoring | ✅ Successful |
| File Integrity | ✅ Successful |
| Privilege Monitoring | ✅ Successful |
| Threat Detection | ✅ Successful |
| Event Correlation | ✅ Successful |
| Incident Investigation | ✅ Successful |

The laboratory achieved **100% detection** for every scenario intentionally executed during testing.

---

---

# 📸 Screenshots

The following screenshots were captured during the laboratory and provide visual evidence of the deployment, monitoring, and investigation process.

```text
screenshots/
│
├── dashboard-overview.png
├── agent-connected.png
├── ssh-alert.png
├── file-integrity-alert.png
├── privilege-alert.png
├── mitre-attck.png
└── architecture.png
```

## Dashboard

Displays the Wazuh Dashboard with the connected Ubuntu endpoint, active alerts, and overall security monitoring status.

---

## SSH Authentication Alerts

Illustrates both failed and successful SSH authentication events generated during the attack simulation.

---

## File Integrity Monitoring

Shows the File Integrity Monitoring (Syscheck) alerts generated after modifying a protected configuration file.

---

## Privilege Escalation

Presents alerts related to:

- User creation
- Group modification
- Password changes
- sudo execution
- Administrative activity

---

## MITRE ATT&CK Mapping

Shows how Wazuh automatically associates alerts with standardized MITRE ATT&CK techniques, helping analysts classify attacker behavior.

---

# 🧠 Skills Demonstrated

This laboratory demonstrates practical experience across several cybersecurity domains.

---

## Security Operations Center (SOC)

- SIEM deployment
- Alert triage
- Event investigation
- Threat detection
- Incident response
- Security reporting
- Event correlation

---

## Linux Administration

- User management
- Service management
- SSH configuration
- System monitoring
- Permission management
- Log analysis
- Package installation

---

## Wazuh SIEM

- Wazuh Manager deployment
- Wazuh Agent configuration
- Dashboard usage
- Rule interpretation
- Alert investigation
- Syscheck configuration
- Authentication monitoring

---

## Threat Detection

- Authentication attacks
- Password guessing
- Privilege escalation
- Administrative activity
- File Integrity Monitoring
- Endpoint monitoring
- Suspicious activity analysis

---

## Security Frameworks

- MITRE ATT&CK
- Incident Response lifecycle
- Defense in Depth
- Least Privilege Principle
- Blue Team methodologies

---

## Documentation

- Architecture documentation
- Detection reports
- Incident reports
- Technical documentation
- Security findings
- Executive reporting

---

# 📚 Lessons Learned

Building this laboratory provided valuable practical experience beyond simply installing a SIEM platform.

Several important concepts became clear throughout the project.

---

## SIEMs Require Proper Configuration

Installing a SIEM alone is not sufficient.

Security value comes from:

- Collecting relevant logs
- Configuring monitored directories
- Creating useful detection rules
- Investigating alerts

---

## Event Correlation Is Essential

A single alert rarely indicates an attack.

Real investigations require analysts to correlate multiple events before drawing conclusions.

For example:

```
Failed Authentication
        │
        ▼
Successful Login
        │
        ▼
New Administrator
        │
        ▼
Sensitive File Modified
```

This sequence provides much stronger evidence than any isolated event.

---

## Visibility Improves Security

By centralizing endpoint telemetry inside Wazuh, security analysts gain visibility into:

- Authentication activity
- User management
- Administrative commands
- File modifications
- Privilege escalation

Without centralized monitoring, many of these events could remain unnoticed.

---

## Documentation Matters

A well-documented investigation is just as important as technical detection.

Incident reports should clearly explain:

- What happened
- When it happened
- Why it matters
- How it was detected
- What actions should be taken

This project emphasizes both technical implementation and professional documentation.

---

# 🚀 Future Improvements

Although the laboratory successfully achieved its objectives, several enhancements could extend its capabilities.

---

## Windows Endpoint Monitoring

Deploy a Windows virtual machine with:

- Sysmon
- Windows Event Logs
- PowerShell logging

---

## Active Response

Automatically respond to threats by:

- Blocking IP addresses
- Disabling compromised accounts
- Isolating endpoints
- Killing malicious processes

---

## Threat Intelligence Integration

Integrate external intelligence feeds such as:

- VirusTotal
- AbuseIPDB
- AlienVault OTX
- MISP

---

## Detection Engineering

Improve detection coverage by:

- Custom Wazuh Rules
- Sigma Rules
- YARA Rules
- IOC Matching

---

## Multi-Endpoint Environment

Expand monitoring to include:

- Multiple Linux systems
- Windows hosts
- Docker containers
- Cloud virtual machines

---

## Advanced Monitoring

Future attack simulations may include:

- Web attacks
- Reverse shell detection
- Malware behavior
- Persistence mechanisms
- Rootkit detection
- Lateral movement
- DNS anomalies
- Suspicious process execution

---

# 💼 Professional Value

This project demonstrates hands-on experience relevant to several cybersecurity positions, including:

- SOC Analyst
- Blue Team Analyst
- Cybersecurity Analyst
- Detection Engineer
- Security Operations Intern
- Junior Security Engineer

The repository highlights not only technical implementation but also analytical thinking, structured investigations, and professional documentation.

---

# 👨‍💻 About the Author

**Falilou Diouf**

Cybersecurity Engineering Student with a strong interest in:

- Security Operations Centers (SOC)
- Blue Team Operations
- SIEM Technologies
- Linux Security
- Threat Detection
- Incident Response
- Embedded Systems Security
- Artificial Intelligence for Cybersecurity

---

### Connect with Me

**GitHub**

```
https://github.com/falliloudiouf20-ai
```

**LinkedIn**

> *(Add your LinkedIn profile URL here.)*

---

# 🤝 Acknowledgments

This laboratory would not have been possible without the open-source cybersecurity community.

Special thanks to:

- The Wazuh Project
- The MITRE ATT&CK Framework
- Ubuntu Linux
- Kali Linux
- Oracle VirtualBox
- The broader open-source security community

Their tools and documentation make practical cybersecurity learning accessible to everyone.

---

# 📜 License

This project is released under the **MIT License**.

You are free to:

- Use
- Modify
- Learn from
- Share

the contents of this repository while preserving the original license.

---

# 🏁 Final Conclusion

The **Wazuh SOC Lab** represents the successful design, deployment, and validation of a complete Security Operations Center laboratory focused on Linux endpoint monitoring.

Starting from an empty environment, a fully operational SIEM infrastructure was built using Wazuh, with a monitored Ubuntu endpoint generating real security telemetry. Through controlled attack simulations, the platform successfully detected authentication failures, successful remote logins, file integrity violations, privilege escalation activities, and administrative actions. Every alert was investigated, documented, and mapped to the MITRE ATT&CK framework, providing a realistic view of how enterprise SOC teams monitor and respond to security events.

Beyond the technical implementation, this project emphasizes the importance of structured investigations, event correlation, and clear documentation. The incident reports and security findings included in this repository demonstrate the complete lifecycle of a security investigation—from detection and analysis to reporting and recommendations.

This repository reflects practical, hands-on experience with SIEM technologies, Linux administration, endpoint monitoring, and incident response. It serves as a strong portfolio project for cybersecurity internships and apprenticeships, showcasing both technical competence and the ability to communicate security work in a professional manner.

If you found this project useful, consider giving the repository a ⭐ on GitHub.

---

<p align="center">

**"Security is not about eliminating every threat. It is about detecting, understanding, and responding before those threats become incidents."**

⭐ **Thank you for visiting this project!**

</p>
