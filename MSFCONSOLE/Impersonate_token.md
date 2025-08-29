# Windows Access Tokens & Privilege Escalation

## What are Access Tokens?
- Core part of Windows authentication, managed by **LSASS (Local Security Authority Subsystem Service)**.
- Think of them as a **temporary key (like a cookie)** that lets a user or process access system resources without re-entering credentials.
- Created when a user logs in (`winlogon.exe` → `userinit.exe`).
- Child processes inherit the parent’s token and privileges.

---

## Types of Tokens
- **Impersonate Token**  
  - Created during non-interactive logins (services, scheduled tasks).  
  - Can be used locally but not on remote systems.  

- **Delegate Token**  
  - Created during interactive logins (RDP, console login).  
  - More dangerous: can be used to impersonate on **any system**.

---

## Required Privileges for Token Abuse
- **SeAssignPrimaryToken** → Impersonate tokens.  
- **SeCreateToken** → Create arbitrary tokens with admin rights.  
- **SeImpersonatePrivilege** → Run a process in another user’s security context (often abused in privilege escalation).

---

## Attack Flow (from screenshots)
1. **Initial Access**  
   - Attacker gains a foothold on the system (e.g., via `meterpreter` session).
   - Run `sysinfo`, `getuid`, and `getprivs` to check privileges.

2. **Privilege Check**  
   - Confirm available privileges like `SeImpersonatePrivilege`.

3. **Token Hunting**  
   - Load `incognito` in meterpreter:  
     ```bash
     meterpreter > load incognito
     meterpreter > list_tokens -u
     ```
   - Lists available **delegation** and **impersonation** tokens.

4. **Token Impersonation**  
   - Use `impersonate_token` to impersonate a higher-privileged account (e.g., Administrator).  
     ```bash
     meterpreter > impersonate_token "DOMAIN\\Administrator"
     ```

5. **Verification**  
   - Check identity with `getuid`.  
   - Attempt actions requiring higher privileges (e.g., `hashdump`).  
   - If denied, SYSTEM-level privileges may still be required.

---

## Key Takeaway
- Access tokens = identity + privileges.  
- Delegate tokens = high risk, as they allow impersonation across systems.  
- Privilege escalation depends on **finding the right token** + **having impersonation privileges**.  
