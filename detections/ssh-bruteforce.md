# 🔐 SSH Brute Force & Authentication Detection

## Overview

One of the first attack vectors monitored by a Security Operations Center (SOC) is unauthorized remote access. SSH (Secure Shell) is commonly used to administer Linux servers remotely, making it a frequent target for brute-force attacks, password guessing, and credential abuse.

The objective of this scenario was to verify that the Wazuh SIEM could detect:

- Failed SSH authentication attempts
- Successful SSH authentication
- Password guessing attempts
- PAM authentication events
- User session creation
- MITRE ATT&CK mapping

This scenario reproduces a realistic SOC workflow from attack simulation to incident investigation.

---

# Lab Environment

| Component | Value |
|-----------|-------|
| Attacker Machine | Kali Linux |
| Target Machine | Ubuntu 26.04 LTS |
| Target IP | 192.168.56.101 |
| Service | OpenSSH Server |
| Monitoring | Wazuh Agent 4.12.0 |
| SIEM | Wazuh Manager 4.12.0 |

---

# Objective

Validate that Wazuh correctly detects SSH authentication events generated on the Ubuntu endpoint.

The following situations were tested:

- SSH service installation
- SSH connectivity verification
- Failed login attempts
- Successful login
- Session creation
- Event correlation
- MITRE ATT&CK mapping

---

# Initial Problem

The Ubuntu endpoint did not have an SSH server installed.

Command executed:

```bash
sudo systemctl status ssh
```

Result:

```text
Unit ssh.service could not be found.
```

Without the SSH service, no remote connection could be established.

---

# Resolution

OpenSSH Server was installed.

```bash
sudo apt install openssh-server -y
```

The service was enabled.

```bash
sudo systemctl enable --now ssh
```

Verification:

```bash
sudo systemctl status ssh
```

Expected result:

```text
Active: active (running)
```

---

# Port Verification

The SSH port was verified.

```bash
sudo ss -tulpn | grep :22
```

Result:

```text
LISTEN 0.0.0.0:22
```

---

# Connectivity Test

From Kali Linux:

```bash
nc -zv 192.168.56.101 22
```

Result:

```text
22 (ssh) open
```

The endpoint was now reachable.

---

# SSH Monitoring Configuration

Initially, Wazuh was not monitoring authentication logs.

The following configuration was added inside `ossec.conf`.

```xml
<localfile>
  <log_format>syslog</log_format>
  <location>/var/log/auth.log</location>
</localfile>
```

The agent was restarted.

```bash
sudo systemctl restart wazuh-agent
```

Verification:

```text
Analyzing file:
/var/log/auth.log
```

At this point, SSH events started being forwarded to the Wazuh Manager.

---

# Attack Simulation

## Step 1 — Failed Authentication

A first SSH connection was intentionally performed with an incorrect password.

Command:

```bash
ssh -o PreferredAuthentications=password \
-o PubkeyAuthentication=no \
soc_test@192.168.56.101
```

The wrong password was entered.

This simulated a password guessing attempt.

---

## Wazuh Detection

The following alert was generated.

Rule ID

```
5503
```

Description

```
PAM: User login failed
```

MITRE ATT&CK

```
T1110.001
Password Guessing
```

Additional SSH alert:

Rule

```
5760
```

Description

```
sshd: authentication failed
```

Detected event:

```text
Failed password for soc_test from 192.168.56.1
```

Source IP:

```
192.168.56.1
```

Destination user:

```
soc_test
```

The SOC analyst can immediately identify:

- Source IP
- Username
- Authentication mechanism
- Service
- Time
- Endpoint concerned

---

# Successful Authentication

After assigning a password to the newly created account:

```bash
sudo passwd soc_test
```

A new SSH connection was initiated.

```bash
ssh -o PreferredAuthentications=password \
-o PubkeyAuthentication=no \
soc_test@192.168.56.101
```

Connection succeeded.

Ubuntu displayed:

```text
Welcome to Ubuntu 26.04 LTS
```

The remote shell was successfully opened.

---

# Password Change Detection

Before the successful login, Wazuh detected the password modification.

Rule:

```
5555
```

Description

```
PAM: User changed password
```

Detected event:

```text
password changed for soc_test
```

---

# Successful Authentication Detection

Wazuh generated the following alert.

Rule

```
5715
```

Description

```
sshd: authentication success
```

Detected log

```text
Accepted password for soc_test from 192.168.56.1
```

Mapped MITRE ATT&CK

```
T1078
Valid Accounts
```

```
T1021
Remote Services
```

---

# Session Creation

Immediately after authentication, PAM created a user session.

Rule

```
5501
```

Description

```
PAM: Login session opened
```

Detected user

```
soc_test
```

This confirms that the authentication process completed successfully.

---

# Timeline

```
SSH Server absent
        │
        ▼
Installation OpenSSH
        │
        ▼
Port 22 opened
        │
        ▼
SSH reachable
        │
        ▼
Authentication log monitoring enabled
        │
        ▼
Failed login attempt
        │
        ▼
Rule 5503
        │
        ▼
Rule 5760
        │
        ▼
Password configured
        │
        ▼
Successful SSH login
        │
        ▼
Rule 5715
        │
        ▼
PAM session opened
        │
        ▼
Rule 5501
```

---

# Security Analysis

This scenario demonstrates how a SOC analyst can detect both unsuccessful and successful authentication attempts.

A brute-force campaign generally generates numerous failed authentication events before eventually succeeding if valid credentials are obtained.

By monitoring:

- Failed logins
- Successful logins
- Source IP addresses
- Usernames
- Session creation

the analyst can rapidly identify suspicious activity and determine whether an attacker has gained access to the system.

---

# MITRE ATT&CK Mapping

| Technique | Description |
|------------|-------------|
| T1110.001 | Password Guessing |
| T1021 | Remote Services |
| T1078 | Valid Accounts |

---

# Skills Demonstrated

- Linux Administration
- SSH Configuration
- Wazuh Agent Configuration
- Authentication Monitoring
- PAM Log Analysis
- Threat Detection
- Incident Investigation
- MITRE ATT&CK Mapping
- SIEM Event Analysis
- Security Log Correlation

---

# Evidence

Screenshots associated with this scenario:

```
screenshots/
│
├── ssh-alert.png
├── agent-connected.png
└── mitre-attck.png
```

---

# Conclusion

The SSH authentication scenario successfully validated the complete detection chain of the Wazuh platform.

The laboratory demonstrated the ability to detect failed authentication attempts, password changes, successful logins, PAM session creation, and remote access events. All generated alerts were correlated with MITRE ATT&CK techniques, providing a realistic SOC investigation workflow comparable to those used in enterprise Security Operations Centers.
