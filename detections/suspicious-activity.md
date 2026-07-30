# 🕵️ Suspicious Activity Detection

## Overview

Modern Security Operations Centers (SOCs) continuously monitor systems for suspicious behaviors that may indicate an ongoing cyber attack. Rather than relying on a single alert, analysts correlate multiple events to reconstruct the attack timeline and determine whether an endpoint has been compromised.

This scenario combines the activities performed throughout the laboratory into a single investigation. Wazuh successfully detected each stage of the simulated attack, allowing the SOC analyst to observe the complete kill chain from initial access to privilege escalation.

Unlike previous chapters that focused on individual detections, this section presents the attack from an incident investigation perspective.

---

# Lab Environment

| Component | Value |
|-----------|-------|
| SIEM | Wazuh 4.12.0 |
| Manager | Kali Linux |
| Endpoint | Ubuntu 26.04 LTS |
| Agent | Ubuntu-Agent-01 |
| Agent ID | 002 |

---

# Objective

Demonstrate how multiple low-level events become a complete security incident when correlated together.

Observed activities include:

- SSH authentication failures
- Successful SSH authentication
- File integrity monitoring alerts
- User creation
- Privilege escalation
- Password modification
- Administrative command execution

---

# Attack Timeline

```
Reconnaissance
      │
      ▼
SSH Service Available
      │
      ▼
Failed SSH Authentication
      │
      ▼
Password Guessing Detection
      │
      ▼
Creation of New User
      │
      ▼
User Added to sudo Group
      │
      ▼
Password Configuration
      │
      ▼
Successful SSH Authentication
      │
      ▼
Sensitive File Modification
      │
      ▼
Integrity Alert
      │
      ▼
SOC Investigation
```

---

# Stage 1 — Failed Authentication

The first suspicious activity detected was an unsuccessful SSH authentication attempt.

Observed event:

```text
Failed password for soc_test
```

Detected by Wazuh:

Rule

```
5760
```

Description

```
sshd: authentication failed
```

MITRE

```
T1110.001
Password Guessing
```

---

# Stage 2 — PAM Authentication Failure

The authentication failure also generated PAM alerts.

Rule

```
5503
```

Description

```
PAM: User login failed
```

This event confirms that the endpoint rejected the authentication request.

---

# Stage 3 — Creation of a New Account

A new account was created.

Observed account:

```
soc_test
```

Rule

```
5902
```

MITRE

```
T1136
Create Account
```

This represents a common persistence technique after an attacker gains access to a machine.

---

# Stage 4 — Privilege Escalation

The new account received administrator privileges.

Command executed:

```bash
sudo usermod -aG sudo soc_test
```

Wazuh detected the modification.

Rule

```
5901
```

Administrative command execution was also recorded.

Rule

```
5402
```

Description

```
Successful sudo to ROOT executed
```

---

# Stage 5 — Password Modification

The attacker configured credentials for the newly created account.

Command:

```bash
sudo passwd soc_test
```

Detected by Wazuh:

Rule

```
5555
```

Description

```
PAM: User changed password
```

---

# Stage 6 — Successful Authentication

After the password was configured, a new SSH session was successfully established.

Observed event:

```text
Accepted password for soc_test
```

Rule

```
5715
```

MITRE

```
T1078
Valid Accounts
```

```
T1021
Remote Services
```

Immediately afterward, PAM generated:

Rule

```
5501
```

Description

```
Login session opened
```

---

# Stage 7 — File Integrity Monitoring

The attacker modified a protected file.

Target:

```
/var/www/sensitive/config.php
```

Modification:

```text
MODIFICATION_DE_TEST_WAZUH=true
```

Rule

```
550
```

Description

```
Integrity checksum changed
```

Alert Level

```
7
```

The platform also detected permission modifications after:

```bash
chmod 600
```

---

# Event Correlation

From the SOC perspective, none of these alerts should be analyzed independently.

Instead, they form the following sequence:

```
Authentication Failure
        │
        ▼
Account Creation
        │
        ▼
Privilege Assignment
        │
        ▼
Password Change
        │
        ▼
Successful Authentication
        │
        ▼
Sensitive File Modification
```

This sequence strongly suggests a post-compromise scenario requiring immediate investigation.

---

# MITRE ATT&CK Coverage

| Technique | Description |
|------------|-------------|
| T1110.001 | Password Guessing |
| T1078 | Valid Accounts |
| T1021 | Remote Services |
| T1136 | Create Account |
| T1548.003 | Sudo and Sudo Caching |
| T1565.001 | Stored Data Manipulation |

---

# Security Analysis

This laboratory demonstrates the importance of event correlation within a SIEM.

A failed SSH login alone may simply indicate a user typing the wrong password.

A successful login alone may appear legitimate.

A file modification alone may correspond to a normal administrator activity.

However, when these events occur in sequence, they reveal a much more serious situation.

By correlating authentication events, privilege escalation, account creation, and file modifications, the SOC analyst gains a complete understanding of the attack lifecycle and can prioritize the incident accordingly.

---

# Incident Severity Assessment

| Activity | Risk |
|----------|------|
| Failed SSH Login | Medium |
| Successful SSH Login | Medium |
| New User Creation | High |
| Privilege Escalation | Critical |
| Password Modification | High |
| File Integrity Alert | High |

Overall Incident Severity:

```
HIGH
```

---

# Skills Demonstrated

- Security Monitoring
- Event Correlation
- SOC Investigation
- Linux Administration
- Threat Detection
- Wazuh SIEM
- Incident Analysis
- File Integrity Monitoring
- SSH Monitoring
- Privilege Escalation Detection
- MITRE ATT&CK Mapping
- Log Analysis

---

# Evidence

Associated screenshots:

```
screenshots/
│
├── ssh-alert.png
├── file-integrity-alert.png
├── privilege-alert.png
├── mitre-attck.png
└── agent-connected.png
```

---

# Conclusion

This scenario consolidates every detection performed throughout the Wazuh SOC laboratory into a single investigative workflow.

Rather than analyzing isolated alerts, the exercise demonstrates how a SIEM enables analysts to correlate authentication failures, account creation, privilege escalation, administrative activity, and file integrity events to identify a coordinated attack.

The result is a realistic SOC investigation process that reflects how enterprise security teams detect, analyze, and respond to suspicious activity on Linux endpoints.
