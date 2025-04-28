# Active-Directory

# Installing VirtualBox and Server 2022
I downloaded VirtualBox.
Next, I went to the Microsoft Evaluation website and downloaded Windows Server 2022 ISO.
Once it was downloaded and set up, I hit start. 
For the Windows Setup, I chose the Desktop Experience.

# Installing Active Directory
I started by renaming my server's name to Server2022. 
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
what group he is part of, when the password was last set, if he passwodeds ever expires, and their last logon.

# Join PC to domain, RSAT Tool
I went to the Microsoft page and downloaded a windows 10 ISO. I opened up the Windows 10 Setup once it was done downloading
![image](https://github.com/user-attachments/assets/8ed45424-e6dc-4711-9725-1c1f2682b7f5)
I selected ISO file, and then hit next and saved it.
Once it was downloaded, I opened up Virtualbox and hit New. I named it `Windows 10 Lab` > Next > 5 GB RAM > Next > Next > Install.
I then selected my Windows10 ISO file for the start-up disk. I selected I don't have a product Key and chose Windows 10 Pro because I am trying to add this computer to a domain and you can't do that with Windows 10 Home.
I chose to make the IP of my Server a static IP
I opened  up the control panel > View network status and tasks > Change adapter settigns > Ethernet > Properties > TCP/IPv4 > Use the following aIP addresses
![image](https://github.com/user-attachments/assets/77525dda-b523-4b57-9d71-573b66f9342b)
Next, I clicked on the Network settings of my VirtualBox, and for the `Attached to:` I changed it from `NAT` to `Host-only Adapter`

Next, I went back to my Windows 10 computer. Once it was done setting up, I opened File Explorer and right-clicked on `This PC` > Manage > Local Users and Groups > Users.
I right-clicked on Administrator> Properties, checked Password never expires, unchecked all the other boxes,and hit OK.
I right-clicked on Administrator> Set Password. 
I created a password, signed out, and logged in as the Administrator.
Once logged in, I opened `Computer Mangement` and deleted the `User` account so that I now only had one account on the PC.
I renamed the computer to: Desktop 1.
I opened the Settings and went to `Apps & features` > Optional features > Add a feature > RSAT Tools: (Active Directory Certificate Service Tools,  AD Domain Services,
DHCP Server Tools, DNS Server Tools, Group Policy, Remote Desktop, Server Manager > Install.
RSAT Tools will give me the ability to manage Active Directory Users and Services on a Windows 10 desktop.
![image](https://github.com/user-attachments/assets/93899a4e-3815-43b6-91ba-d79260c1730e)
I opened my server and ran the cmd `ipconfig` to see my IP address. I then tried pinging my server's ip from `Desktop 1` but it was unsuccessful since they're not  in the same subnet or domain.
![image](https://github.com/user-attachments/assets/06821cac-ad7b-4a2d-9d2b-2394c5b59006)

****
I opened the control panel on `Desktop 1`and gave it a static ip that's in the same subnet as the server. 
I clicked on the Network settings of my VirtualBox, and folr the `Attached tp:` I changed it from `NAT` to `Host-only Adapter`
I tried pinging my server again, and it was now successful.
[insert successful ping]
I added `Desktop 1` to my domain `ADLabs.com`- I opened File Explorer and right-clicked on `This PC` > Properties > Change Settings > Change > Domain. 
I then went into my Windows Server and opened AD USers and Computers to confirm that `Desktop 1` joined the domain.
[insert pic]
Next I opened `Desktop 1` and instead of signing in as the Administrator, I now signed in using the helpdesk account that I created earlier in the lab.

# Group Policy
I logged into the helpdesk account and opened AD Users and Computers. I right-cllicked on my doamin name > New > Organizational Unit. 
For the name I put in `HR`.
I right-clicked on Users > New > User. I named my new User `John` and created a new password for him. I then cliked on John's name and moved him into the HR folder/OU.
Next I created a New OU and named it `IT` then I moved the helpdedk into the IT Organizational Unit.

I opened Group Policy Management and checked my `Account Policies/Account Lockout Policy` and saw that my account lockout threshold was 0 invalid login attempts, which is a problem 
because this means that if someone kept trying to login, they would have unlimited attempts of guessing my password.
[inset pic of GPO]

To change this I right-clicked on `Default Domain Policy` > Edit > Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies.
I then open Account Lockout Policy, then clicked on Accoutn lockout threshold policy and checked the `Define policy setting` and set it to 4 invalid logon attempts and to reset account lockout after 30 minutes.
I also changed my Max password age to 90 days. I then went back to my Group Policy Management to see if the changes applied.
[inset pic of GPO]

I started by creating a new Windows 10 VM on virtualbox. After it was done setting up I named it `Desktop2`. I enalbed the admin account on this computer too and delted the other user account.
I gave `Destop2` a static ip and connected it to my window's server DNS. Then changed the virtula box's network settings to `Host only Adapter`.
[insert ips]
I then added it to my domain of `ADLabs.com`

I now opened my Active Directory on `Desktop1` on which I am signed in as the helpdesk which is an admin account and saw that `Desktop2` shows up under computers.
[Insert pic]

On `Desktop2`, I tried loggin in as `John` and it was succesful.
I went back on `Desktop1`, opened AD Users and Computers then went into the `HR` OU and clicked on John > Attribute Error. I checked on this to see when was the last time the user logged in.
[insert pic]

# Common Active Directory Issues
I logged into my `helpdesk` account and pinged John's computer using `-t` which sends a continuous ping.
I ran the command prompt as an admin and ran the cmd `gpresult /r>c:\results.txt`
This ran the command and put the findings in a text file called `results.txt` on my PC.
[Insert]

Since I set the lockout policy to 4 attempts. I intentionally tried to lock John out of his account my attempting the wrong password 4 times.
[insert pic of locked out account]

This scenario assumes that John calls the helpdesk and now as the helpdesk we have to help him log back in. So on my helpdesk account I opened AD Users and Computers. Clicked on his OU which is `HR`. Then double-clicked on `John` > Account > Unlock account > OK. 
Now the helpdesk asks John to try and log in again and he now successfully logs in.

Inside the helpdesk's Acitve Directory, I clicked on `Computers` and deleted the `Desktop2` computer. I then went on Desktop1 and tried to sign in and it gave me an error mesage.
[insert pic]
To add it back to the Domain, I logged into `Desktop2` as a localuser, then added it to a workgroup, the added it back to the ADLOabs.com domain then restarted the computer.
I went back into the `helpdesk` Active Directry and now `Desktop2` was back on the domain as it showed up under Computers.

# Security Groups, Map Drives, Personal Drives, Map Letter
I opened Server Manager on the `helpdesk`account. Then clicked on `File and Storage Services` > Shares > Right-click on the blank space > New Share > Quick > Next > Next.
[insert pic - Name-HR, click on select by volume]..

For the Share Name, I named it `HR` then hit Next > Next > Next > Create.

I then created another one but this time I named it `Personal`.
 I opened `C:` on file explorer and saw the two shared drives that I just created.
 [insert pic]

 I went back to AD Users and Computers. I right-cllicked on Users > Group. I named the Group `HR` and for the group type I selected `Security`.
 I opened up the HR Security Group, and went ot the `Managed By` taba and for the naeme I added \helpdesk.
 [insert pic]
 I then created another security group and named it `Personal`
 I opened up the shared drives' propersties, clikcked on Sharing,and copied their network paths. I then pasted the paths into the description of the Security groups respectivley in AD Users and computers.
 [insert pic of one example.- After network path,put [shared folder]]

 I then opened up the security groups and went to the `Members` tab and added the user `John` into them.
 To confirm that they're added, I opened up the HR OU and clicked on John's name, then clicked on `Member of` and HR and Personal showed up.
 [insert pic]

 # Permissioning the folder: 
 **Step 1:** I right-clicked the Personal folder on the local PC and hit properties > Security > Advanced > Disable inheritance > Convert inherited permissions.
 **Step 2:**  I  removed the Users, then hit add and added helpdesk and personal, and checked Modify and then hit OK. 
 **Step 3:**  I right-clicked the Personal folder again and hit Sharing > Share > Personal > Read and Write > Share.

 For the HR Folder:
  **Step 1:** I right-clicked the HR folder on the local PC and hit properties > Security > Advanced > Disable inheritance > Convert inherited permissions.
 **Step 2:**  I  removed the Users, then hit add and added helpdesk and HR, and checked Modify and then hit OK. 
 **Step 3:**  I right-clicked the HR folder again and hit Sharing > Share > HR > Read and Write > Share.

I looked at the network path for the HR properties and it was `\\Server2022\hr`.
To see if my permissions were setup correctly I opened `Desktop2` and logged in as John. I opened File explorer and typed int he network path: `\\Server2022\hr`and it worked.
[insert pic]
To map the drive I copied the network path, right-clicked on This PC > Map Network Drive, and then typed in the network path, checked `reconnect at sign in`, and hit finish.
Now, when I click on This PC, the HR folder shows up.
[inset pic].

On my server, I opened Active Directory and clicked on the user John > Profile. ON the Home folder on the connect P To: I typed in the Network path of the Personal Folder\username, `\\Server2022\%Personal%` and hit apply.
I signed in to John's account agian and clicked on `This PC`  and the personal folder was now showing  up.
[insert pic]

 # Remote Desktop
 I logged into Jphn's account and opened file explorer, and right-clicked on `This PC` > Properties > Remote Settings > `Put in helpdesk credentials` > Checked Allow remote connections to this computer > Select Users > helpdesk > OK > Apply > OK.
I now went into Desktop1 and logged in as helpdesk and opened remote desktop. I typed  `desktop2` as the computer name and hit Connect, and put in my helpdesk password.
[if it doesn't't work, sign in as john and on cmd run: ipconfig / flushdns]
On Johns computer, I hit Yes for allow the RDP connection, and I was now logged in as helpdesk on John's computer. I was able to add new folders into John's computer.
On the Users folder on `This PC` I clicked on JOhns name and then typed in\appdata and I was now able to delete and add new apps for him.
I ended the RDP session and let John log in again. He could see the new folder on his Home page that I added for him.
[Inset Pic]
On John's computer, I logged in and typed net user on the Command Prompt and I was able to see their shared drives.
[insert pic]
Another way to see their shared drives, is on the `helpdesk` account I opened the Registry Editor > File > Connect Network Registry > `Typed in desktop2`.
I got an error that it was Unable to connect to Desktop2. Make sure that this computer is on the network, has remote administration enabled.
I opened up `Services` on Desktop2 and ran it as an administrator, and clicked on Remote Registry Properties > Start Type: Automatic > Start >OK.
I opened the registry enter on the helpdesk account agian and this type it worked and I was able to access desktop 2.
I clicked on `Desktop2` > HKEY_USERS > s-1-5-21-..... > Network > Z. I was able to view that the driver is mapped with the Z drive.
[inset pic]

Next, I opened remote assistance on desktop2 and clicked on Invite Someone to help you. On the helpdesk account, I opened Remote Assistance  and clicked on Use an invitaiton file > type in `\\desktop2\c$` > Users > John > Desktop > Invitation. I then put in the password that was showing up on John's Screen. Then on John's screen I clickd on yes to allow helpdesk to see whatever is going on on your desktop. Now I was able to see John's desktop from the helpdesk computer.
[insert pic of both screens]
I was also able to use the chat fuction to chat with John and ask them what was the issue.

# Group Policy, Task Manager, Disable Logoff
I tried using the following commands to see how they worked: `gpupdate /force` , and `gpresult /r`
On the helpdesk computer, I opened Group Policy Managemnt and right-cliked on `Group Policy Object` > New. Then named it Task Manager and hit OK.
`Task Manager` was now under GPO. I clicked on it and selected `Delegation` > Add. Typed in 'John` and then hit OK, then put read permission only.
Right-clicked Task Manager > Edit > Policies > Administrative Temp. I played around with this and saw different things that I could add and remove from John's account like the : 'Task bar, Remove Task Manager, Remove Change Passowrd, Remove Logoff'.
After I changed different settings I went back to Group Policy Management and grabbed `Task Manager` from GPO and dragged it into `HR` since our User John is a part of that OU.
[insert pic]
I right-clicked on `Task Manager` and hit enforce, and then on John's compuer I ran the command `gpupadate /force` to apply the new policies.
I then ran the cmd: `gpresult /r` and saw that the Group Policy `Task Manager` was being applied.
[Insert pic]
I tried opening Task Manager as the user John but I wasn't able to. It was now blocked because of the `Task Manager` Group Policy
To see the policies enforced on John's computer as the helpdesk, I opened Groujp Policy Management > Right-clicked GP Results > GP Resuslts Wizard > Next > Browse > Type : Desktop2 > Next > /John > Next > Next > Finish.
That gave me the report of the Policies enforced on John's computer. I clicked on Details and it showed me even more information about all the policies setup on John's computer.

# Installing and Deploying PDQ, Software Packages
I took my Server of static IP to install the CD image. I took a screenshot of the static IP so that I won't forget it.
On VirtualBox, I clicked on Devices and chose the option to Insert Guest Additions CD image. Once I was done on my server I clicked on this PC and the CD image was mounted there. This CD will allow me to share stuff from my VM.
[Insert Pic]
I opened my virtualbox settings for my Server2022 lab and clicked on Shared Folders > Add Share > Chose Downloads as the Path, created a new folder in `Downloads`, and named it ADLabs > Checked Auto-mount > OK.
On the bottom right of my VirtualBox I right-clicked on the CD and hit remove disk from virtual drive. 
I opened my web browser and downloaded `PDQ`, and `PDQ Inventory`, and saved it into the ADLabs Folder. I then grabbed the PDQ application and moved it to the desktop.
I opened my Virtualbox network settings and selected Bridged Adapter. On my server I opened the control panel > View network status and tasks > change adapter settings > Righ-Click Ethernet > Rename > Obtain Ip address automatically > OK.
I pinged 8.8.8.8 to verify that I was connected to the internet.

I hit PDQ > Install. It required me to install .Net Framework 4.8 and then it finished installing. 
I also finished the installation for the PDQ Inventory
I opened PDQ and went to package Library and installed `Zoom Client`. 
I went to Packages > Right-clicked Zoom Client > Deploy Once > Choose Targets > Active Directory > Computers > Server2022 > OK > Deploy Now. Zoom was now installed on my Server's Desktop.
[Insert pic where you can see both zoom on desktop and PDQ]

I finished installing the PDQ inventory. I opened it up and clicked on All computers > Right-clicked Desktop2 > Run Report > Shared Folders. That showed me all the folders that were mapped on it.
I cliked on the Computer and it gave me all the information about Desktop2.
[Insert pic]
I clicked on Applications which showed me what applications they had installed on their PC.



After this I went back into my settings and changed my server's Ip address back to static, and changed the Virtualbox network settings back to `Host-Only Adapter`.
I pinged ADLabs.com to confirm that the static ip was working.
[insert pic]
I also restarted `Desktop2` from the PDQ Inventory app on my server.
I opened up PDQ Deploy again and deployed Zoom onto `Desktop2`. I logged into desktop2 to confirm that zoom was downloaded.]
[insert pic]

# Printer Setup, NTFS, Printer Cloud
I opened up Server Manager > Manage > Add Roles & Features > Next > Next > Next > Print and Document Services > Add Features > Next > Next > Next > Next > Install.
Back to Server Manager > Tools > Print Management > Print Servers > Right-click the blank space > Add Printer > Add a new printer using an existing port > Install a new driver > Next > HP Color LaserJet > Next > Uncheck Share this printer > Next > Next.
Now the Printer showed up in my Print Management under Printers.
[Insert Pic]
From here, start following along. <Part 12 (6:14)>


# Undersstanding Tickets Using Spiceworks, RDP Tools
I went into my browser and looked up Spiceworks and then I went to start remote session and it gave me a code.
I opened up `Desktop2` and removed the static ip so that it was connected to my internet. I set the Virtualbox network settings to `Bridged Adapter`, and in the advanced section I set `Allow` for the Promiscous mode.
I then went to the website `join.zoho.com` and put in the session ID and a name.
[insert pic]
I hit join session > Download > Run > Join.
I went back to spiceworks which was opened on the helpdesk account and I was now able to remotely access `Desktop2`.
I was able to open up the Command Prompt and chat with the user as I helped them out.

# Understanding Delegate Control and Account Locked out 
I opened up server manager and created a new user named `Scott`. Im going to act like Scott isn't fully part of our organization and he is just a vendor that needs limited access to some stuff. I also created a new OU named consultants and added him to it.
[Inset pic]
I right-clicked on my domain name > Delegate Control > Next > Add > Typed in `Scott` > Next > Reset Password > Next > Finish.
I logged in as scott and opened AD Users and Computers and opened the `HR` OU. I right clicked on the user John > Properties. The only thing Scott was able to do was reset John's password. All the other options were greyed out.
[insert pic]
saw that the only thing he was able to do was reset passwords.





















