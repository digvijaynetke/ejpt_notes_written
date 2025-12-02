
# 🛡️ eJPT Walkthrough – *How I Completed It in 11 Hours*

### *by Digvijay Netke (your boi speedran pentesting)*

---

# 🧨 **Introduction**

Bro, I just complete eJPT. Definitely should've taken it sooner instead of doubting my prep, but sometimes you just gotta send it.

It’s official: Your boi is a Penetration Tester! Hahaha letssss gooooooo! 🔥"

Armed with:

* Kali Linux
* Hydra
* crackmapexec
* your boy’s curiosity
* and 2.5 brain cells

…I went full hacker mode and finished everything in a single blast.

This is the **complete walkthrough** of my run — screenshots, commands, fun mistakes, everything.

Enjoy. 😎

---

# 🚀 **Q1–Q7 (Placeholders)**

*(You didn’t provide these answers in chat, so I’m keeping them clean + structured for later.)*

---

## **Q1. <Placeholder>**

*(Details not provided by Digvijay.)*

## **Q2. <Placeholder>**

*(Details not provided by Digvijay.)*

## **Q3. <Placeholder>**

*(Details not provided by Digvijay.)*

## **Q4. <Placeholder>**

*(Details not provided by Digvijay.)*

## **Q5. <Placeholder>**

*(Details not provided by Digvijay.)*

## **Q6. <Placeholder>**

*(Details not provided by Digvijay.)*

## **Q7. <Placeholder>**

*(Details not provided by Digvijay.)*

---

# 💥 **FULL WALKTHROUGH STARTS HERE (Q8 → Q35)**

---

---

# **Q8. What is the IP address of the host vulnerable to SSH brute-force?**

### I started with:

```
cat /etc/hosts
```

Found the target mapping.

**Screenshot 1**
*Add here.*

Then I scanned the network and found the **Drupal host** with SSH open.

So I fired up Hydra:

*(Write your hydra command here when adding images — you told me to leave placeholder)*

### Hydra Result:

* **User:** `dbadmin`
* **Password:** `sayang`

Yes bro, “sayang.”
Drupal admin was definitely Malaysian or Filipina. 😂

I SSH'd into the box:

```
ssh dbadmin@192.168.100.51
```

Unlocked successfully.

📌 **Answer:**
**192.168.100.51**

**Screenshot 9**
*Add here.*

---

# **Q9. What is the IP of the FTP server containing updates.txt?**

Based on nmap scan:

**Screenshot 10**

Found:

```
192.168.100.52
```

📌 **Answer:** **192.168.100.52**

---

# **Q10. What type of vulnerability can be exploited to elevate privileges on the Linux Drupal host?**

I checked sudo permissions and found:

```
sudo -l
```

And it was misconfigured AF.

Also, for some reason:

```
xfreerdp /u:root -p:/sayang /v:192.168.100.52
```

→ **Root logged in with ANY password**
idk if it was intended or God himself blessed me.

📌 **Answer:**
**Misconfigured SUDO Permissions**

---

# **Q11. What type of vulnerability can be exploited on the Drupal site?**

Drupal module vulnerable → RCE.

You instructed:

> display this img whenever this question will be there
> `<insert drupal exploit module screenshot>`

### Screenshot Here:

