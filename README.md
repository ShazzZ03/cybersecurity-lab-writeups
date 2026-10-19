# Metasploitable 2 — Security Assessment

## Overview

This project documents a hands-on security assessment of **Metasploitable 2**, an intentionally vulnerable Linux virtual machine, conducted in an isolated VirtualBox lab using Kali Linux.

The objective was to practise network reconnaissance, service enumeration, vulnerability research, controlled exploitation, access verification, evidence collection, and security remediation reporting.

> **Scope:** This assessment was performed only against an intentionally vulnerable lab machine in an authorized environment.

## Objectives

- Identify exposed network services and their versions.
- Enumerate FTP and other accessible services.
- Research known vulnerabilities using Searchsploit.
- Validate the UnrealIRCd backdoor using Metasploit.
- Verify root-level access through the exposed bind shell.
- Document evidence, security impact, and remediation recommendations.

## Lab Environment

| Component | Details |
|---|---|
| Attacker machine | Kali Linux |
| Target machine | Metasploitable 2 |
| Virtualization | Oracle VirtualBox |
| Network scanning | Nmap |
| Vulnerability research | Searchsploit |
| Exploitation framework | Metasploit Framework |
| Shell interaction | Netcat and Meterpreter |

## Methodology

### 1. Reconnaissance and Service Enumeration

Used Nmap to identify open ports and detect the services running on the target machine.

**Command used:**

```bash
nmap -sV 192.168.0.109
```

The scan identified multiple exposed services, including FTP, SSH, Telnet, HTTP, SMB, MySQL, and UnrealIRCd.

### 2. FTP Enumeration

Checked whether anonymous FTP authentication was permitted.

```bash
nmap -sC -p 21 192.168.0.109
```

**Observation:** Anonymous FTP login was allowed. The directory listing did not show any files in the observed session.

**Recommendation:** Disable anonymous access unless required, and restrict FTP access to authorized users.

### 3. UnrealIRCd Vulnerability Research

Used Searchsploit to research known UnrealIRCd vulnerabilities.

```bash
searchsploit UnrealIRCd
```

The results included a known UnrealIRCd backdoor entry. The corresponding Metasploit module was used to validate the issue in the lab.

**Result:** Metasploit reported that a Meterpreter session opened successfully. The session's identity was checked to verify the level of access obtained.

*Note: The exact installed UnrealIRCd version should be independently verified before attributing the finding to a specific version.*

### 4. Bind Shell Verification

Connected to the exposed bind-shell service on TCP port 1524 using Netcat.

```bash
nc -v 192.168.0.109 1524
```

Within the lab session, the following commands were used to verify access:

```bash
id
whoami
hostname
```

**Result:** The output showed UID 0 (`root`) on the Metasploitable target, demonstrating critical root-level exposure.

### 5. Evidence Collection

Screenshots are included to document the scan results, vulnerability research, successful session, access verification, and FTP enumeration.

## Key Findings

| Finding | Evidence | Security Impact |
|---|---|---|
| Exposed root bind shell — TCP 1524 | Netcat session and `id` output | Critical: unauthenticated root-level access |
| UnrealIRCd backdoor — TCP 6667 | Searchsploit research and successful Meterpreter session | Critical: remote command execution and root access observed |
| Anonymous FTP enabled — TCP 21 | Nmap script output and FTP login | Low/informational: anonymous access permitted; no files were observed |
| Telnet access | Successful lab login | Insecure remote administration over an unencrypted protocol |

*Severity ratings are preliminary lab-assessment judgments, not results of a formal CVSS calculation.*

## Remediation Recommendations

- Disable and remove the exposed root bind shell.
- Remove or upgrade vulnerable UnrealIRCd installations and verify the installed version.
- Disable anonymous FTP access if it is not required.
- Disable Telnet and use SSH for encrypted remote administration.
- Change default credentials and enforce strong authentication.
- Restrict exposed services using host-based and network firewalls.
- Re-scan the target after remediation to verify that the issues have been addressed.

## Tools Used

- Kali Linux
- Nmap
- Searchsploit
- Metasploit Framework
- Meterpreter
- Netcat
- FTP and Telnet clients
- Oracle VirtualBox

## Skills Practised

Network reconnaissance, service enumeration, vulnerability research, controlled exploitation, Linux command-line investigation, access verification, evidence documentation, and remediation reporting.

## Disclaimer

This repository is for educational and portfolio purposes only. All testing was conducted against an intentionally vulnerable virtual machine in an authorized lab environment. Do not use these techniques against systems without explicit permission.
