---
layout: default
title: Azure Overview
---

# Azure Overview
Looking for more usernames?  Want to enum the network but afraid it will get picked up by `GENERIC AV`?  
![Azure Enum](/cmxdocs/imgs/azureenum.gif "Sry 4 the sh!ty gif")

Did you know if your target uses o365 then you can probably enumerate quite a bit of info without 
even being on the network!  
O365 enables features companies might not be aware of, even if you don't 'turn on' anything with azureAD.  

## Get Started
To get started you _will_ need a valid set of credentials.  
These are used to setup our azure session.  
  
### Establish a Session
Running `cmx az --config` will setup or re-setup an azure session.    
This will open a browser and prompt for the user credentials  
or if over an ssh connection prompts the user to follow a link and enter a Code.  

![Azure Configure](/cmxdocs/imgs/azureconfig.png "Azure Config")
Visit the link in a browser to sign-in  
![Azure Enter Code](/cmxdocs/imgs/azureentercode.png "Azure Enter Code")

After entering the code you will be taken to the targets sign-in page where you enter credentials.  
CMX will wait until the session is established, verify the session, and inform you when its ready.   

![Azure Configured](/cmxdocs/imgs/azureconfigd.png "Azure Configured")



#### Session Errors
CMX will auto-detect if you have a current session and let you know if you need to setup/re-establish a session.  
![Azure Not Configured](/cmxdocs/imgs/azurenotconfigured.png "Azure Not Configured")

_Note: Azure configuration, access tokens, and profile information can be found in the ~/.azure/ directory after establishing a session._  














   
  
--------------------------------------------------------------------------------------------------------------------------------------------------------
