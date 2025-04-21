# Active-Directory

# Installing Virtulabox and Server 2022
I downloaded virtualbox.
Next I went to Microsoft Evaluation website and downloaded Windows Server 2022 ISO.
Once it was downloaded and setup, I hit start. 
For the Windows Setup I chose the Desktop Experience.

# Installing Active Directory
I started by renaming my server's name to: Server2022. 
![image](https://github.com/user-attachments/assets/afd670d5-8e86-42fe-9b37-6e99ff7ad28c)
I opened Server Manager. I hit Manage > Add Roles and Features > Next > Role-based >  Next > Next.
Select Active Directory Domain Services > Add Features > Next > Next > Install
Once it was done I clicked on `promote this server to a domain controller` > Add a new forest.
For root domain, I named it: ADLabs then hit Next and created a password for it.