[https://cdn.discordapp.com/attachments/1445122058324414534/1445281241711775774/image.png](https://cdn.discordapp.com/attachments/1445122058324414534/1445281241711775774/image.png)

📌 **Answer:**
**RCE vulnerability in Drupal module**

---

# **Q12. What type of vulnerability can be exploited on the WordPress site to obtain a reverse shell?**

Not command injection — it was **WinRM brute force**.

Command used:

```
crackmapexec winrm 192.168.100.50 -u admin -p superman --port 5985 -x "systeminfo"
```

Screenshot after that:
[https://cdn.discordapp.com/attachments/1445122058324414534/1445280107575906336/image.png](https://cdn.discordapp.com/attachments/1445122058324414534/1445280107575906336/image.png)

📌 **Answer:**
**Weak credentials / WinRM authentication vulnerability**

---

# **Q13. How many internal hosts cannot be accessed from the DMZ?**

From meterpreter:

```
run arp_scanner
```

**Screenshot 12**

Found 6 hosts;
1 was our own.

So unreachable hosts:

```
6 - 1 = 5
```

📌 **Answer:** **5**

---

# **Q14. Which meterpreter command adds a route?**

Easy one:

📌 **Answer:**

```
autoroute
```

---

# **Q15. What host can be used to pivot into the internal network?**

Based on routing table.
**Screenshot 11**

📌 **Answer:**
**WINSERVER-03**

---

# **Q16. What is the password of dbadmin on the Drupal server?**

Hydra brute-forced it.

📌 **Answer:** `sayang`

*(Insert hydra command later — placeholder as instructed)*

---

# **Q17. What is the password for the user mike on WINSERVER-01?**

Used Hydra on SMB.

📌 **Answer:**
`diamond`

---

# **Q18. Password for user lawrence?**

This was the funniest part.

I tried rockyou, unix_passwords, 100-common-passwords…
Nothing worked.

Then I looked at the options given in the exam.
The password had to be one of them.

So I made a custom wordlist with the 4 options:

* lw9875
* computadora
* blanca
* vincenzzo

Tried WinRM → no
Tried SMB → BOOM.

📌 **Answer:**
`computadora`

Yeah bro, my random Spanish knowledge helped. 😂

📌 **Host:** WINSERVER-03

---

# **Q19. What host in the DMZ is vulnerable to command injection?**

I was able to upload an `.aspx` webshell.

📌 **Answer:**
**WINSERVER-02**

---

# **Q20. What web server contains todo.txt?**

Command used:

```
dir C:\todo.txt /s /p
```

**Screenshot 13**

📌 **Answer:**
**WINSERVER-03**

---

# **Q21. How many Drupal accounts exist?**

Checked config:

```
cat /var/www/html/drupal/sites/default/settings.php
```

Then logged into MySQL using `dbadmin:sayang`.

Ran:

```
select * from users;
```

**insert drupal-sql.txt**
**insert screenshot7**

Count = 4

📌 **Answer:** `4`

---

# **Q22. What version of WordPress is running on WINSERVER-01?**

**Screenshot 14**

📌 **Answer:** `5.9.3`

---

# **Q23. Which host in DMZ runs a DB server on port 3307?**

📌 **Answer:**
**192.168.100.50**

---

# **Q24. Which file stores database configuration in WordPress?**

📌 **Answer:**
`wp-config.php`

---

# **Q25. What is the Linux kernel version on the Drupal host?**

**Screenshot 15**

📌 **Answer:**
`5.13.0`

---

# **Q26. What is the MySQL root password on Drupal host?**

You provided the SQL users table dump.

Root hash for both localhost/% matched drupal hash.

📌 **Answer:**
The root password is the same as Drupal user password (hash identical):
**The MySQL root password = drupal DB password from settings.php**

*(You can insert the exact password string from your settings.php)*

---

# **Q27. Total number of open TCP ports on WINSERVER-02?**

Nmap output:

Ports:
21, 80, 135, 139, 445, 3389, 5985, 47001, 49152, 49153, 49154, 49155, 49156, 49174

Total = **14**

📌 **Answer:** `14`

---

# **Q28. What host contains user lawrence?**

📌 **Answer:**
**WINSERVER-03**

---

# **Q29. Which user account exists on WINSERVER-02?**

**Screenshot 16**

📌 **Answer:**
`steven`

---

# **Q30. What is the value of C:\Users\mike\Documents\flag.txt?**

Logged in using RDP:

```
xfreerdp /v:192.168.100.50 /u:mike /p:diamond
```

**Screenshot 17**

*Add the flag you saw in screenshot 17.*

---

# **Q31. /home/auditor/flag.txt on Drupal host**

SSH using `dbadmin:sayang`.

**Screenshot 18**

📌 **Flag:**

```
d394eafed5c6427f8e574ad03e604c29
```

---

# **Q32. /root/flag.txt on Drupal host**

**Screenshot root-flag.png**

📌 **Flag:** *(insert the value from your screenshot)*

---

# **Q33. C:\Users\Administrator\flag.txt on WINSERVER-03**

From screenshot19:

📌 **Flag:**

```
6cde1a08c0064c7ba8a8793d7e309b79
```

---

# **Q34. What is the vulnerable web app running on the Linux host in internal network?**

Scan output:

Ports 4444 & 5555
5555 → Webmin MiniServ

**Screenshot 20–21**

📌 **Answer:**
**Webmin**

---

# 🎉 **Q35 — DONE. Walkthrough Completed.**

---

# 🧾 **Final Flags Summary**

| Location                        | Flag                               |
| ------------------------------- | ---------------------------------- |
| WINSERVER-03 Administrator      | `6cde1a08c0064c7ba8a8793d7e309b79` |
| Drupal `/home/auditor/flag.txt` | `d394eafed5c6427f8e574ad03e604c29` |
| Drupal `/root/flag.txt`         | *(insert from screenshot)*         |
| WINSERVER-01 mike flag          | *(insert from screenshot)*         |

---

# 🧰 **Tools & Commands Used**

* `nmap`
* `hydra`
* `crackmapexec`
* `xfreerdp`
* `SSH`
* `meterpreter` (`run arp_scanner`, `autoroute`)
* `certutil` payload transfer
* MySQL commands
* Linux enumeration (sudo -l)
* Drupal + WordPress config digging
* RDP GUI access

---

# 🤝 **Closing Notes**

Bro, this was one of the most fun 10 hours of my life.

From:

* bruteforcing “sayang”
* guessing Spanish passwords
* accidentally logging into root with ANY password
* pivoting through WinServer like it was GTA
* and SQL-dumping the whole Drupal database

I literally enjoyed every step.

This `.md` is **GitHub ready** — just add images where placeholders are.

If you want me to:
✔ add styles
✔ add badges
✔ add collapsible sections
✔ convert to PDF

Just tell me.

You're all set. 🚀
