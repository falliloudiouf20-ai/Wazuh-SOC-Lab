# 📄 Final Report — Wazuh SOC Lab

## Project Information

| Item | Value |
|------|-------|
| Project | Wazuh SOC Lab |
| Author | Falilou Diouf |
| Role | Cybersecurity Student / SOC Analyst |
| Platform | Wazuh 4.12.0 |
| Operating Systems | Kali Linux & Ubuntu 26.04 LTS |
| Project Type | Security Operations Center (SOC) Laboratory |
| Duration | Multi-stage practical laboratory |
| Repository | Wazuh-SOC-Lab |

---

# Executive Summary

The purpose of this project was to design, deploy, and validate a complete Security Operations Center (SOC) laboratory using the Wazuh SIEM platform.

The laboratory was built from scratch using a Kali Linux machine acting as the Wazuh Manager and an Ubuntu virtual machine configured as a monitored endpoint. Throughout the project, the endpoint generated real security events that were collected, analyzed, and correlated by Wazuh.

Rather than relying on sample datasets or pre-recorded logs, every alert analyzed during this project was produced by practical hands-on testing. This approach provided a realistic understanding of endpoint monitoring, threat detection, incident response, and security event analysis.

The completed laboratory reproduces the workflow commonly followed by SOC analysts when monitoring Linux infrastructures.

---

# Project Objectives

The project was designed to achieve the following objectives:

- Deploy a functional SIEM environment.
- Configure endpoint monitoring.
- Collect security logs from Linux.
- Detect authentication events.
- Detect file modifications.
- Detect privilege escalation activities.
- Correlate multiple security events.
- Investigate security incidents.
- Produce professional security documentation.

All objectives were successfully completed.

---

# Laboratory Architecture

The environment consisted of two virtual systems connected through a dedicated Host-Only VirtualBox network.

```
                 +---------------------------+
                 |      Kali Linux           |
                 |---------------------------|
                 | Wazuh Manager             |
                 | Wazuh Dashboard           |
                 | Filebeat                  |
                 | Indexer                   |
                 +------------+--------------+
                              |
                              |
                Host-Only Network
                              |
                              |
                 +------------+--------------+
                 |      Ubuntu 26.04 LTS     |
                 |---------------------------|
                 | Wazuh Agent               |
                 | OpenSSH Server            |
                 | Syscheck (FIM)            |
                 +---------------------------+
```

The Ubuntu endpoint continuously transmitted security events to the Wazuh Manager, where they were processed and displayed through the Wazuh Dashboard.

---

# Project Workflow

The project followed the same lifecycle used during enterprise SOC deployments.

```
Infrastructure Preparation
            │
            ▼
Wazuh Installation
            │
            ▼
Agent Enrollment
            │
            ▼
Log Collection
            │
            ▼
Security Monitoring
            │
            ▼
Attack Simulation
            │
            ▼
Alert Generation
            │
            ▼
Incident Investigation
            │
            ▼
Documentation
```

Each stage was validated before progressing to the next one.

---

# Simulated Security Scenarios

The following attack scenarios were successfully reproduced.

## 1. SSH Authentication Monitoring

Objective:

Validate Wazuh's ability to detect SSH authentication events.

Observed detections:

- Failed authentication
- Successful authentication
- PAM session creation

Relevant Rules:

- 5503
- 5715
- 5760
- 5501

---

## 2. File Integrity Monitoring

Objective:

Detect modifications to sensitive files.

Observed detections:

- File modification
- Checksum variation
- Permission changes
- Metadata updates

Relevant Rule:

- 550

---

## 3. Privilege Escalation

Objective:

Detect administrative account manipulation.

Observed detections:

- User creation
- Group modification
- Password change
- sudo execution
- Successful SSH login

Relevant Rules:

- 5902
- 5901
- 5555
- 5402
- 5715

---

# Security Findings

The laboratory confirmed that Wazuh successfully detects several categories of security events.

| Category | Result |
|----------|--------|
| Authentication Monitoring | ✅ |
| SSH Monitoring | ✅ |
| File Integrity Monitoring | ✅ |
| Privileged Commands | ✅ |
| User Management | ✅ |
| Account Creation | ✅ |
| Group Modification | ✅ |
| Password Changes | ✅ |
| Event Correlation | ✅ |

Every tested scenario generated the expected alerts.

---

# MITRE ATT&CK Coverage

