---
layout: default
title: Network Recon
---
# SMB: Network Recon Reference
Created by: @awsmhacks  
Updated: 3/26/20  
CMX Version: 0.0.1.azdev1


**Notes:**
* The following examples assume you have a Kali/Windows host connected to an internal network.
* If you are running from Kali, be sure to escape special chars i.e ($,!)
* OPsec_safe results are from testing against crowdstrike on 4/3/2020. 
* These might not be opsec safe if you are in an environment with ATA or other domain controller watchdogs

Network(Domain) Recon Sections  
  
1. [Users](#users)
2. [Groups](#groups)
3. [Computers](#computers)
4. [Full domain recon - runs all 3](#full-domain-recon)
5. [Dumping Domain Passwords](#)

** If you have DNS resolving the domain controller you wont need to add the -dc switch

Need updates/Still need to document examples:  
X. [Dumping Domain Passwords](#dump)  



------------------------------------------------------------------------
# Users

* Enumerate users

|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| true          | true        | false       | false       | true       |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}

### Command Syntax  
`cmx smb TARGET -u Username -p Pass -dc DC.IP.ADD.RESS -users`

```
> cmx smb 192.168.0.104 -u arianna -p User!23 -dc 192.168.0.100 -users
Mar.26.20 16:37:09  SMB      192.168.0.104:445  SERVER2016A [*] Windows 10.0 Build 14393 x64 (domain:SWAMP) (signing:False) (SMBv:3.0)
Mar.26.20 16:37:09  SMB      192.168.0.104:445  SERVER2016A [+] SWAMP\arianna:User!23
Mar.26.20 16:37:09  SMB      192.168.0.104:445  SERVER2016A [+] Domain Users enumerated
Mar.26.20 16:37:09  SMB      192.168.0.104:445  SERVER2016A         SWAMP Domain User Accounts
Mar.26.20 16:37:09  SMB      192.168.0.104:445  SERVER2016A    SWAMP\Administrator         rid: 500
Mar.26.20 16:37:09  SMB      192.168.0.104:445  SERVER2016A    SWAMP\Guest                 rid: 501
Mar.26.20 16:37:09  SMB      192.168.0.104:445  SERVER2016A    SWAMP\krbtgt                rid: 502
Mar.26.20 16:37:09  SMB      192.168.0.104:445  SERVER2016A    SWAMP\drake                 rid: 1104
Mar.26.20 16:37:09  SMB      192.168.0.104:445  SERVER2016A    SWAMP\wayne                 rid: 1105
Mar.26.20 16:37:09  SMB      192.168.0.104:445  SERVER2016A    SWAMP\eminem                rid: 1106
Mar.26.20 16:37:09  SMB      192.168.0.104:445  SERVER2016A    SWAMP\gucci                 rid: 1107
Mar.26.20 16:37:09  SMB      192.168.0.104:445  SERVER2016A    SWAMP\joyner                rid: 1108
Mar.26.20 16:37:09  SMB      192.168.0.104:445  SERVER2016A    SWAMP\arianna               rid: 1111
Mar.26.20 16:37:09  SMB      192.168.0.104:445  SERVER2016A    SWAMP\OLEN_ROSS             rid: 1115
Mar.26.20 16:37:09  SMB      192.168.0.104:445  SERVER2016A    SWAMP\GINO_FLYNN            rid: 1116
Mar.26.20 16:37:09  SMB      192.168.0.104:445  SERVER2016A    SWAMP\AMY_KEY               rid: 1117
Mar.26.20 16:37:09  SMB      192.168.0.104:445  SERVER2016A    SWAMP\BRITTNEY_FERNANDEZ    rid: 1118
...
...
```











------------------------------------------------------------------------
# Groups

* Enumerate Groups

|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| TRUE          | TRUE        | false       | false       | TRUE       |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}

### Command Syntax  
`cmx smb TARGET -u Username -p Pass -dc DC.IP.ADD.RESS -groups`
  
```
> cmx smb 192.168.0.104 -u arianna -p User!23 -dc 192.168.0.100 -groups
Mar.26.20 16:38:49  SMB      192.168.0.104:445  SERVER2016A [*] Windows 10.0 Build 14393 x64 (domain:SWAMP) (signing:False) (SMBv:3.0)
Mar.26.20 16:38:49  SMB      192.168.0.104:445  SERVER2016A [+] SWAMP\arianna:User!23
Mar.26.20 16:38:49  SMB      192.168.0.104:445  SERVER2016A [+] Domain Groups enumerated
Mar.26.20 16:38:49  SMB      192.168.0.104:445  SERVER2016A        SWAMP Domain Group Accounts
Mar.26.20 16:38:49  SMB      192.168.0.104:445  SERVER2016A    Enterprise Read-only Domain Controllers  membercount: 1
Mar.26.20 16:38:49  SMB      192.168.0.104:445  SERVER2016A    Domain Admins                   membercount: 3
Mar.26.20 16:38:49  SMB      192.168.0.104:445  SERVER2016A    Domain Users                    membercount: 28
Mar.26.20 16:38:49  SMB      192.168.0.104:445  SERVER2016A    Domain Guests                   membercount: 4
Mar.26.20 16:38:49  SMB      192.168.0.104:445  SERVER2016A    Domain Computers                membercount: 27
Mar.26.20 16:38:50  SMB      192.168.0.104:445  SERVER2016A    Domain Controllers              membercount: 6
Mar.26.20 16:38:50  SMB      192.168.0.104:445  SERVER2016A    Schema Admins                   membercount: 8
Mar.26.20 16:38:50  SMB      192.168.0.104:445  SERVER2016A    Enterprise Admins               membercount: 11
Mar.26.20 16:38:50  SMB      192.168.0.104:445  SERVER2016A    Group Policy Creator Owners     membercount: 4
Mar.26.20 16:38:50  SMB      192.168.0.104:445  SERVER2016A    Read-only Domain Controllers    membercount: 2
Mar.26.20 16:38:50  SMB      192.168.0.104:445  SERVER2016A    Cloneable Domain Controllers    membercount: 5
Mar.26.20 16:38:50  SMB      192.168.0.104:445  SERVER2016A    Protected Users                 membercount: 3
Mar.26.20 16:38:50  SMB      192.168.0.104:445  SERVER2016A    DnsUpdateProxy                  membercount: 6
Mar.26.20 16:38:50  SMB      192.168.0.104:445  SERVER2016A    AM-guillermh-distlist           membercount: 3
Mar.26.20 16:38:50  SMB      192.168.0.104:445  SERVER2016A    jo-teamojorg-distlist           membercount: 5
Mar.26.20 16:38:50  SMB      192.168.0.104:445  SERVER2016A    TO-Hersheyki-admingroup         membercount: 3
Mar.26.20 16:38:50  SMB      192.168.0.104:445  SERVER2016A    PH-401557491-distlist           membercount: 2
Mar.26.20 16:38:50  SMB      192.168.0.104:445  SERVER2016A    BE-619904351-distlist           membercount: 4
Mar.26.20 16:38:50  SMB      192.168.0.104:445  SERVER2016A    wa-cut-admingroup               membercount: 5
Mar.26.20 16:38:50  SMB      192.168.0.104:445  SERVER2016A    NE-270-distlist                 membercount: 5
Mar.26.20 16:38:50  SMB      192.168.0.104:445  SERVER2016A    BE-jk2247hqa-distlist           membercount: 6
Mar.26.20 16:38:50  SMB      192.168.0.104:445  SERVER2016A    39-arl-distlist                 membercount: 0
```


* Get users of a Particular Group

|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| TRUE          | TRUE        | false       | false       | TRUE       |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}

### Command Syntax  
`cmx smb TARGET -u Username -p Pass -dc DC.IP.ADD.RESS -group "GROUP NAME"`

```
> cmx smb 192.168.0.104 -u arianna -p User!23 -dc 192.168.0.100 -group "Domain Controllers"
Mar.26.20 16:39:55  SMB      192.168.0.104:445  SERVER2016A [*] Windows 10.0 Build 14393 x64 (domain:SWAMP) (signing:False) (SMBv:3.0)
Mar.26.20 16:39:55  SMB      192.168.0.104:445  SERVER2016A [+] SWAMP\arianna:User!23
Mar.26.20 16:39:55  SMB      192.168.0.104:445  SERVER2016A [+] "Domain Controllers" Domain Group Found in SWAMP
Mar.26.20 16:39:55  SMB      192.168.0.104:445  SERVER2016A        "Domain Controllers" Group Info
Mar.26.20 16:39:55  SMB      192.168.0.104:445  SERVER2016A    Member Count: 6
Mar.26.20 16:39:55  SMB      192.168.0.104:445  SERVER2016A    SWAMP\DC2012A$
Mar.26.20 16:39:55  SMB      192.168.0.104:445  SERVER2016A    SWAMP\Administrator
Mar.26.20 16:39:55  SMB      192.168.0.104:445  SERVER2016A    SWAMP\arianna
Mar.26.20 16:39:56  SMB      192.168.0.104:445  SERVER2016A    SWAMP\TONIA_BRADY
Mar.26.20 16:39:56  SMB      192.168.0.104:445  SERVER2016A    SWAMP\YESENIA_CARLSON
Mar.26.20 16:39:56  SMB      192.168.0.104:445  SERVER2016A    SWAMP\MARVA_ONEILL
```


* Get all groups and their users

Note: Doesnt grab nested groups.

|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| TRUE          | TRUE        | false       | false       | TRUE       |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}

### Command Syntax  
`cmx smb TARGET -u Username -p Pass -dc DC.IP.ADD.RESS -groups-full`

```
> cmx smb 192.168.0.104 -u arianna -p User!23 -dc 192.168.0.100 -groups-full
Mar.26.20 16:41:16  SMB      192.168.0.104:445  SERVER2016A [*] Windows 10.0 Build 14393 x64 (domain:SWAMP) (signing:False) (SMBv:3.0)
Mar.26.20 16:41:16  SMB      192.168.0.104:445  SERVER2016A [+] SWAMP\arianna:User!23
Mar.26.20 16:41:16  SMB      192.168.0.104:445  SERVER2016A [+] Domain Groups enumerated
Mar.26.20 16:41:16  SMB      192.168.0.104:445  SERVER2016A        SWAMP Domain Group Accounts
Mar.26.20 16:41:16  SMB      192.168.0.104:445  SERVER2016A    Enterprise Read-only Domain Controllers  membercount: 1
Mar.26.20 16:41:16  SMB      192.168.0.104:445  SERVER2016A         SWAMP\572543SA
Mar.26.20 16:41:16  SMB      192.168.0.104:445  SERVER2016A    Domain Admins                   membercount: 3
Mar.26.20 16:41:16  SMB      192.168.0.104:445  SERVER2016A         SWAMP\Administrator
Mar.26.20 16:41:16  SMB      192.168.0.104:445  SERVER2016A         SWAMP\eminem
Mar.26.20 16:41:16  SMB      192.168.0.104:445  SERVER2016A         SWAMP\392648SA
Mar.26.20 16:41:16  SMB      192.168.0.104:445  SERVER2016A    Domain Users                    membercount: 28
Mar.26.20 16:41:16  SMB      192.168.0.104:445  SERVER2016A         SWAMP\Administrator
Mar.26.20 16:41:16  SMB      192.168.0.104:445  SERVER2016A         SWAMP\krbtgt
Mar.26.20 16:41:16  SMB      192.168.0.104:445  SERVER2016A         SWAMP\drake
Mar.26.20 16:41:16  SMB      192.168.0.104:445  SERVER2016A         SWAMP\wayne
Mar.26.20 16:41:16  SMB      192.168.0.104:445  SERVER2016A         SWAMP\eminem
Mar.26.20 16:41:16  SMB      192.168.0.104:445  SERVER2016A         SWAMP\gucci
Mar.26.20 16:41:16  SMB      192.168.0.104:445  SERVER2016A         SWAMP\joyner
Mar.26.20 16:41:16  SMB      192.168.0.104:445  SERVER2016A         SWAMP\arianna
Mar.26.20 16:41:16  SMB      192.168.0.104:445  SERVER2016A         SWAMP\OLEN_ROSS
Mar.26.20 16:41:16  SMB      192.168.0.104:445  SERVER2016A         SWAMP\GINO_FLYNN
Mar.26.20 16:41:16  SMB      192.168.0.104:445  SERVER2016A         SWAMP\AMY_KEY
Mar.26.20 16:41:16  SMB      192.168.0.104:445  SERVER2016A         SWAMP\BRITTNEY_FERNANDEZ
Mar.26.20 16:41:16  SMB      192.168.0.104:445  SERVER2016A         SWAMP\TERRELL_WIGGINS
Mar.26.20 16:41:16  SMB      192.168.0.104:445  SERVER2016A         SWAMP\BEAU_CALDERON
Mar.26.20 16:41:16  SMB      192.168.0.104:445  SERVER2016A         SWAMP\TONIA_BRADY
Mar.26.20 16:41:16  SMB      192.168.0.104:445  SERVER2016A         SWAMP\PHILIP_BARRETT
Mar.26.20 16:41:17  SMB      192.168.0.104:445  SERVER2016A         SWAMP\DINA_POTTER
Mar.26.20 16:41:17  SMB      192.168.0.104:445  SERVER2016A         SWAMP\NADIA_BRADFORD
Mar.26.20 16:41:17  SMB      192.168.0.104:445  SERVER2016A         SWAMP\YESENIA_CARLSON
Mar.26.20 16:41:17  SMB      192.168.0.104:445  SERVER2016A         SWAMP\392648SA
Mar.26.20 16:41:17  SMB      192.168.0.104:445  SERVER2016A         SWAMP\ALANA_WATSON
Mar.26.20 16:41:17  SMB      192.168.0.104:445  SERVER2016A         SWAMP\JO_COOKE
Mar.26.20 16:41:17  SMB      192.168.0.104:445  SERVER2016A         SWAMP\572543SA
Mar.26.20 16:41:17  SMB      192.168.0.104:445  SERVER2016A         SWAMP\ANNA_FITZGERALD
Mar.26.20 16:41:17  SMB      192.168.0.104:445  SERVER2016A         SWAMP\NELDA_BARRETT
Mar.26.20 16:41:17  SMB      192.168.0.104:445  SERVER2016A         SWAMP\MARVA_ONEILL
Mar.26.20 16:41:17  SMB      192.168.0.104:445  SERVER2016A         SWAMP\BETTYE_KAUFMAN
Mar.26.20 16:41:17  SMB      192.168.0.104:445  SERVER2016A         SWAMP\OTHER$
Mar.26.20 16:41:17  SMB      192.168.0.104:445  SERVER2016A    Domain Guests                   membercount: 4
Mar.26.20 16:41:17  SMB      192.168.0.104:445  SERVER2016A         SWAMP\Guest
Mar.26.20 16:41:17  SMB      192.168.0.104:445  SERVER2016A         SWAMP\WIN10B$
Mar.26.20 16:41:17  SMB      192.168.0.104:445  SERVER2016A         SWAMP\BETTYE_KAUFMAN
Mar.26.20 16:41:17  SMB      192.168.0.104:445  SERVER2016A [-]     Member with SID 1142 might be a group
...
...
```













------------------------------------------------------------------------
# Computers

* Enumerate Domain Computers

|---------------+-------------+-------------+-------------+------------|
| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| TRUE          | TRUE        | false       | false       | TRUE       |
|---------------+-------------+-------------+-------------+------------|
{: .tablelines}

### Command Syntax  
`cmx smb TARGET -u Username -p Pass -dc DC.IP.ADD.RESS -computers`

  
```
> cmx smb 192.168.0.104 -u arianna -p User!23 -dc 192.168.0.100 -computers
Mar.26.20 16:42:47  SMB      192.168.0.104:445  SERVER2016A [*] Windows 10.0 Build 14393 x64 (domain:SWAMP) (signing:False) (SMBv:3.0)
Mar.26.20 16:42:47  SMB      192.168.0.104:445  SERVER2016A [+] SWAMP\arianna:User!23
Mar.26.20 16:42:47  SMB      192.168.0.104:445  SERVER2016A [+] Domain Controllers enumerated
Mar.26.20 16:42:47  SMB      192.168.0.104:445  SERVER2016A          SWAMP Domain Controllers
Mar.26.20 16:42:47  SMB      192.168.0.104:445  SERVER2016A    DC2012A$                rid: 1001

Mar.26.20 16:42:47  SMB      192.168.0.104:445  SERVER2016A [+] Domain Computers enumerated
Mar.26.20 16:42:47  SMB      192.168.0.104:445  SERVER2016A          SWAMP Domain Computer Accounts
Mar.26.20 16:42:47  SMB      192.168.0.104:445  SERVER2016A    WIN7A$                  rid: 1109
Mar.26.20 16:42:47  SMB      192.168.0.104:445  SERVER2016A    WIN10A$                 rid: 1110
Mar.26.20 16:42:47  SMB      192.168.0.104:445  SERVER2016A    SERVER2016A$            rid: 1112
Mar.26.20 16:42:47  SMB      192.168.0.104:445  SERVER2016A    SERVER2019A$            rid: 1113
Mar.26.20 16:42:47  SMB      192.168.0.104:445  SERVER2016A    WIN10B$                 rid: 1114
Mar.26.20 16:42:47  SMB      192.168.0.104:445  SERVER2016A    ESMWCTRX1000000$        rid: 1143

```



