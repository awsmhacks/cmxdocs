---
layout: default
title: Home
---

### Greetz
Welcome to the CMX docs!  
[General Usage](https://awsmhacks.github.io/cmxdocs/general/general)  
[SMB Command Reference](https://awsmhacks.github.io/cmxdocs/smb/smb-command-ref)  
[Mimikats](https://awsmhacks.github.io/cmxdocs/SMB-Module-Reference#mimikatz)  
[Azure Overview](https://awsmhacks.github.io/cmxdocs/azure/azure)  

If you want to jump right in to CMX   
 
```
#~ apt install pipenv
#~ git clone https://github.com/awsmhacks/CrackMapExtreme /opt/CrackMapExtreme
#~ cd /opt/CrackMapExtreme  
#~ pipenv shell --three   
#~ pip install -r requirements.txt 
#~ python setup.py -q install 
#~ cmx 
```
_assuming you want to install into the /opt directory using default configs_  

  
------------------------------------------------------------------------  

## New Sh!t
Theres a ton of new stuff if you're coming from cme.      

### Some of the stuff I'm most exited about:
- Host/Domain/Full Enum grouped commands  
- New Database feels  
- Interactive Command Shells  
- Keeping up-to-date with Mimikatz  
- UAC Checks  
- Making Registry Changes  
- Disable Defender + auto-AMSI bypass  
- Procdump lsass  
- Azure!   
- Logging output  
- Saving username lists  
- Include password history and/or if accounts are enabled in NTDS Dumps  
- Checking for dualhomed boxes  
- No longer need to copy results backs via shares with wmiexec/smbexec

------------------------------------------------------------------------

## What it does
- Fingerprint Hosts  
- Password Spraying
- Extracts Credentials
- Enumerate Host
- Enumerate Network
- Execute Commands  
- Interactive Command Shell
- Check/Change Remote UAC Status
- Azure Things! External Enum!
- Enumerate users via azure
- Enumerate groups via azure
- Enumerate apps/spns/resource groups/vms/privileges via azure

------------------------------------------------------------------------

  
Also note, there's a ton of half-documented functions / over-used debug statements / and commented out code currently.  
After things smooth out I'll get to fully documenting and cleanup.   