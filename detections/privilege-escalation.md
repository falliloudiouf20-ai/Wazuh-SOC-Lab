# 🚨 Privilege Escalation Detection

## Overview

Privilege escalation is one of the most critical stages of a cyber attack. Once an attacker gains initial access to a system, the next objective is often to obtain higher privileges in order to disable security controls, access sensitive data, or establish persistence.

This laboratory demonstrates how Wazuh detects privilege escalation activities on a Linux endpoint, including the creation of a new privileged account, membership changes, password modifications, and the execution of administrative commands using `sudo`.

The objective is to validate that these high-risk activities are immediately visible to a SOC analyst.

---

# Lab Environment

| Component | Value |
|-----------|-------|
| Attacker Machine | Kali Linux |
| Target Machine | Ubuntu 26.04 LTS |
| Endpoint | Ubuntu-Agent-01 |
| Agent ID | 002 |
| SIEM | Wazuh 4.12.0 |

---

# Objective

Validate Wazuh's ability to detect:

- User account creation
- Group membership modification
- Privilege assignment
- Password modification
- Successful sudo execution
- SSH authentication using the new privileged account

---

# Scenario Description

After compromising the endpoint, an attacker may attempt to create a new administrator account to maintain long-term access to the system.

This scenario reproduces that behavior using standard Linux administration commands.

---

# Step 1 — Create a New User

A new local account was created.

```bash
sudo useradd -m soc_test
```

Purpose:

- Simulate attacker persistence
- Create a secondary account
- Prepare a privileged user

---

# Wazuh Detection

Rule ID

```
5902
```

Description

```
New user added to the system
```

MITRE ATT&CK

```
T1136
Create Account
```

Observed account

```
soc_test
```

---

# Step 2 — Grant Administrative Privileges

The account was added to the sudo group.

```bash
sudo usermod -aG sudo soc_test
```

Purpose:

Allow the attacker to execute administrative commands.

---

# Wazuh Detection

Rule ID

```
5901
```

Description

```
Group added to the system
```

Observed action

```
soc_test added to sudo group
```

MITRE ATT&CK

```
T1548.003
Sudo and Sudo Caching
```

---

# Step 3 — Configure the Password

A password was assigned to the new account.

```bash
sudo passwd soc_test
```

During testing, Ubuntu displayed:

```text
BAD PASSWORD:
The password is shorter than 8 characters
```

Although the password did not meet Ubuntu's recommended complexity, it was intentionally accepted for laboratory purposes.

Password update completed successfully.

---

# Wazuh Detection

Rule ID

```
5555
```

Description

```
PAM: User changed password
```

Detected event

```text
password changed for soc_test
```

---

# Step 4 — Administrative Command Detection

While configuring the password, Wazuh detected the sudo command.

Detected command

```text
COMMAND=/usr/bin/passwd soc_test
```

Rule ID

```
5402
```

Description

```
Successful sudo to ROOT executed
```

This confirms that Wazuh captures privileged command execution in addition to authentication events.

---

# Step 5 — SSH Login Using the New Account

After assigning a password, the account was used for remote access.

Command executed:

```bash
ssh -o PreferredAuthentications=password \
-o PubkeyAuthentication=no \
soc_test@192.168.56.101
```

Result:

```text
Welcome to Ubuntu 26.04 LTS
```

The connection was established successfully.

---

# Wazuh Detection

Rule ID

```
5715
```

Description

```
sshd: authentication success
```

Detected event

```text
Accepted password for soc_test from 192.168.56.1
```

MITRE ATT&CK

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

Immediately after authentication, PAM opened a new user session.

Rule ID

```
5501
```

Description

```
PAM: Login session opened
```

User

```
soc_test
```

This confirms that the account successfully authenticated and established an interactive session.

---

# Timeline

```
New account created
        │
        ▼
Rule 5902
        │
        ▼
Added to sudo group
        │
        ▼
Rule 5901
        │
        ▼
Password configured
        │
        ▼
Rule 5555
        │
        ▼
sudo detected
        │
        ▼
Rule 5402
        │
        ▼
SSH login
        │
        ▼
Rule 5715
        │
        ▼
PAM session
        │
        ▼
Rule 5501
```

---

# Security Analysis

Privilege escalation is a common post-exploitation technique used by attackers after obtaining an initial foothold.

Creating a new privileged account provides:

- Persistence
- Administrative control
- Remote access capability
- Reduced dependence on compromised credentials

In this laboratory, Wazuh successfully detected every stage of the attack chain, allowing a SOC analyst to reconstruct the complete sequence of events.

---

# MITRE ATT&CK Mapping

| Technique | Description |
|------------|-------------|
| T1136 | Create Account |
| T1548.003 | Sudo and Sudo Caching |
| T1078 | Valid Accounts |
| T1021 | Remote Services |

---

# Evidence

Wazuh recorded:

- User creation
- Group modification
- Password change
- sudo execution
- SSH authentication
- PAM session creation
- Source IP
- Username
- Endpoint
- Timestamp
- Rule ID
- Alert severity

---

# Skills Demonstrated

- Linux User Administration
- Linux Permissions
- sudo Management
- PAM Authentication
- SSH Administration
- Wazuh SIEM
- Threat Detection
- Security Monitoring
- Incident Investigation
- MITRE ATT&CK Mapping

---

# Screenshots

```
screenshots/
│
├── privilege-alert.png
├── ssh-alert.png
└── mitre-attck.png
```

---

# Conclusion

This privilege escalation scenario demonstrated how Wazuh provides full visibility into administrative activities on a monitored Linux endpoint.

From account creation and privilege assignment to password modification, sudo execution, and successful SSH authentication, every critical event was detected and correlated with MITRE ATT&CK techniques.

These capabilities allow SOC analysts to rapidly identify unauthorized privilege escalation attempts, investigate the complete attack timeline, and initiate an appropriate incident response before attackers can establish long-term persistence within the environment.
