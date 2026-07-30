# Wazuh SOC Lab — Commands Reference

---

# Overview

This document serves as the complete command reference for the **Wazuh SOC Lab** project.

Every command listed in this document was used during one or more phases of the laboratory, including:

- Environment preparation
- Network verification
- Wazuh deployment
- Agent enrollment
- SSH monitoring
- File Integrity Monitoring (FIM)
- Privilege escalation detection
- Incident investigation
- Linux administration
- Troubleshooting

Each command includes its purpose and the expected outcome.

---

# Table of Contents

1. Linux Information
2. Network Diagnostics
3. Wazuh Installation
4. Wazuh Service Management
5. Agent Management
6. SSH Monitoring
7. File Integrity Monitoring
8. Privilege Escalation
9. Incident Investigation
10. Useful Linux Commands
11. Troubleshooting Commands

---

# 1. Linux Information

## Display system information

```bash
hostnamectl
```

Purpose

- Display hostname
- Operating system
- Kernel version
- Hardware information

---

## Display CPU information

```bash
lscpu
```

Purpose

- Processor architecture
- Number of cores
- CPU model

---

## Display memory usage

```bash
free -h
```

Purpose

Display RAM and swap usage.

---

## Display disk layout

```bash
lsblk
```

Purpose

Display available storage devices.

---

## Display mounted filesystems

```bash
df -h
```

Purpose

Check available storage space.

---

# 2. Network Diagnostics

## Display IP addresses

```bash
ip -br addr
```

Purpose

Quickly display all network interfaces and assigned IP addresses.

---

## Display routing table

```bash
ip route
```

Purpose

Display default gateway and routing information.

---

## Test localhost connectivity

```bash
ping 127.0.0.1
```

Purpose

Verify TCP/IP stack functionality.

---

## Test Internet connectivity

```bash
ping google.com
```

Purpose

Verify Internet access.

---

## DNS verification

```bash
dig example.com
```

Purpose

Verify DNS resolution.

---

## Display listening ports

```bash
sudo ss -tulpn
```

Purpose

Display active services and listening ports.

---

## Check SSH listening port

```bash
sudo ss -tulpn | grep :22
```

Purpose

Verify SSH service availability.

---

# 3. Wazuh Installation

## Download Wazuh Agent

```bash
wget https://packages.wazuh.com/4.x/apt/pool/main/w/wazuh-agent/wazuh-agent_4.12.0-1_amd64.deb
```

Purpose

Download the official Wazuh Agent package.

---

## Install Wazuh Agent

```bash
sudo WAZUH_MANAGER='127.0.0.1' \
WAZUH_AGENT_NAME='kali-endpoint' \
dpkg -i ./wazuh-agent_4.12.0-1_amd64.deb
```

Purpose

Install and register the endpoint with the Wazuh Manager.

---

## Check installed agents

```bash
sudo /var/ossec/bin/agent_control -l
```

Purpose

Display enrolled agents.

---

# 4. Wazuh Service Management

## Restart Wazuh Agent

```bash
sudo systemctl restart wazuh-agent
```

Purpose

Apply configuration changes.

---

## Check Wazuh Agent status

```bash
sudo systemctl status wazuh-agent
```

Purpose

Verify service health.

---

## Restart Wazuh Manager

```bash
sudo systemctl restart wazuh-manager
```

Purpose

Restart the manager after configuration changes.

---

## Check Manager status

```bash
sudo systemctl status wazuh-manager
```

Purpose

Verify Wazuh Manager operation.

---

# 5. SSH Configuration

## Install OpenSSH Server

```bash
sudo apt install openssh-server -y
```

Purpose

Install SSH service on Ubuntu.

---

## Enable SSH

```bash
sudo systemctl enable --now ssh
```

Purpose

Start SSH and enable automatic startup.

---

## Check SSH service

```bash
sudo systemctl status ssh
```

Purpose

Verify SSH status.

---

## Remote login

```bash
ssh soc_test@192.168.56.101
```

Purpose

Generate successful authentication events.

---

## Failed login attempts

Example

```text
Wrong password entered several times.
```

Purpose

Generate authentication failure alerts.

---

# 6. Authentication Logs

## Monitor authentication log

```bash
tail -f /var/log/auth.log
```

Purpose

Observe authentication events in real time.

---

## Search SSH events

```bash
grep ssh /var/log/auth.log
```

Purpose

Display SSH-related entries.

---

## Search failed authentications

```bash
grep "Failed password" /var/log/auth.log
```

Purpose

Locate brute-force attempts.

---

## Search successful authentications

```bash
grep "Accepted password" /var/log/auth.log
```

Purpose

Locate successful SSH logins.

---

# 7. File Integrity Monitoring

## Create monitored directory

```bash
sudo mkdir -p /var/www/sensitive
```

Purpose

Create monitored directory.

---

## Create test file

```bash
sudo nano /var/www/sensitive/config.php
```

Purpose

Create monitored file.

---

Example content

```text
MODIFICATION_DE_TEST_WAZUH=true
```

---

## Modify monitored file

```bash
sudo nano /var/www/sensitive/config.php
```

Purpose

Trigger File Integrity Monitoring.

---

## Change permissions

```bash
sudo chmod 777 /var/www/sensitive/config.php
```

Purpose

Generate metadata change alerts.

---

---

# 8. Privilege Escalation

## Create a new user

```bash
sudo useradd -m soc_test
```

### Purpose

Create a new local Linux account used to simulate an attacker creating a new user after gaining initial access.

### Expected Detection

- Rule **5902**
- MITRE **T1136 – Create Account**

---

## Add the user to the sudo group

```bash
sudo usermod -aG sudo soc_test
```

