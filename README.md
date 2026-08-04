# Cybersecurity Lab Portfolio

**Candidate:** Asha Latha Panuganti
**Programme:** MSc Cybersecurity, Aston University
**Environment:** Kali Linux attacker VM against isolated laboratory hosts and authorised training targets within controlled environments (private `192.168.x.x` networks and approved remote lab systems).

---

# Overview

This portfolio documents a structured penetration testing workflow carried out exclusively within authorised laboratory environments. Rather than demonstrating familiarity with individual security tools alone, the exercises show the ability to follow a recognised assessment methodology, interpret technical results, and explain how each phase contributes to identifying potential security weaknesses.

The activities are organised according to the standard penetration testing lifecycle:

1. Scanning
2. Enumeration
3. System Hacking

Each screenshot included within this portfolio is accompanied by the command executed, its objective, and an explanation of the resulting output.

---

# 1. Scanning

### Objective

The scanning phase establishes whether target systems are reachable and identifies accessible network services before conducting deeper analysis.

---

## 1.1 TCP SYN Probe – Port Range

**Command**

```bash
hping3 -S 192.168.1.7 -p 1-100
```

### Purpose

This command performs manual TCP SYN probing across ports **1–100**, allowing direct observation of TCP responses instead of relying solely on automated scanners.

### Analysis

The captured responses contain **RST-ACK (RA)** flags.

This indicates:

* The target host is reachable.
* The tested ports are closed or rejecting connections.
* Round-trip time (RTT) values provide basic network latency information.

This demonstrates an understanding of TCP flag behaviour and low-level packet analysis.

**Screenshot**

> hping3 – TCP SYN Probe (Ports 1–100)

---

## 1.2 TCP SYN Probe – Single Port

**Command**

```bash
hping3 -S 192.168.1.1 -p 80
```

### Purpose

A targeted SYN probe against TCP port 80.

### Analysis

The response returns a **SYN-ACK (SA)** flag.

This confirms:

* The host is online.
* Port 80 is open.
* A service is actively listening and available for further enumeration.

This exercise demonstrates the ability to distinguish between TCP response types and correctly interpret port states.

**Screenshot**

> hping3 – TCP SYN Probe (Port 80)

---

# 2. Enumeration

### Objective

Enumeration builds a detailed understanding of discovered systems by identifying hosts, services, applications, network traffic, and local system information.

---

## 2.1 Host Discovery

**Command**

```bash
nmap -sn 192.168.1.1-234
```

### Purpose

Performs a network-wide ping sweep to identify active hosts.

### Analysis

The scan lists all responding systems together with response latency.

This establishes the inventory of reachable devices before more detailed scans are performed.

**Screenshot**

> Nmap – Host Discovery

---

## 2.2 Service and Version Detection

**Command**

```bash
nmap -sV -v --script=nbstat.nse 192.168.1.3
```

### Purpose

Identify running services, determine software versions, and execute the NetBIOS status NSE script.

### Analysis

The scan identifies multiple open TCP ports including:

* 7000
* 8001
* 8002
* 8080
* 9080

Version detection fingerprints the running services while the NSE script gathers additional NetBIOS information.

This provides valuable intelligence for identifying potential attack surfaces and selecting appropriate follow-up testing.

**Screenshot**

> Nmap – Service & Version Detection

---

## 2.3 Network Traffic Analysis

**Capture Filter**

```text
ip.addr == 65.61.137.117
```

### Purpose

Monitor only traffic exchanged with the target host.

### Analysis

The packet capture demonstrates:

* TCP three-way handshake
* HTTP GET requests
* Retrieval of website resources
* Successful HTTP 200 OK responses

Observed requests include:

* /
* /style.css
* /images/logo.gif
* /images/header_pic.jpg

This illustrates how packet analysis reveals application behaviour, page structure, and communication flow.

**Screenshot**

> Wireshark – HTTP Session Analysis

---

## 2.4 Local User and Credential Enumeration

**Commands**

```bash
cat /etc/passwd
cat /etc/shadow
```

### Purpose

Enumerate local user accounts and inspect password storage following authorised access within the laboratory environment.

### Analysis

The `/etc/passwd` file identifies:

* System accounts
* Service accounts
* User IDs
* Group IDs
* Login shells
* Home directories

The `/etc/shadow` file provides information about:

* Password hash presence
* Locked accounts
* Disabled accounts
* Password ageing policies

Understanding account configuration is an important post-access activity because weak credential management can significantly increase organisational risk.

**Screenshots**

> Linux CLI – `/etc/passwd`

> Linux CLI – `/etc/shadow`

---

# 3. System Hacking

### Objective

The final phase demonstrates authorised credential attacks against intentionally vulnerable laboratory services to evaluate password strength and authentication controls.

---

## 3.1 FTP Authentication Testing

**Command**

```bash
medusa -h 192.168.237.129 -u msfadmin -P /usr/share/wordlists/rockyou.txt -M ftp
```

### Purpose

Assess the resilience of an FTP service against weak passwords using a known username and a standard password dictionary.

### Analysis

Medusa systematically attempts authentication using entries from the RockYou wordlist while reporting progress and login attempts.

This exercise demonstrates:

* Automated authentication testing
* Password auditing techniques
* Understanding of service-specific brute-force modules

**Screenshot**

> Medusa – FTP Credential Testing

---

## 3.2 HTTP Login Authentication Testing

**Command**

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt 65.61.137.117 http-post-form \
"/login.jsp:username=admin&password=^PASS^:failed to login"
```

### Purpose

Perform authorised password auditing against a web application's login form.

### Analysis

Hydra automates HTTP POST authentication attempts by identifying unsuccessful login responses using the specified failure string.

The captured output demonstrates successful identification of multiple weak credentials accepted by the intentionally vulnerable application.

This exercise highlights:

* Correct construction of Hydra's HTTP POST syntax
* Understanding of authentication workflows
* Practical password auditing methodology

**Screenshot**

> Hydra – HTTP POST Authentication Testing

---

# Technical Skills Demonstrated

Throughout this portfolio the following practical cybersecurity competencies are demonstrated:

* Manual TCP packet analysis
* Host discovery
* Network reconnaissance
* Service fingerprinting
* NSE scripting
* HTTP traffic analysis
* Packet capture filtering
* Linux system enumeration
* User account analysis
* Password hash inspection
* FTP authentication testing
* Web authentication testing
* Interpretation of network protocols
* Analysis of security assessment outputs

---

# Tools Used

| Tool      | Primary Purpose                             |
| --------- | ------------------------------------------- |
| hping3    | Manual TCP packet crafting and port probing |
| Nmap      | Host discovery and service enumeration      |
| Nmap NSE  | Extended service information gathering      |
| Wireshark | Packet capture and protocol analysis        |
| Medusa    | FTP authentication testing                  |
| Hydra     | HTTP authentication testing                 |
| Linux CLI | Local system enumeration                    |

---

# Key Learning Outcomes

This portfolio demonstrates the ability to:

* Apply a structured penetration testing methodology.
* Interpret TCP/IP behaviour beyond automated tool output.
* Enumerate hosts, services, users, and network communications.
* Analyse packet captures to understand application behaviour.
* Conduct authorised password security assessments.
* Explain technical findings clearly and accurately.
* Document security activities in a professional and reproducible manner.

---

# Ethics and Scope

All activities presented in this portfolio were conducted exclusively within authorised laboratory environments provided for cybersecurity education and training. No testing was performed against unauthorised systems. The techniques demonstrated are intended solely for defensive security assessment, security education, and improving organisational security posture while adhering to recognised ethical and legal standards.

