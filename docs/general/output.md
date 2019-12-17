---
layout: default
title: Output Overview
---

*this page still needs updates*    

[Output](#output)  
[Saving Results](#saving-results)  
[Saving output](#saving-output)  
[Results Database](#results-database)  
[Troubleshooting](#no-output)  

  
### Output
CMX will always output the fingerprint of target it connects with, followed by results.  
  
The output follows the general format:  
`date  protocol     targetIP:port   targethostname(or IP if unknown)  [indicator] Message`  
  
Indicators:  
`[*]` for informational messages  
`[-]` for failure messages  
`[+]` for success messages  
`[!]` for warning messages  
    
Example checking for null sessions:  
```
#~ cmx smb 10.10.33.120-127 -u '' -p '' 

Aug.20.19 12:20:15  SMB         10.10.33.122:445  10.10.33.122 [*] Windows Server 2012 R2 Datacenter 9600 x64 (domain:OCEAN) (signing:False) (SMBv:1)
Aug.20.19 12:20:15  SMB         10.10.33.121:445  10.10.33.121 [*] Windows Server 2012 R2 Datacenter 9600 x64 (domain:OCEAN) (signing:False) (SMBv:1)
Aug.20.19 12:20:15  SMB         10.10.33.124:445  10.10.33.124 [*] Windows 7 Ultimate 7601 Service Pack 1 x64 (domain:OCEAN) (signing:False) (SMBv:1)
Aug.20.19 12:20:15  SMB         10.10.33.122:445  10.10.33.122 [-] OCEAN\: STATUS_ACCESS_DENIED 
Aug.20.19 12:20:15  SMB         10.10.33.121:445  10.10.33.121 [+] OCEAN\: 
Aug.20.19 12:20:15  SMB         10.10.33.123:445  10.10.33.123 [*] Windows 6.1 Build 7601 x64 (domain:OCEAN) (signing:True) (SMBv:2.1)
Aug.20.19 12:20:15  SMB         10.10.33.125:445  10.10.33.125 [*] Windows 10.0 Build 17763 x64 (domain:OCEAN) (signing:False) (SMBv:3.0)
Aug.20.19 12:20:15  SMB         10.10.33.124:445  10.10.33.124 [+] OCEAN\: 
Aug.20.19 12:20:15  SMB         10.10.33.125:445  10.10.33.125 [-] OCEAN\: STATUS_ACCESS_DENIED 
Aug.20.19 12:20:15  SMB         10.10.33.123:445  10.10.33.123 [+] OCEAN\: 
Aug.20.19 12:20:21         [!] Could not connect to 10.10.33.127, no route to host. Can you ping it? [!]
Aug.20.19 12:20:21         [!] Could not connect to 10.10.33.126, no route to host. Can you ping it? [!]

```

### Saving results
CMX can also save just the results of a given command using the --save switch
Use case: store the usernames gathered during enum for more password spraying.  
```
#~ cmx smb 192.168.1.110 -u sgomez -p User\!23 --users --save
Dec.17.19 15:49:20  SMB      192.168.1.110:445  WIN10-1903 [*] Windows 10.0 Build 18362 x64 (domain:NAILED) (signing:False) (SMBv:3.0)
Dec.17.19 15:49:20  SMB      192.168.1.110:445  WIN10-1903 [+] NAILED\sgomez:User!23 (Pwn3d!)
Dec.17.19 15:49:20  SMB      192.168.1.110:445  WIN10-1903 [+] Domain Users enumerated
Dec.17.19 15:49:20  SMB      192.168.1.110:445  WIN10-1903      NAILED Domain User Accounts
Dec.17.19 15:49:20  SMB      192.168.1.110:445  WIN10-1903 NAILED\Administrator         rid: 500
Dec.17.19 15:49:20  SMB      192.168.1.110:445  WIN10-1903 NAILED\Guest                 rid: 501
Dec.17.19 15:49:20  SMB      192.168.1.110:445  WIN10-1903 NAILED\krbtgt                rid: 502
Dec.17.19 15:49:20  SMB      192.168.1.110:445  WIN10-1903 NAILED\sgomez                rid: 1104
Dec.17.19 15:49:20  SMB      192.168.1.110:445  WIN10-1903 NAILED\agrande               rid: 1105
Dec.17.19 15:49:20  SMB      192.168.1.110:445  WIN10-1903 NAILED\nminaj                rid: 1108
Dec.17.19 15:49:20  SMB      192.168.1.110:445  WIN10-1903 NAILED\drake                 rid: 1110
Dec.17.19 15:49:20  SMB      192.168.1.110:445  WIN10-1903 [+] Usernames saved to: NAILED-users.txt

```
TODO: implement everywhere.  
Currently works for smb --users and az --users  
  
### Saving output
CMX has the ability to log all ino from a given command.  
Add the --log switch to the end of any command to store the output.  
  
TODO: implement everywhere  

### Results Database
Many commands that enumerate targets will populate the cmx database (cmxdb)  
  

### No output?
If you arent getting any output or it seems like CMX is just hanging try appending the --debug switch to the command.  
If it seems like its just hanging try adjusting the --timeout switch.  