
# Searchsploit

**Searchsploit** is part of the Exploit-DB toolkit.  
It lets us quickly search, copy, and use publicly available exploits from the terminal.  
This is super useful during penetration testing when looking for exploits for discovered services or software versions.

---

## Basic Usage
```bash
# Search for exploits related to vsftpd 2.3.4
searchsploit vsftpd 2.3.4

# Update the Searchsploit database
searchsploit -u

# Copy exploit code locally (here exploit ID is 49757)
searchsploit -m 49757
```

## Case Sensitivity

* Searchsploit is **case-sensitive** when using `-c`.
* Example:

  ```bash
  searchsploit -c OpenSSH   # Works
  searchsploit -c openssh   # May fail
  ```

---

## Title-Based Search

```bash
searchsploit -t vsftpd
searchsploit -t bufferoverflow
```

---

## Exact Match in Title

```bash
searchsploit -e "Windows XP"        # Exact match in title
searchsploit --exact "windows exp"  # Alternate syntax
```

You can also combine with `grep`:

```bash
searchsploit -e "Windows XP" | grep -e "microsoft windows"
```

---

## Multi-Keyword Search

```bash
# General exploit searches
searchsploit remote windows smb
searchsploit remote windows buffer

# Web application exploits
searchsploit remote webapps wordpress
searchsploit remote webapps drupal
```

---

## Privilege Escalation

```bash
# Search for local privilege escalation exploits in Windows
searchsploit local windows | grep -e "microsoft"
```

---

## Extra Options

```bash
# Get Exploit-DB online link instead of local path
searchsploit remote windows smb -w | grep -e "Eternal Blue"

# Copy exploit path to working directory
sudo cp /usr/share/exploitdb/exploits/windows/remote/xxxx.py .
```

---

## Practical Takeaways (My Notes)

* Use `searchsploit -u` regularly to keep exploit database updated.
* `-m` is extremely handy to quickly bring an exploit into your working folder.
* `-t` and `-e` help refine searches when results are too broad.
* Combining Searchsploit with `grep` gives much more control.
* Exploits are not plug-and-play → **always read exploit code** to adjust target IP/port/version.
* Works offline (local database), unlike online exploit searches.


