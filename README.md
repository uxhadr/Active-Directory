# 🖥️ Active Directory Lab Setup

This project documents the process I followed to set up an Active Directory lab environment using VirtualBox and Windows Server 2022. The goal was to get hands-on practice with domain services, account management, and administrative tools.

---

## 📦 Installing VirtualBox and Server 2022

1. Downloaded and installed [VirtualBox](https://www.virtualbox.org/).
2. Went to the [Microsoft Evaluation Center](https://www.microsoft.com/en-us/evalcenter/) and downloaded the **Windows Server 2022 ISO**.
3. Set up a new VM in VirtualBox and attached the ISO.
4. Launched the VM and selected **Desktop Experience** during Windows Setup.

---

## ⚙️ Installing Active Directory

1. Renamed the server to `Server2022`.  
   ![Rename Server](https://github.com/user-attachments/assets/afd670d5-8e86-42fe-9b37-6e99ff7ad28c)

2. Opened **Server Manager** and navigated to:  
   `Manage > Add Roles and Features > Next > Role-based > Next > Next`

3. Selected:
   - **Active Directory Domain Services**
   - Clicked **Add Features > Next > Next > Install**

4. After installation, clicked on  
   `Promote this server to a domain controller`.

5. Chose **Add a new forest** and named the root domain `ADLabs`.

6. Set a password and completed the installation.

7. After the reboot, opened **Active Directory Users and Computers** via  
   `Server Manager > Tools` and pinned it to the taskbar.

---

## 👤 Active Directory Account Creation

1. Opened **Active Directory Administrative Center** from `Tools`.

2. Selected my domain and enabled the **Recycle Bin**.

3. Opened **AD Users and Computers**, went to `View > Advanced Features`.

4. Navigated to `Domain > Users`, right-clicked `Administrator`, and chose **Copy** to create a new Help Desk account with admin privileges.  
   ![Help Desk Account](https://github.com/user-attachments/assets/3aa09aad-7954-4d76-bbee-8e5408a698a8)

5. To verify the account details, I ran:

   ```bash
   net user helpdesk /domain


## 🖧 Join Windows 10 PC to Domain + Install RSAT Tools

1. Downloaded a **Windows 10 ISO** from the [Microsoft Evaluation Center](https://www.microsoft.com/en-us/evalcenter/).

2. Launched the Windows 10 setup and selected **ISO file**, then saved it.
   ![ISO Setup](https://github.com/user-attachments/assets/8ed45424-e6dc-4711-9725-1c1f2682b7f5)

3. In VirtualBox:

   * Clicked **New**, named it `Windows 10 Lab`
   * Allocated **5 GB RAM**
   * Attached the Windows 10 ISO as the startup disk

4. Selected “I don’t have a product key” and chose **Windows 10 Pro** (Home edition can't join domains).

5. After installation, opened **Control Panel** on `Desktop1` and assigned a **static IP** on the same subnet as the server.

6. In VirtualBox, changed **Network Settings** from `NAT` to `Host-only Adapter`.

7. At first, I couldn't ping the desktop. To fix it:

   * Opened **Windows Defender Firewall** > **Advanced Settings**
   * Enabled: `File and Printer Sharing (Echo Request - ICMPv4-In)`
   * Ping was now successful
     ![Ping Success](https://github.com/user-attachments/assets/67117df2-55b8-4e02-ac7f-1c7a6cc54592)

---

## 🏷️ Join PC to Domain

8. Opened **System Properties**:

   * `Win + R` → `sysdm.cpl` → Enter
     *or*
   * Right-click **This PC** → **Properties** → **Advanced system settings**

9. Under the **Computer Name** tab, clicked **Change...**, selected **Domain**, and entered: `ADLabs.com`.

10. Entered domain admin credentials when prompted.

11. Back on the server, ran `ipconfig` to confirm the IP address.

12. Opened **AD Users and Computers** to verify `Desktop1` successfully joined the domain.
    ![Joined Domain](https://github.com/user-attachments/assets/d656a0e4-bf60-4b44-8a60-9239944090c2)

---

## 🛠️ Desktop Cleanup and RSAT Tool Installation

13. On `Desktop1`:

* Opened **Computer Management**
* Went to `Local Users and Groups > Users`
* Right-clicked `Administrator` → **Properties**
* Checked **Password never expires**, unchecked all other options
* Set a new password and signed in as Administrator

14. Deleted the default `User` account and renamed the PC to: `Desktop1`.

15. Installed **RSAT Tools** via:
    `Settings > Apps & Features > Optional Features > Add a feature`
    Installed:

    * Active Directory Certificate Services
    * Active Directory Domain Services
    * DHCP Server Tools
    * DNS Server Tools
    * Group Policy
    * Remote Desktop Services
    * Server Manager
      ![RSAT Tools](https://github.com/user-attachments/assets/93899a4e-3815-43b6-91ba-d79260c1730e)

---

## 👥 Domain Logon and Organizational Units

16. Signed in as the **Helpdesk** domain user (created earlier in the lab).

17. Opened **AD Users and Computers**:

* Right-clicked domain name → **New > Organizational Unit** → Named it `HR`
* Right-clicked `Users` > **New > User** → Created a new user named `John`
* Moved `John` into the `HR` OU

18. Created another **OU** named `IT` and moved the **Helpdesk** user into it.


# Group Policy
To view my Account Policies, I opened Group Policy Management > Domains > (Mydomain.com) > Default DOmain Policy > Settings.
I saw that users have unlimited attempts to input a password which is a security risk as it allows for a bruteforce attempt to occur. 
![image](https://github.com/user-attachments/assets/a5aac827-c287-4664-9568-ab3946bd163d)

To change this, I right-clicked on `Default Domain Policy` > Edit > Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies.

I then opened Account Lockout Policy, clicked on Account lockout threshold policy, checked the `Define policy setting`, set it to 4 invalid logon attempts, and reset account lockout after 30 minutes.
I also changed the maximum password age to 90 days. I then went back to my Group Policy Management to see if the changes applied.
![image](https://github.com/user-attachments/assets/751aeff7-9630-41d1-8ab6-bfe64d54f41a)

I created a new Windows 10 VM on virtualbox. After it was done setting up I named it `Desktop2`. I enalbed the admin account on this computer too and delted the other user account.

I gave `Destop2` a static ip and connected it to my window's server DNS. Then changed the virtula box's network settings to `Host only Adapter`.
![image](https://github.com/user-attachments/assets/f958576a-37bb-4efa-8aea-fcb37e4577b9)

I then added it to my domain of `ADLabs.com`

I now opened my Active Directory on `Desktop1` on which I am signed in as the helpdesk which is an admin account and saw that `Desktop2` shows up under computers.
![image](https://github.com/user-attachments/assets/20d581a1-771a-457d-be8f-a41427ee878b)

On `Desktop2`, I tried logging in as `John` and it was successful.
I went back on `Desktop1`, opened AD Users and Computers, I enable Advanced features view, then went into the `HR` OU and clicked on John > Attribute Error. I checked on this to see when was the last time the user last logged in.
![image](https://github.com/user-attachments/assets/a0faad53-3dad-4bf8-9a68-ad94882d8783)

# Common Active Directory Issues
I logged into my `helpdesk` account and pinged John's computer using `-t`, which sends a continuous ping, which would help me know when John's computer gets back on the network.
I opened the command prompt as an admin and ran the cmd `gpresult /r>c:\results.txt`
This ran the command and put the findings in a text file called `results.txt` on my PC. 
![image](https://github.com/user-attachments/assets/e6194f17-7af1-4ea3-bbe4-c3f902c80c0e)

Since I set the lockout policy to 4 attempts. I intentionally tried to lock John out of his account my attempting the wrong password 4 times.
![image](https://github.com/user-attachments/assets/cbc2486b-2f2d-4fb8-a31b-5bd6bd21327a)

 This scenario assumes that John calls the helpdesk, and now, as the helpdesk, we have to help him log back in. So, on my helpdesk account,t I opened AD Users and Computers. Clicked on his OU which is `HR`. Then double-clicked on `John` > Account > Unlock account > OK. 
 ![image](https://github.com/user-attachments/assets/ddeeadb2-72ab-4f0a-b101-503329297c7b)
Now the helpdesk asks John to try and log in again and he now successfully logs in.

Inside the helpdesk's Active Directory, I clicked on `Computers` and deleted the `Desktop2` computer. I then went on Desktop2 and tried to sign in and it gave me an error mesage.
![image](https://github.com/user-attachments/assets/f23f8c15-39d3-432b-a0f2-8ed3b0b0e69d)

To add it back to the Domain, I logged into `Desktop2` as a localuser, then added it to a workgroup, the added it back to the ADLOabs.com domain then restarted the computer.
I went back into the `helpdesk` Active Directry and now `Desktop2` was back on the domain as it showed up under Computers.


# Security Groups, Map Drives, Personal Drives, Map Letter
I opened Server Manager, then clicked on `File and Storage Services` > Shares > Right-click on the blank space > New Share > Quick > Next > Select by volume: C: > Next > `Name it HER` > Next > Next > Next > Create.
![image](https://github.com/user-attachments/assets/643a2fe5-584b-45e4-afe4-ef5578ff7bc6)

I then created another one named  `Personal`.
 I opened `C:` > Shares on file explorer and saw the two shared drives that I just created.
![image](https://github.com/user-attachments/assets/5b0f3ffd-dccd-4b91-9279-de1fadaad5d5)

 I went back to AD Users and Computers. I right-clicked on Users > New > Group. 
I named the Group `HR` and for the group type I selected `Security`.
 I opened up the HR Security Group, and went ot the `Managed By` tab and for the name I added \helpdesk.
![image](https://github.com/user-attachments/assets/5c0a82e6-5a87-4062-9212-4fa76cf54410)

 I then created another security group and named it `Personal`
 I opened up the shared drives' properties, clicked on Sharing, and copied their network paths. I then pasted the paths into the description of the Security groups respectivley in AD Users and computers.
![image](https://github.com/user-attachments/assets/8fde7482-1ec0-4aed-ba69-a83c99f3e0a3)


I then opened up the security groups and went to the `Members` tab and added the user `John` into them.
To confirm that they're added, I opened up the HR OU and clicked on John's name, then clicked on `Member of` and HR and Personal showed up.
![image](https://github.com/user-attachments/assets/537cf90c-7529-4c4f-af30-b24a0611a730)


 # Permissioning the folder: 
 **Step 1:** I right-clicked the Personal share folder on the local PC and hit properties > Security > Advanced > Disable inheritance > Convert inherited permissions.
 **Step 2:**  I  removed the Users, then hit add and added helpdesk and personal, and checked Modify, and then hit OK. 
 **Step 3:**  I right-clicked the Personal folder > Properties > Sharing > Share > Personal > Read and Write > Share.

 For the HR Folder:
  **Step 1:** I right-clicked the HR share folder on the local PC and hit properties > Security > Advanced > Disable inheritance > Convert inherited permissions.
 **Step 2:**  I  removed the Users, then added helpdesk and HR, and checked Modify and then hit OK. 
 **Step 3:**  I right-clicked the HR folder again and hit Sharing > Share > HR > Read and Write > Share.

I looked at the network path for the HR properties and it was `\\Server2022\hr`.
To see if my permissions were set up correctly, I opened `Desktop2` and logged in as John. I opened File explorer and typed in the network path: `\\Server2022\hr`and it worked.
![image](https://github.com/user-attachments/assets/db43b785-6dc1-4c43-9dd9-9dfbf81d7d13)
![image](https://github.com/user-attachments/assets/abb73157-b057-4a9a-99ab-a400b04f90a5)

To map the HR drive: I copied the network path, right-clicked on This PC > Map Network Drive > typed in the network path > checked `reconnect at sign in`, and hit finish.
Now, when I click on This PC, the HR folder shows up.
![image](https://github.com/user-attachments/assets/57696812-ea08-4bdc-9d00-d8178f3a261d)


On my server, I opened Active Directory and clicked on the user John > Profile > Home folder >  connect > P: > To: > Type in the Network path of the Personal Folder\username, `\\Server2022\Personal\% username%` > Apply.


I signed in to John's account agian and clicked on `This PC`  and the personal folder was now showing  up.
![image](https://github.com/user-attachments/assets/5704546d-01ab-445c-804e-4a20e5752396)


 # Remote Desktop
 I logged into John's account and opened file explorer, and right-clicked on `This PC` > Properties > Remote Settings > `Put in helpdesk credentials` > Checked Allow remote connections to this computer > Select Users > helpdesk > OK > Apply > OK.
I now went into Desktop1 and logged in as helpdesk and opened remote desktop. I typed  `desktop2` as the computer name and hit Connect, and put in my helpdesk password.
I kept getting the following error:
![image](https://github.com/user-attachments/assets/3eabf0a7-8ca8-415d-ad2c-19967f5ee98b)
I went to settings and allowed remote conections on this computer on the helpdesk's computer then tried again.
ping -t help with troubleshooting the following:
![image](https://github.com/user-attachments/assets/0ec7179d-014f-4d18-948a-6942a6b25def)
I had to allow ICMP requests on the firewall on both desktops for the desktops to be able to ping each other using the cmd: `netsh advfirewall firewall add rule name="Allow ICMPv4-In" protocol=icmpv4:8,any dir=in action=allow`
![image](https://github.com/user-attachments/assets/f9ba204d-8396-4813-8940-dbf9b5d37121)

I was now able to remote by typing desktop2 on the rdp application.
![image](https://github.com/user-attachments/assets/d3971d60-8164-4e7c-8db1-9ad7d396659c)

On Johns computer, I hit Yes for allow the RDP connection, and I was now logged in as helpdesk on John's computer. I was able to add new folders into John's computer.
![image](https://github.com/user-attachments/assets/691d396f-baea-4b8a-8e0e-d110846650f3)

Now logged as the helpdesk accessing John's computer. I clicked on `This PC` > C: > Users > John > Continue > Desktop. 
I was able to create new folders and delete folders for John. 
I created a new called and called it `Test Folder` and moved it to his Desktop.
I clicked on `This PC` > Users > John > and then typed in `\appdata` and I was now able to delete and add new apps for him.
I ended the RDP session and let John log in again. John could see the new folder on his Home page that I added for him.
![image](https://github.com/user-attachments/assets/31deb30f-e031-4c95-a29a-896cb7fd1f25)

On John's computer, I logged in and typed `net use` on the Command Prompt, and I was able to see their shared drives.
![image](https://github.com/user-attachments/assets/3b7b6438-c0d2-446c-ab1d-d8e08f2f1525)

Another way to see their shared drives, is on the `helpdesk` account I opened the Registry Editor > File > Connect Network Registry > `Typed in desktop2`.
I got an error that it was Unable to connect to Desktop2.` Make sure that this computer is on the network, has remote administration enabled.`
I opened up `Services` on Desktop2 and ran it as an administrator, and clicked on Remote Registry Properties > Start Type: Automatic > Start >OK.
I opened the registry enter on the helpdesk account agian and this type it worked and I was able to access desktop 2.
I clicked on `Desktop2` > HKEY_USERS > s-1-5-21-..... > Network > Z. I was able to view that the driver is mapped with the Z drive.
![image](https://github.com/user-attachments/assets/1b26da27-dbca-4b75-a15c-00d3fbeb0128)

[[[[[[[Start from here]]]]]]]]]]]]]]]]]]]
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





















