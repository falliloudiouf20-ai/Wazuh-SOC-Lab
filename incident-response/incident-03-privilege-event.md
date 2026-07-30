# 🚨 Incident Response Report 03 — Privilege Escalation Detection

## Incident Summary

| Field | Value |
|-------|-------|
| Incident ID | IR-003 |
| Category | Privilege Escalation |
| Severity | Critical |
| Status | Resolved |
| Detection Platform | Wazuh SIEM 4.12.0 |
| Analyst | Falilou Diouf |
| Target Endpoint | Ubuntu-Agent-01 |
| Agent ID | 002 |

---

# Executive Summary

During continuous security monitoring, Wazuh detected a sequence of administrative activities on the monitored Ubuntu endpoint that resulted in the creation of a new privileged account.

The investigation revealed the creation of a local user, assignment of administrative privileges through the `sudo` group, modification of the account password, execution of privileged commands, and a successful SSH login using the newly created account.

Although these actions were intentionally performed in a controlled laboratory environment, they closely resemble the techniques used by attackers after gaining initial access to a Linux system.

This exercise validates Wazuh's ability to detect privilege escalation attempts and provide sufficient evidence for a SOC analyst to investigate the incident.

---

# Detection Timeline

## Event 1 — User Account Creation

A new local user was created.

Command executed:

```bash
sudo useradd -m soc_test
```

Wazuh Detection

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

## Event 2 — Administrative Privilege Assignment

The newly created account was granted administrative privileges.

Command executed:

```bash
sudo usermod -aG sudo soc_test
```

Wazuh Detection

Rule ID

```
5901
```

Description

```
Group modified
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

## Event 3 — Password Configuration

A password was assigned to the account.

Command executed:

```bash
sudo passwd soc_test
```

Ubuntu displayed the following warning:

```text
BAD PASSWORD:
The password is shorter than 8 characters
```

The password was nevertheless accepted because this was a controlled laboratory environment.

---

## Event 4 — Password Change Detection

Wazuh generated the following alert.

Rule ID

```
5555
```

Description

```
PAM: User changed password
```

Observed event

```text
password changed for soc_test
```

---

## Event 5 — Privileged Command Execution

While configuring the account, Wazuh also detected the execution of an administrative command.

Observed command

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

This confirms that Wazuh monitors privileged command execution in addition to authentication events.

---

## Event 6 — Successful SSH Authentication

The new account was then used to establish a remote SSH session.

Command executed:

```bash
ssh -o PreferredAuthentications=password \
-o PubkeyAuthentication=no \
soc_test@192.168.56.101
```

Observed result

```text
Welcome to Ubuntu 26.04 LTS
```

Wazuh Detection

Rule ID

```
5715
```

Description

```
sshd: authentication success
```

Observed event

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

## Event 7 — PAM Session Creation

Immediately after the successful login, Wazuh detected the creation of a user session.

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

---

# Indicators of Compromise (IOCs)

| Indicator | Value |
|-----------|-------|
| Username | soc_test |
| Source IP | 192.168.56.1 |
| Endpoint | Ubuntu-Agent-01 |
| Privileged Group | sudo |
| Remote Service | SSH |
| Administrative Command | passwd |
| Authentication Method | Password |

---

# Investigation

The SOC analyst reviewed the Wazuh alerts generated during the incident.

The investigation confirmed the following sequence:

1. A new account was created.
2. The account was granted administrative privileges.
3. A password was configured.
4. A privileged command was executed using `sudo`.
5. The account authenticated successfully via SSH.
6. A new interactive session was established.

Each event was correlated by Wazuh, allowing the analyst to reconstruct the complete attack timeline.

---

# Root Cause Analysis

Root Cause

The privilege escalation activity was intentionally generated to validate Wazuh detection capabilities.

In a production environment, this sequence would strongly suggest that an attacker had obtained initial access and was attempting to establish long-term administrative control over the endpoint.

---

# Impact Assessment

Potential risks associated with this activity include:

- Unauthorized administrative access
- Persistence
- Credential abuse
- Security control bypass
- Lateral movement
- Deployment of additional malware
- Data exfiltration

Laboratory Impact

```
No production impact.
```

All actions were executed in an isolated virtual environment.

---

# Containment Actions

If this incident occurred in production, the SOC team should:

- Disable the suspicious account.
- Remove unauthorized administrative privileges.
- Reset compromised credentials.
- Terminate active SSH sessions.
- Verify the legitimacy of recent account creations.
- Block suspicious source IP addresses if appropriate.
- Preserve all forensic evidence.

---

# Eradication

Recommended eradication activities include:

- Remove unauthorized user accounts.
- Delete unnecessary sudo privileges.
- Review `/etc/sudoers` and group memberships.
- Remove persistence mechanisms.
- Audit privileged commands executed during the incident.
- Perform a complete malware assessment.

---

# Recovery

Recovery activities should include:

- Restore approved user and group configurations.
- Validate authentication policies.
- Review all privileged accounts.
- Confirm endpoint integrity.
- Continue enhanced monitoring through Wazuh.

---

# Lessons Learned

This exercise demonstrated that Wazuh can successfully detect every critical stage of a privilege escalation attack.

The platform recorded:

- User creation
- Privilege assignment
- Password modification
- Privileged command execution
- Successful authentication
- Session creation

The correlation of these events provides SOC analysts with a clear and complete picture of attacker behavior.

---

# MITRE ATT&CK Mapping

| Technique | Description |
|------------|-------------|
| T1136 | Create Account |
| T1548.003 | Sudo and Sudo Caching |
| T1078 | Valid Accounts |
| T1021 | Remote Services |

---

# Evidence Collected

The following evidence was available for investigation:

- Wazuh alerts
- Authentication logs
- PAM logs
- User creation records
- Group membership changes
- Password change events
- Sudo execution logs
- SSH authentication logs
- Source IP address
- Username
- Rule IDs
- Alert severity

---

# Recommendations

To improve security in a production environment:

- Enforce the principle of least privilege.
- Continuously monitor privileged account creation.
- Enable multi-factor authentication for administrative access.
- Audit membership of the `sudo` group regularly.
- Configure alert escalation for new privileged accounts.
- Restrict SSH access to trusted networks.
- Periodically review authentication and privilege escalation events.

---

# Conclusion

This incident response exercise successfully demonstrated Wazuh's ability to detect and correlate privilege escalation activities on a Linux endpoint.

By identifying new account creation, administrative privilege assignment, password modification, privileged command execution, and remote authentication, Wazuh provided a complete forensic record of the simulated attack.

These capabilities are essential for modern Security Operations Centers, enabling analysts to rapidly identify unauthorized administrative activity, assess the potential impact, and initiate appropriate containment and recovery measures before attackers can fully compromise critical systems.
