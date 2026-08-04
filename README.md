Cybersecurity Lab Portfolio — README

Candidate: Asha Latha Panuganti Focus: MSc Cybersecurity, Aston University Environment: Kali Linux attacker VM against isolated lab hosts / authorised training targets (local 192.168.x.x network and controlled remote lab endpoints)

Reviewer's note

As a hiring manager, what I look for in a candidate's lab portfolio isn't just "ran a tool" — it's whether the candidate understands why a technique sits where it does in an assessment, and can read and explain the output. This README walks through each screenshot in that order: the phase it belongs to, the exact command run, and what the output tells us. It follows the standard penetration-testing flow: Scanning → Enumeration → System Hacking.

1. Scanning

Goal: confirm a target/port is live and responsive before committing further effort.

1.1 SYN Probe — Port Range
hping3 -S 192.168.1.7 -p 1-100
Sends crafted TCP SYN packets across ports 1–100 on the target.
Output shows flags=RA (Reset-ACK) responses with RTT ~2000ms — indicates the probed ports are closed/filtered but the host is reachable.
Screenshot: hping3 — SYN Probe (Port Range 1–100)
1.2 SYN Probe — Single Port
hping3 -S 192.168.1.1 -p 80
Targeted SYN probe against port 80 specifically.
Output shows flags=SA (SYN-ACK) responses — confirms port 80 is open and the service is actively listening.
Screenshot: hping3 — SYN Probe (Port 80)

What this demonstrates: manual packet crafting (rather than relying solely on Nmap), and the ability to read raw flag responses (RA vs SA) to determine port state.

2. Enumeration

Goal: build a detailed picture of what's running, who has access, and how the application behaves.

2.1 Host Discovery
nmap -sn 192.168.1.1-234
Ping sweep across the full subnet range.
Output lists every responding host (192.168.1.1 through .17+ shown), each with round-trip latency — establishes the live-host inventory before deeper scanning.
Screenshot: Nmap — Host Discovery / Ping Sweep
2.2 Service & Version Detection
nmap -sV -v --script=nbstat.nse 192.168.1.3
Version-detection scan combined with the nbstat NSE script against a specific live host.
Output identifies five open ports (7000, 8001, 8002, 8080, 9080/tcp) and fingerprints the services behind them.
Screenshot: Nmap — Service/Version Scan with NSE Scripting
2.3 Traffic & Session Enumeration
# Wireshark capture filter
ip.addr==65.61.137.117
Live packet capture filtered to isolate traffic to/from the target.
Output shows the TCP three-way handshake (SYN → SYN/ACK → ACK) followed by sequential HTTP GET requests (/, /style.css, /images/logo.gif, /images/header_pic.jpg) each returning 200 OK — reveals the application's page structure and asset requests.
Screenshot: Wireshark — HTTP Session Capture & Filtering
2.4 Exploitation: Local Account & Hash Enumeration
cat /etc/passwd
cat /etc/shadow
Read directly on the target/compromised host.
/etc/passwd output enumerates every system and service account, its UID/GID, home directory, and login shell (e.g. root, daemon, www-data, mysql, _gophish, redis, etc.).
/etc/shadow output shows each account's password-hash field state (!, *, or a hash) and aging/expiry values — indicating which accounts are lockable, disabled, or password-set.
Screenshots: Linux CLI — /etc/passwd Enumeration, Linux CLI — /etc/shadow Enumeration

What this demonstrates: the candidate moves from network-level enumeration (hosts, ports, services) to application-level (traffic/session flow) to host-level (local accounts and credential storage) — a complete, layered enumeration approach.

3. System Hacking

Goal: gain access to a target service using discovered/weak credentials.

3.1 FTP Brute-Force
medusa -h 192.168.237.129 -u msfadmin -P /usr/share/wordlists/rockyou.txt -M ftp
Automated credential attack against an FTP service, testing a known username (msfadmin) against the rockyou.txt wordlist.
Output logs each password attempt with timestamp and progress count against the 14,344,390-entry wordlist.
Screenshot: Medusa — FTP Service Brute-Force
3.2 Web Login Form Brute-Force
hydra -l admin -P /usr/share/wordlists/rockyou.txt 65.61.137.117 http-post-form \
  "/login.jsp:username=admin&password=^PASS^:failed to login"
Automated credential attack against a web application's login form (HTTP POST), testing the admin account against rockyou.txt.
Output confirms the run completed with 16 valid passwords found for the target account (e.g. password, nicole, 12345, iloveyou, rockyou, princess, etc.).
Screenshot: Hydra — HTTP POST-Form Brute-Force

What this demonstrates: correct use of two different brute-force tools against two different protocols (FTP service vs. web application form), including correctly structuring Hydra's http-post-form syntax with a failure-condition string.

Tools reference
Tool	Purpose in this portfolio
hping3	Manual TCP SYN packet crafting / port probing
nmap (+NSE)	Host discovery, service/version detection
wireshark	Packet capture and traffic/session analysis
medusa	FTP credential brute-forcing
hydra	Web login form (HTTP POST) credential brute-forcing
Linux CLI (cat)	Local account and password-hash file enumeration
Ethics & scope note

All activity shown was performed against an isolated home lab network and controlled training targets for educational purposes. None of the commands, wordlists, or techniques here were used against systems without authorisation.
