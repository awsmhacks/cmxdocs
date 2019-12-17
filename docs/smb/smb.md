---
layout: default
title: SMB Overview
---

TODO



# Command Reference Guides 

Several Reference Guides exist with the intention of quickly providing  
examples of command and the required formats.  

  
Each section uses a table with some or all of the following items.   

| Multiple_Host | Requires DC | Requires LA | Requires DA | Opsec_safe |
|---------------|-------------|-------------|-------------|------------|
| true          | true        | false       | false       | false      |
  
  
Multiple Host = can be run on multiple targets, i.e 192.168.1.0/24  
Requires DC = Only works if you target a Domain Controller      
Requires LA = Creds need to have local admin on target  
Requires DA = Creds needs to be domain admin or equivilant   
Opsec_safe = If there is a chance this is easily caught, leaves remnants, or gets flagged in logs this is false.  
  - - - - - and this depends on your opinion of 'opsec'   
  
CMX will autodetect (in most cases) if you dont have the required privilege level or targets.     
  
--------------------------------------------------------------------------------------------------------------------------------------------------------
