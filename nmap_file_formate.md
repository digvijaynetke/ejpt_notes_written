# NMAP file formate


3 major formate 
### -oA

### -oN <- normal formate
### -OX <- xml format that store..offfers a conversion layer import into metasploit framework'
### -oG <- grepable formate

> nmap -Pn -sS -F -T4 <IP> -oN nmap_normal.txt
same as in terminal



> nmap -Pn -sS -F -T4 <IP> -oX nmap_XML.xml


import scan result in metasploit
ensure service postgresqll must be start

metasploit requeries connect to postgresql server
```
msfconsole
workspace
workspace -h
```
help for workspace

then create new workspace
workspace -a pentest_1
db_status   <- to check postgresql connected or not
db_import "file pathh"

use " hosts " to see the port scaned host
"services" top seee all <- sV

>db_nmap -Pn -sS -sV -O -p445 <IP>
scan results will automatically save with the current workspace of metaspoit framework 


we can also see the avaliable vulnarability by
vulns <- 

hosts




*-oG*
nmap -Pn -sS -F -T4 <IP> -oG nmap_grep.txt

```egrep -v "^#Status: up" nmap_grep.txt | cut -d' ' -f2,4- | sed -n -e 's/Ignored.*//p' | awk '
{print "Host: " $1 " Ports: NF-1; $1=""; for(i=2; i<=Ns/%-7s %s\n", v[2],v[3],v[1] , v[5]}; a=""}'







