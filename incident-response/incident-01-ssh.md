# 🚨 Incident Response Report 01 — SSH Authentication Attack

## Incident Summary

| Field | Value |
|-------|-------|
| Incident ID | IR-001 |
| Category | Unauthorized Remote Access |
| Severity | High |
| Status | Resolved |
| Detection Platform | Wazuh SIEM 4.12.0 |
| Analyst | Falilou Diouf |
| Target Endpoint | Ubuntu-Agent-01 |
| Agent ID | 002 |

---

# Executive Summary

During routine monitoring of the Ubuntu endpoint, Wazuh detected multiple SSH authentication events originating from the Kali Linux workstation.

The incident began with unsuccessful authentication attempts against the newly created user account `soc_test`. Shortly afterward, a successful authentication was observed from the same source IP, resulting in the creation of a remote SSH session.

Although this activity was intentionally generated as part of the laboratory, it accurately reproduces a real-world attack scenario involving password guessing followed by valid credential usage.

The objective of this incident response exercise was to validate the organization's capability to detect, investigate, and document remote access events using Wazuh SIEM.

---

# Detection Timeline

## Event 1 — Failed SSH Authentication

Observed Event

```text
Failed password for soc_test from 192.168.56.1
```

Wazuh Rule

```
5760
```

Description

```
sshd: authentication failed
```

MITRE ATT&CK

```
T1110.001
Password Guessing
```

Severity

```
Medium
```

---

## Event 2 — PAM Authentication Failure

Rule

```
5503
```

Description

```
PAM: User login failed
```

Observed User

```
soc_test
```

This confirms that the endpoint rejected the authentication request.

---

## Event 3 — Successful Authentication

Observed Event

```text
Accepted password for soc_test from 192.168.56.1
```

Rule

```
5715
```

Description

```
sshd: authentication success
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

## Event 4 — Session Creation

Rule

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

Result

The remote session was successfully established.

---

# Indicators of Compromise (IOCs)

| Indicator | Value |
|-----------|-------|
| Source IP | 192.168.56.1 |
| Destination Host | Ubuntu-Agent-01 |
| Username | soc_test |
| Protocol | SSH |
| Port | 22 |
| Authentication Method | Password |

---

# Investigation

The analyst reviewed the Wazuh alerts stored in:

```bash
/var/ossec/logs/alerts/alerts.json
```

Relevant events included:

- Failed SSH authentication
- PAM authentication failure
- Successful authentication
- Session creation

The logs confirmed that all authentication attempts originated from the Kali Linux analysis workstation.

No evidence of malware execution or persistence mechanisms was observed during this specific incident.

---

# Root Cause Analysis

Root Cause

The activity was intentionally generated to validate SSH monitoring capabilities within the Wazuh SOC laboratory.

In a production environment, this sequence would indicate:

- Password guessing
- Credential compromise
- Unauthorized remote access

and would require immediate investigation.

---

# Impact Assessment

Potential risks include:

- Unauthorized remote access
- Credential compromise
- Privilege abuse
- Lateral movement
- Data theft
- Persistence

In this laboratory:

```
No production impact.
```

The activity was fully controlled.

---

# Containment Actions

The following actions would normally be performed by a SOC team:

- Verify source IP legitimacy
- Confirm user identity
- Review authentication history
- Disable compromised account if necessary
- Force password reset
- Block malicious IP address
- Preserve forensic evidence

---

# Eradication

Possible eradication actions include:

- Remove malicious accounts
- Change compromised passwords
- Revoke SSH keys
- Remove persistence mechanisms
- Audit privileged accounts

For this laboratory, no eradication was required because the activity was generated intentionally.

---

# Recovery

Recommended recovery steps:

- Confirm system integrity
- Review authentication logs
- Verify no unauthorized accounts remain
- Validate SSH configuration
- Continue monitoring

---

# Lessons Learned

This incident demonstrated that Wazuh successfully detected:

- Failed SSH authentication
- Successful authentication
- PAM login events
- Remote session establishment

The investigation also confirmed that authentication events can be correlated to reconstruct the complete attack timeline.

---

# MITRE ATT&CK

| Technique | Description |
|------------|-------------|
| T1110.001 | Password Guessing |
| T1078 | Valid Accounts |
| T1021 | Remote Services |

---

# Evidence Collected

The following evidence was collected during the investigation:

- Wazuh alerts
- Authentication logs
- SSH logs
- PAM logs
- Source IP address
- Username
- Rule IDs
- Alert severity
- MITRE ATT&CK mapping

---

# Recommendations

To improve the security posture of a production environment:

- Enforce strong password policies.
- Disable password authentication and use SSH keys where possible.
- Restrict SSH access using firewalls or VPNs.
- Enable multi-factor authentication (MFA).
- Monitor repeated authentication failures.
- Regularly review privileged account activity.
- Configure alert escalation for repeated failed logins.

---

# Conclusion

This incident response exercise validated the effectiveness of Wazuh in detecting and documenting SSH authentication events on a Linux endpoint.

The SIEM successfully identified failed login attempts, successful authentication, and session creation while providing sufficient forensic information for a SOC analyst to reconstruct the incident timeline.

Although the activity was generated in a controlled laboratory environment, the detection and investigation process accurately reflects the workflow used by enterprise Security Operations Centers when responding to unauthorized remote access attempts.