Throughout the project, Wazuh automatically mapped alerts to the MITRE ATT&CK framework.

| Technique | Description |
|------------|-------------|
| T1110.001 | Password Guessing |
| T1078 | Valid Accounts |
| T1021 | Remote Services |
| T1136 | Create Account |
| T1548.003 | Sudo and Sudo Caching |
| T1565.001 | Stored Data Manipulation |

The use of MITRE ATT&CK significantly improves threat classification and incident investigation by providing standardized techniques recognized across the cybersecurity industry.

---

# Incident Response Capabilities

Three complete incident investigations were documented during this laboratory.

| Incident | Description |
|-----------|-------------|
| IR-001 | SSH Authentication Investigation |
| IR-002 | File Integrity Monitoring Investigation |
| IR-003 | Privilege Escalation Investigation |

Each report included:

- Executive summary
- Timeline
- Investigation
- Root cause analysis
- Impact assessment
- Containment
- Recovery
- Recommendations
- MITRE mapping

---

# Skills Developed

This project provided practical experience in several cybersecurity domains.

### SIEM

- Wazuh deployment
- Alert analysis
- Rule interpretation
- Dashboard usage

### Linux

- User administration
- SSH configuration
- Service management
- File permissions
- System monitoring

### SOC Operations

- Security monitoring
- Incident investigation
- Event correlation
- Threat analysis
- Documentation

### Defensive Security

- File Integrity Monitoring
- Authentication monitoring
- Privilege monitoring
- Endpoint visibility

---

# Challenges Encountered

Several technical challenges were encountered and resolved during the project.

### SSH Service

Initially, the Ubuntu endpoint did not include the OpenSSH Server package.

After installation and configuration, remote monitoring became operational.

---

### Log Collection

Authentication logs were not initially collected because `/var/log/auth.log` was not configured within the Wazuh Agent.

Adding the appropriate `<localfile>` configuration and restarting the agent resolved the issue.

---

### Endpoint Monitoring

Additional configuration was required to enable real-time monitoring of the custom directory:

```
/var/www/sensitive
```

This allowed Syscheck to immediately detect file modifications.

---

# Lessons Learned

This project reinforced several important cybersecurity concepts.

- SIEM platforms are most effective when multiple security events are correlated rather than analyzed individually.
- File Integrity Monitoring is an essential mechanism for detecting post-compromise activity.
- Authentication logs provide valuable insight into attempted and successful remote access.
- Administrative account creation and privilege escalation are high-value indicators that require immediate investigation.
- Proper documentation is as important as technical detection because it enables repeatable incident response procedures.

Beyond the technical configuration of Wazuh, this laboratory emphasized the investigative mindset required in a Security Operations Center.

---

# Future Improvements

The laboratory can be expanded with additional security scenarios, including:

- Web attack detection
- Malware detection
- Rootkit detection
- Sysmon integration
- Windows endpoint monitoring
- Active Response automation
- Email alerting
- VirusTotal integration
- YARA scanning
- Multi-endpoint monitoring
- Docker monitoring
- Cloud workload monitoring

These enhancements would provide broader detection coverage and simulate more advanced enterprise environments.

---

# Professional Value

This project demonstrates practical knowledge of:

- SIEM deployment
- Endpoint security
- Linux administration
- Threat detection
- Security monitoring
- Incident response
- Log analysis
- MITRE ATT&CK
- Security documentation
- SOC analyst methodologies

Unlike purely theoretical exercises, every detection documented in this repository was generated through real interactions with the monitored endpoint, providing verifiable evidence of hands-on experience.

---

# Final Conclusion

The **Wazuh SOC Lab** successfully met all of its technical and educational objectives.

A fully operational Security Information and Event Management (SIEM) environment was deployed, configured, and validated through realistic attack simulations. The platform accurately detected SSH authentication events, file integrity violations, privilege escalation activities, and administrative actions while correlating these events with the MITRE ATT&CK framework.

The project also demonstrated the complete lifecycle of a SOC investigation—from infrastructure deployment and log collection to incident detection, forensic analysis, reporting, and remediation planning.

Beyond proving technical proficiency with Wazuh, Linux, and security monitoring, this repository showcases the ability to document cybersecurity work to a professional standard. It reflects practical experience that is directly relevant to roles such as **SOC Analyst**, **Blue Team Analyst**, **Cybersecurity Engineer**, or **Security Operations Intern**, making it a strong portfolio project for internship and apprenticeship applications.