### Purpose

Grant administrative privileges to the newly created account.

### Expected Detection

- Rule **5901**
- MITRE **T1548.003 – Sudo and Sudo Caching**

---

## Set the user's password

```bash
sudo passwd soc_test
```

### Purpose

Assign a password to the newly created account.

### Expected Detection

- Rule **5555**
- Password modification event

---

## Authenticate using the new account

```bash
ssh soc_test@192.168.56.101
```

### Purpose

Generate a successful SSH authentication using a privileged account.

### Expected Detection

- Rule **5715**
- Rule **5501**
- MITRE **T1078**
- MITRE **T1021**

---

## Execute privileged commands

```bash
sudo whoami
```

### Purpose

Generate privileged command execution.

---

Expected Output

```text
root
```

---

## Verify current identity

```bash
whoami
```

Purpose

Display the currently authenticated user.

---

## Display user ID

```bash
id
```

Purpose

Display UID, GID and group memberships.

---

# 9. Wazuh Configuration Commands

## Edit Wazuh configuration

```bash
sudo nano /var/ossec/etc/ossec.conf
```

Purpose

Modify Wazuh monitoring configuration.

---

## Monitor authentication logs

```xml
<localfile>
  <log_format>syslog</log_format>
  <location>/var/log/auth.log</location>
</localfile>
```

Purpose

Enable authentication log monitoring.

---

## Configure File Integrity Monitoring

```xml
<directories realtime="yes" report_changes="yes">
/var/www/sensitive
</directories>
```

Purpose

Enable real-time monitoring of sensitive files.

---

## Restart Wazuh Agent

```bash
sudo systemctl restart wazuh-agent
```

Purpose

Apply configuration changes.

---

## Verify monitored files

```text
Analyzing file:
/var/log/auth.log
```

Purpose

Confirm that the Wazuh Agent is collecting authentication events.

---

# 10. Incident Investigation Commands

## Search authentication failures

```bash
grep "Failed password" /var/log/auth.log
```

Purpose

Identify failed SSH login attempts.

---

## Search successful logins

```bash
grep "Accepted password" /var/log/auth.log
```

Purpose

Identify successful SSH authentications.

---

Example

```text
Accepted password for soc_test from 192.168.56.1
```

---

## Follow authentication log in real time

```bash
tail -f /var/log/auth.log
```

Purpose

Observe authentication events live.

---

## Search SSH events

```bash
grep ssh /var/log/auth.log
```

Purpose

Display SSH-related events.

---

# 11. Useful Linux Commands

## List files

```bash
ls
```

---

## Detailed listing

```bash
ls -l
```

---

## List hidden files

```bash
ls -la
```

---

## Current directory

```bash
pwd
```

---

## Change directory

```bash
cd
```

---

## Create directory

```bash
mkdir
```

---

## Remove directory

```bash
rmdir
```

---

## Copy files

```bash
cp
```

---

## Move files

```bash
mv
```

---

## Delete files

```bash
rm
```

---

## Display file contents

```bash
cat
```

---

## Edit files

```bash
nano
```

---

## Search text

```bash
grep
```

---

## Search files

```bash
find
```

---

# 12. Troubleshooting Commands Used During the Lab

## Check SSH service

```bash
sudo systemctl status ssh
```

---

## Start SSH service

```bash
sudo systemctl start ssh
```

---

## Enable SSH at boot

```bash
sudo systemctl enable ssh
```

---

## Verify listening services

```bash
sudo ss -tulpn
```

---

## Check SSH port

```bash
sudo ss -tulpn | grep :22
```

---

## Display interfaces

```bash
ip -br addr
```

---

## Display routing table

```bash
ip route
```

---

## Connectivity test

```bash
ping 127.0.0.1
```

---

## DNS verification

```bash
dig example.com
```

---

# Problems Encountered During the Project

During the laboratory, several issues were identified and resolved.

## SSH service not installed

Observed message:

```text
Unit ssh.service could not be found.
```

Resolution:

```bash
sudo apt install openssh-server -y
sudo systemctl enable --now ssh
```

---

## Wazuh Agent service unavailable

Observed message:

```text
Unit wazuh-agent.service could not be found.
```

Resolution

Verify installation and reinstall the agent if necessary.

---

## Missing DHCP client

Observed message

```text
dhclient: command not found
```

Resolution

Use the appropriate networking tools available on the operating system instead of relying on `dhclient`.

---

# Wazuh Rules Observed

| Rule ID | Description |
|----------|-------------|
| 550 | File Integrity checksum changed |
| 5501 | PAM session opened |
| 5503 | PAM authentication failure |
| 5402 | Successful sudo execution |
| 5555 | Password modification |
| 5715 | Successful SSH authentication |
| 5760 | Failed SSH authentication |
| 5901 | User added to privileged group |
| 5902 | User account creation |

---

# MITRE ATT&CK Techniques Observed

| Technique | Description |
|------------|-------------|
| T1110.001 | Password Guessing |
| T1021 | Remote Services |
| T1078 | Valid Accounts |
| T1136 | Create Account |
| T1548.003 | Sudo and Sudo Caching |
| T1565.001 | Stored Data Manipulation |

---

# Summary

This reference gathers every significant command used throughout the Wazuh SOC Lab.

The commands cover:

- Linux system administration
- Network diagnostics
- Wazuh deployment
- Wazuh configuration
- SSH monitoring
- Authentication analysis
- File Integrity Monitoring
- Privilege escalation simulation
- Incident investigation
- Troubleshooting

Together, they reproduce the complete operational workflow followed during the deployment, configuration, monitoring, and validation of the Wazuh-based Security Operations Center laboratory.
