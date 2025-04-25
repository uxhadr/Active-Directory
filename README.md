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
Next I clicked on the Network settings of my VirtualBox, and folr the `Attached tp:` I changed it from `NAT` to `Host-only Adapter`

Next I wnet back to my Windows 10 computer. Once it was done setting up, I opened File Explorer and right-clicked on `This PC` > Manage > Local Users and Groups > Users.
I right-clicked on Adminstrator > Properties. I checdked Password never expires and unchecked all the other boxes,and hit OK.
I I right-clicked on Adminstrator > Set Password. 
I created a password and then signed out, then logged in as the Adminstrator.
Once logged in, I opened `Computer Mangement` and deleted the `User`account so that I now only had one account on the PC.
I renamed the computer to : Desktop 1.
I opened the Settings and went to `Apps & features` > Optional features > Add a feature > RSAT Tools: (Active Directory Certificate Service Tools,  AD Domain Services,
DHCP Server Tools, DNS Server Tools, Group Policy, Remote Desktop, Server Manager > Install.
RSAT Tools will give me the ability to Active Directory Users and Services on a Windows 10 desktop.
[Inset pictur of RSAT Tools installed]
I downloaded google chrome and teamviewer.
I opened my server and ran the cmd `ipconfig` to see my ip address. I then tried pinging my server's ip from `Desktop 1` but it was unsuccesful since they're not  in the same subnet or domain.
[inset pic of unsuccesful ping`
I opened the control panel on `Desktop 1`and gave it a static ip that's in the same subnet as the server. 
I clicked on the Network settings of my VirtualBox, and folr the `Attached tp:` I changed it from `NAT` to `Host-only Adapter`
I tried pinging my server agian and it was now succesful.
[insert successful ping]
I added `Desktop 1` to my domain `ADLabs.com`- I opened file explored and right-clicked on `This PC` > Properties > Change Settings > Change > Domain. 
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

Next, I opened remote assistance on desktop2 and clicked on Invite Someone to help you. On the helpdesk account, I opened Remote Assistance  and clicked on Use an invitaiton file > type in `\\desktop2\c$` > Users > John > Desktop > Invitation. I then put in the password that was showing up on John's Screen. Then on John's screen I clickd on Ues to allow helpdesk to see whatever is going on on your desktop.
[insert pic of both screens]























