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
For root domain, I named it: ADLabs then hit Next and created a password for it and then installed it.
After it restarted, Server Manager opened up. I clicked on Tools and then `Active Directory Users and Computers`. Once it opened up, I pinned it to the task bar.

# Active Directory Account Creation
I clicked on Tools and opened up `Active Directory Administrative Center`, I clicked on my domain name and then I enabled Recycle Bin.
Next, I opened up AD Users and Computers. I clicked on view and selected Advanced Features. I opened up my donmain name and then clicked on users. I right-clicked 
on `administrator` and then hit copy to create a help desk account with admin privileges. 
![image](https://github.com/user-attachments/assets/3aa09aad-7954-4d76-bbee-8e5408a698a8)
To get more information about the account I just created, I used the command: `net user helpdesk /domain` . It showed me information like: 
what group he is part of, when the passwod was last set, if he passwodeds ever expires, and their last logon.

# Join PC to domain, RSAT Tool
I went to the Microsoft page and downloaded a windows 10 ISO. I opened up the Windows 10 Setup once it was done downloading
![image](https://github.com/user-attachments/assets/8ed45424-e6dc-4711-9725-1c1f2682b7f5)
I selected ISO file, and then hit next and saved it.
Once it was downloaded, I opened up Virtualbox and hit New. I named it `Windows 10 Lab` > Next > 5 GB RAM > Next > Next > Install.
I then selected my Windows10 ISO file for the start-up disk. I selected I don't have a product Key and chose Windows 10 Pro because I am trying to add this computer to a domain and you can't do that with Windows 10 Home.
I chose to make the IP of my Server a static IP
I opened  up the control panel > View network status and tasks > Change adapter settigns > Ethernet > Properties > TCP/IPv4 > Use the following aIP addresses
[Insert Picture]   10.1.10.2, 25.0.0.0, 10.1.10.1.                      10.1.10.2, 10.1.10.1 
Next I clicked on the Network settings of my VirtualBox, and folr the `Attached tp:` I changed it from `NAT` to `Host-only Adapter`

Next I wnet back to my Windows 10 computer. Once it was done setting up, I opened File Explorer and right-clicked on `This PC` > Manage > Local Users and Groups > Users.
I right-clicked on Adminstrator > Properties. I checdked Password never expires and unchecked all the other boxes,and hit OK.
I I right-clicked on Adminstrator > Set Password. 
I created a password and then signed out, then logged in as the Adminstrator.
Once logged in, I opened `Computer Mangement` and deleted the `User`account so that I now only had one account on the PC.
I opened the Settings and went to `Apps & features` > Optional features > Add a feature > RSAT Tools: (Active Directory Certificate Service Tools,  AD Domain Services,
DHCP Server Tools, DNS Server Tools, Group Policy, Remote Desktop, Server Manager > Install.
RSAT Tools will give me the ability to Active Directory Users and Services on a Windows 10 desktop.
[Inset pictur of RSAT Tools installed]































