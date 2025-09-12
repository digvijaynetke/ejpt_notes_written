
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
#
#
#
#




# Cross-compiling Exploits
> **Scope / warning:** Cross-compiling and testing exploits must only be done on targets you are explicitly authorized to test. Misuse is illegal and unethical.

---

### Short theory 
**Cross-compiling** = compiling source code on one platform (host) to produce a binary that runs on a *different* target platform/OS/architecture (e.g., compiling on Linux to make a Windows `.exe` or a different CPU architecture binary).
Why: you often find exploit C code but your attacker VM is Linux — target is Windows — so you need a Windows binary.

---

## Tooling & concepts

* **MinGW-w64** (or `i686-w64-mingw32` / `x86_64-w64-mingw32` toolchains) — used to compile Windows PE binaries from Linux.
* **gcc** for native Linux binaries.
* **Linker flags** (e.g. `-lws2_32`, `-lpthread`, `-lcrypt`) resolve platform-specific libraries the exploit depends on.
* **Architecture selection**: use `i686-*` for 32-bit, `x86_64-*` for 64-bit Windows.
* **Static vs dynamic linking**: `-static` can help remove runtime dependencies but increases binary size and sometimes breaks behavior.

---

## Practical walkthrough — Windows exploit (MinGW)

Example: you found an exploit `9303.c` for VideoLAN VLC 0.86 (hypothetical). Steps:

1. **Install cross-compiler**

```bash
sudo apt update
sudo apt install mingw-w64 gcc   # Debian/Ubuntu example
```

2. **Copy exploit locally**

```bash
searchsploit -m 9303
# or manually copy exploit C file to working dir
```

3. **Compile for 32-bit Windows**

```bash
i686-w64-mingw32-gcc 9303.c -o exploit.exe
```

**What this does:**

* `i686-w64-mingw32-gcc` is a GCC frontend that produces 32-bit Windows PE executables.
* `9303.c` → compiled & linked into `exploit.exe`.
* The produced file is a Windows `.exe` that (in theory) can be run on 32-bit Windows or 64-bit Windows compatible with 32-bit apps (WoW64).

4. **Compile and link Windows socket library (if exploit uses sockets)**

```bash
i686-w64-mingw32-gcc 9303.c -o exploit.exe -lws2_32
```

**Why `-lws2_32`?**

* `ws2_32` is the Windows Winsock 2 library (socket APIs: `socket`, `connect`, `recv`, `send`, etc.). If the exploit opens sockets (downloads payload, connects back, etc.) the linker must resolve those symbols. `-lws2_32` tells the linker to link against that library.

5. **Compile for 64-bit Windows**

```bash
x86_64-w64-mingw32-gcc 9303.c -o exploit_x64.exe -lws2_32
```

6. **Optional: static link** (reduce external runtime dependency)

```bash
i686-w64-mingw32-gcc 9303.c -o exploit.exe -lws2_32 -static
```

Be cautious: static may fail if system libraries are not available for static linking, or it may change runtime behavior.

7. **Transfer and run on target Windows** (only on authorized test target)

* Use `python -m http.server` to host `exploit.exe` and download on target, or copy via SMB/USB/other allowed method.
* Run in an instrumented environment (VM, snapshot) and observe.

---

## Practical walkthrough — Linux exploit (native compile)

Example: Dirty COW (`4039.c` in ExploitDB).

1. **Copy exploit & compile with required flags**

```bash
searchsploit -m 4039
gcc 4039.c -o dirtycow_exploit -lpthread -lcrypt
```

**Explanation:**

* `-lpthread` links pthreads (POSIX threads) if exploit uses threading.
* `-lcrypt` links the crypto/`crypt(3)` library if the code uses password hashing functions.
* After compile you get an ELF binary `dirtycow_exploit` which you can run on a compatible Linux kernel/architecture.

2. **If the exploit requires 32-bit userland on 64-bit host**, compile with `-m32` and install `libc6-dev-i386` / multilib support:

```bash
gcc -m32 4039.c -o dirtycow32 -lpthread -lcrypt
# may need: sudo apt install gcc-multilib libc6-dev-i386
```

---

## Common compile flags & why they matter (quick)

* `-o <file>` → output filename.
* `-l<name>` → link library `lib<name>.so` / `lib<name>.a` (Windows: `libws2_32.a` provides Winsock symbols).
* `-m32` / `-m64` → force 32-bit / 64-bit target (host must support multilib).
* `-static` → produce statically linked binary.
* `-fno-stack-protector` / `-z execstack` → sometimes needed to compile exploit PoCs that rely on disabled protections (only for controlled testbeds). **Use with caution**.
* `-O0` / `-g` → disable optimizations and add debug info while developing/fixing.

---

## Troubleshooting / fixing exploits after compile

* **Undefined references**: add `-l...` for missing libs (look at undefined symbol names).
* **Wrong architecture**: `file exploit.exe` / `file dirtycow_exploit` to check binary type. Ensure target architecture matches.
* **Runtime crashes**: run under debugger (gdb, windbg) or instrumented VM to see crash addresses and required adjustments.
* **Exploit expects hardcoded offsets/addresses**: you may need to patch those constants for the target application version.
* **EDB notes**: always read exploit notes for prerequisites (specific target version, required environment, dependencies).

---

## Cross-compile checklist (copy-paste)

```text
1. Identify target OS & arch (win/linux, x86/x64/arm).
2. Get exploit C source and read EDB notes.
3. Install appropriate toolchain:
   - Windows: sudo apt install mingw-w64
   - Linux native: gcc (or multilib for -m32)
4. Compile:
   - Windows 32-bit: i686-w64-mingw32-gcc exploit.c -o exploit.exe -lws2_32
   - Windows 64-bit: x86_64-w64-mingw32-gcc exploit.c -o exploit_x64.exe -lws2_32
   - Linux: gcc exploit.c -o exploit -lpthread -lcrypt
   - For 32-bit on 64-bit host: gcc -m32 exploit.c -o exploit32 ...
5. Check binary type: file exploit.exe / file exploit
6. If missing symbols: add -l<lib> or adjust compile command
7. Test in an isolated VM that matches the target environment
8. If exploit fails: debug (gdb/windbg), read code, modify and recompile
9. Document changes and keep original source backed up
```

---

## Practical takeaways (my notes)

* Cross-compiling lets you produce Windows payloads from Linux; MinGW-w64 is the go-to toolchain.
* Linking `-lws2_32` is necessary when code uses Winsock functions (sockets on Windows).
* For Linux PoCs, link the libs the source expects (`-lpthread`, `-lcrypt`).
* Some PoCs require changing compiler flags (disable stack protector, executable stack) — only in safe test labs.
* Always test in a VM matching the target OS/version, and read EDB notes for version-specific adjustments.
* Keep a reproducible compile command and note any patches applied to the exploit source.
