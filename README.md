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


## 🔐 Configuring Group Policy

1. Opened **Group Policy Management** and navigated to:
   `Domains > (Mydomain.com) > Default Domain Policy > Settings`.

2. Identified a security risk — users had unlimited login attempts, allowing brute force attacks.
   ![Group Policy Settings](https://github.com/user-attachments/assets/a5aac827-c287-4664-9568-ab3946bd163d)

3. Edited the **Default Domain Policy**:
   Right-clicked on `Default Domain Policy` > **Edit** > `Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies`.

4. Configured the **Account Lockout Policy**:

   * Set **Account lockout threshold** to 4 invalid attempts.
   * Set **Reset account lockout after** to 30 minutes.
   * Set **Maximum password age** to 90 days.

5. Verified the policy was applied in **Group Policy Management**.
   ![Group Policy Applied](https://github.com/user-attachments/assets/751aeff7-9630-41d1-8ab6-bfe64d54f41a)

---

## 💻 Setting Up a New VM and Domain

1. Created a new **Windows 10 VM** named `Desktop2` in VirtualBox, enabling the admin account and deleting other user accounts.

2. Assigned a **static IP** to `Desktop2` and connected it to the server's DNS.

3. Set VirtualBox network to **Host Only Adapter**.
   ![Network Settings](https://github.com/user-attachments/assets/f958576a-37bb-4efa-8aea-fcb37e4577b9)

4. Added `Desktop2` to the **ADLabs.com domain**.

5. On `Desktop1`, logged in as **Helpdesk** (admin account) and confirmed that `Desktop2` was listed under **Computers**.
   ![Desktop2 in AD](https://github.com/user-attachments/assets/20d581a1-771a-457d-be8f-a41427ee878b)

6. Tested login on `Desktop2` as **John**, which was successful.

7. In **Active Directory Users and Computers**, I accessed the **HR OU**, clicked on **John**, and checked the **Attribute Error** to verify his last login time.
   ![Attribute Error](https://github.com/user-attachments/assets/a0faad53-3dad-4bf8-9a68-ad94882d8783)

---

## 🛠️ Common Active Directory Issues

1. Pinned `John`'s **PC** using **ping -t** to monitor when it comes back online.

2. Ran **gpresult /r** command to generate a **results.txt** file for policy results.
   ![Gpresult](https://github.com/user-attachments/assets/e6194f17-7af1-4ea3-bbe4-c3f902c80c0e)

3. Intentionally locked **John** out by entering the wrong password 4 times to test the lockout policy.
   ![Lockout](https://github.com/user-attachments/assets/cbc2486b-2f2d-4fb8-a31b-5bd6bd21327a)

4. To unlock **John**'s account, I navigated to **AD Users and Computers**, went to the **HR OU**, and unlocked his account.
   ![Unlock Account](https://github.com/user-attachments/assets/ddeeadb2-72ab-4f0a-b101-503329297c7b)

5. Verified **John** could log back in successfully.

6. Deleted **Desktop2** from the domain, logged in as **localuser**, rejoined it to the **ADLabs.com** domain, and restarted the PC.
   ![Desktop2 Removed](https://github.com/user-attachments/assets/f23f8c15-39bb-4efa-8e7f-70d4508c081c)

7. Rechecked **Active Directory** on the helpdesk account, confirming that `Desktop2` was successfully added back to the domain.

---

## 🗂️ Managing Security Groups and Network Drives

1. In **Server Manager**, I created a new shared folder named **HR**:
   `File and Storage Services > Shares > New Share > Quick > C: > Create`.
   ![Create HR Share](https://github.com/user-attachments/assets/643a2fe5-584b-45e4-afe4-ef5578ff7bc6)

2. Created a second shared folder called **Personal** and confirmed both appeared under **Shares** in **File Explorer**.
   ![HR and Personal Shares](https://github.com/user-attachments/assets/5b0f3ffd-dccd-4b91-9279-de1fadaad5d5)

3. Created **Security Groups** in Active Directory:

   * Created `HR` group and added **Helpdesk** under **Managed By**.
     ![HR Group](https://github.com/user-attachments/assets/5c0a82e6-5a87-4062-9212-4fa76cf54410)

   * Created `Personal` group similarly.

4. Pasted the shared folder paths into the **Security Groups** descriptions in **AD Users and Computers**.
   ![Group Descriptions](https://github.com/user-attachments/assets/8fde7482-1ec0-4aed-ba69-a83c99f3e0a3)

5. Added **John** to both groups by navigating to the **Members** tab in each group.
   ![Add Members](https://github.com/user-attachments/assets/537cf90c-7529-4c4f-af30-b24a0611a730)

---

## 📁 Folder Permissions

1. Configured **permissions** for the **Personal** folder:

   * Disabled inheritance and adjusted permissions for **Helpdesk** and **Personal** groups.
   * Set to **Modify**.
   * Shared the folder with **Read/Write** permissions.

2. Configured **permissions** for the **HR** folder in a similar manner.

3. Verified permissions by logging into **Desktop2** as **John**, confirming access to the **HR** folder.
   ![HR Folder Access](https://github.com/user-attachments/assets/db43b785-6dc1-4c43-9dd9-9dfbf81d7d13)

4. Mapped the **HR** folder by copying the network path and selecting **Reconnect at sign-in**.
   ![Mapped Drive](https://github.com/user-attachments/assets/57696812-ea08-4bdc-9d00-d8178f3a261d)

5. Set up a **Personal Folder** for **John** in Active Directory under his **Profile > Home Folder** by mapping to `\\Server2022\Personal\%username%`.
   ![Personal Folder](https://github.com/user-attachments/assets/5704546d-01ab-445c-804e-4a20e5752396)


### Remote Desktop

I logged into John's account and opened File Explorer. I right-clicked on **This PC** > **Properties** > **Remote Settings** > entered the **helpdesk** credentials > checked **Allow remote connections to this computer** > selected **Users** > added **helpdesk** > clicked **OK**, then **Apply** and **OK**.

I then went to **Desktop1**, logged in as **helpdesk**, and opened **Remote Desktop**. I typed `desktop2` as the computer name and hit **Connect**, entering my helpdesk password. However, I received the following error:
![image](https://github.com/user-attachments/assets/3eabf0a7-8ca8-415d-ad2c-19967f5ee98b)

I proceeded to the settings and allowed remote connections on the **helpdesk** computer as well, then tried again.

For troubleshooting, I pinged **desktop2** with the command `ping -t` to check connectivity:
![image](https://github.com/user-attachments/assets/0ec7179d-014f-4d18-948a-6942a6b25def)

To ensure both desktops could communicate, I enabled ICMP requests on the firewall with the following command:
`netsh advfirewall firewall add rule name="Allow ICMPv4-In" protocol=icmpv4:8,any dir=in action=allow`
![image](https://github.com/user-attachments/assets/f9ba204d-8396-4813-8940-dbf9b5d37121)

I was then able to connect by typing **desktop2** in the RDP application:
![image](https://github.com/user-attachments/assets/d3971d60-8164-4e7c-8db1-9ad7d396659c)

On **John's** computer, I clicked **Yes** to allow the RDP connection and successfully logged into his computer as **helpdesk**. I was able to add new folders to **John's** desktop:
![image](https://github.com/user-attachments/assets/691d396f-baea-4b8a-8e0e-d110846650f3)

I accessed **John's** **C: > Users > John > Desktop** through RDP and created a folder named **Test Folder** and moved it to his desktop. I also navigated to **appdata** and added new apps for him:
I ended the RDP session, and **John** logged in again, where he saw the new folder I added on his home page:
![image](https://github.com/user-attachments/assets/31deb30f-e031-4c95-a29a-896cb7fd1f25)

On **John's** computer, I logged in and typed `net use` in the command prompt. This allowed me to see the shared drives:
![image](https://github.com/user-attachments/assets/3b7b6438-c0d2-446c-ab1d-d8e08f2f1525)

Additionally, I accessed the **Registry Editor** on **helpdesk** by going to **File > Connect Network Registry**, typing **desktop2**. However, I got an error message stating it was unable to connect, and I should ensure that the computer was on the network and had remote administration enabled:
I resolved this by opening **Services** on **Desktop2**, running it as an administrator, and setting **Remote Registry** to **Automatic** > clicked **Start** > **OK**.

Finally, I connected to the **Desktop2** registry on **helpdesk** and was able to view the mapped Z drive under the registry key:
![image](https://github.com/user-attachments/assets/1b26da27-dbca-4b75-a15c-00d3fbeb0128)



Next, I opened remote assistance on desktop2 and clicked on Invite Someone to help you > Save this invitation as a file > Save. It then gave me an invitation file and password.
![image](https://github.com/user-attachments/assets/0b425b87-dbf2-42d1-89d8-19f147127079)

On the helpdesk account, I opened Remote Assistance  and clicked on Use an invitation file > type in `\\desktop2\c$` > Users > John > Documents > Invitation.

I then put in the password that was showing up on John's Screen. The on John's screen, I clicked on yes to allow helpdesk to see whatever is going on on your desktop. Now I was able to see John's desktop from the helpdesk computer.
![image](https://github.com/user-attachments/assets/bf9170a0-99d1-4bf2-be6f-961d832edd36)

I was also able to use the chat function to chat with John and ask them about the issue.

# Group Policy, Task Manager, Disable Logoff
On the helpdesk computer, I opened Group Policy Management and right-cliked on `Group Policy Objects` > New. Then named it Task Manager and hit OK.
`Task Manager` was now under GPO. I clicked on it and selected `Delegation` > Add. Typed in `John ` and then hit OK, then put read permission only.
Right-clicked Task Manager > Edit > User Config > Policies > Administrative Temp > Sytem > Ctrl + Alt + Del.

I enabled remove change Password for the user.
I also enabled `Remove Task Manager` for users so that the users won't be able to launch task manager.
I closed out the editor and went back to the `GP Management`. I grabbed the 
grabbed the `Task Manager` object from GPO and dragged it into `HR`  Organizational Unit.
![image](https://github.com/user-attachments/assets/dabb25d0-3b31-4846-8518-e50a048b8546)

I right-clicked on `Task Manager` and hit enforced, and then on John's compuer I ran the command `gpupadate /force` to apply the new policies.
I then ran the cmd: `gpresult /r` and saw that the Group Policy `Task Manager` was being applied.
![image](https://github.com/user-attachments/assets/c618e81c-5f91-4d21-983b-163554d821f5)

I tried opening Task Manager as the user John but I wasn't able to. 
![image](https://github.com/user-attachments/assets/913a4245-8849-427c-989b-6b78f439ece4)

To see the policies enforced on John's computer,I logged into the helpdesk computer I opened Group Policy Management > Right-clicked GP Results > GP Resuslts Wizard > Next > Another computer > Browse > Type : Desktop2 > Next > /John > Next > Next > Finish.
That gave me the report of the Policies enforced on John's computer. I clicked on Details and it showed me even more information about all the policies setup on John's computer.
![image](https://github.com/user-attachments/assets/acb4cacc-ca64-431b-8fbf-75357caadc84)

# Installing and Deploying PDQ, Software Packages
I took my Server of static IP to install the CD image. I took a screenshot of the static IP so that I won't forget it.
On VirtualBox, I clicked on Devices and chose the option to Insert Guest Additions CD image. Once I was done on my server I clicked on this PC and the CD image was mounted there. This CD will allow me to share stuff from my VM.
![image](https://github.com/user-attachments/assets/8f5c828a-b758-4ea2-9dbf-c0857777d6b8)

I opened my virtualbox settings for my Server2022 lab and clicked on Shared Folders > Add Share > Chose Downloads as the Path, created a new folder in `Downloads`, and named it ADLabs > Checked Auto-mount > OK.
On the bottom right of my VirtualBox I right-clicked on the CD and hit remove disk from virtual drive. 
I opened my web browser and downloaded `PDQ`, and `PDQ Inventory`, and saved it into the ADLabs Folder. I then grabbed the PDQ application and moved it to the desktop.
I opened my Virtualbox network settings and selected Bridged Adapter. On my server I opened the control panel > View network status and tasks > change adapter settings > Righ-Click Ethernet > Rename > Obtain Ip address automatically > OK.
I pinged 8.8.8.8 to verify that I was connected to the internet.

I hit PDQ > Install. It required me to install .Net Framework 4.8 and then it finished installing. 
I also finished the installation for the PDQ Inventory

I opened PDQ Deploy and went to the package Library and installed `PDFsam Basic`. 
I went to Packages > Right-clicked PDFsam Basic > Deploy Once > Choose Targets > Active Directory > Computers > Server2022 > OK > Deploy Now. 
PDFsam Basic was now installed on my Server's Desktop.
![image](https://github.com/user-attachments/assets/2364f509-a54f-41d7-8f63-45c0c350147b)


I finished installing the PDQ inventory.
I right-clicked on all computers and added Desktop1 and Desktop2.
I clicked on All computers > Right-clicked Server2022 > Run Report > Shared Folders. That showed me all the folders that were mapped on it.
I clicked on the Computer and it gave me all the information about Desktop2.
![image](https://github.com/user-attachments/assets/e2881996-2512-41fd-96e0-042ec1aad570)



After this I went back into my settings and changed my server's Ip address back to static, and changed the Virtualbox network settings back to `Host-Only Adapter`.
I pinged ADLabs.com to confirm that the static ip was working.
![image](https://github.com/user-attachments/assets/ebb98caa-cf6c-408f-bac3-3528c6c4913e)

I opened up PDQ Deploy again and deployed Zoom onto `Desktop2`. I logged into desktop2 to confirm that zoom was downloaded.]
![image](https://github.com/user-attachments/assets/7940dd54-6359-4d89-8d15-e70756fe15b1)


# Printer Setup, NTFS, Printer Cloud
I opened up Server Manager > Manage > Add Roles & Features > Next > Next > Next > Print and Document Services > Add Features > Next > Next > Next > Next > Install.

Back to Server Manager > Tools > Print Management > Print Servers > Right-click the blank space > Add Printer > Add a new printer using an existing port > Next > Install a new driver > Next > MS Publisher Color Printer > Next > Uncheck Share this printer > Next > Next > Finish.

Now the Printer showed up in my Print Management under Printers.
![image](https://github.com/user-attachments/assets/a8e9d52f-163f-4172-9863-875e9a688565)

# Undersstanding Tickets Using Spiceworks, RDP Tools
I went into my browser and looked up Spiceworks and then I went to start remote session and it gave me a code.
I opened up `Desktop2` and removed the static ip so that it was connected to my internet. I set the Virtualbox network settings to `Bridged Adapter`, and in the advanced section I set `Allow` for the Promiscous mode.
I then went to the website `join.zoho.com` and put in the session ID and a name.
![image](https://github.com/user-attachments/assets/3fb802ae-7137-4c01-abf9-72696697e84f)

I hit join session > Download > Run > Join.
I went back to spiceworks which was opened on the helpdesk account and I was now able to remotely access `Desktop2`.
I was able to open up the Command Prompt and chat with the user as I helped them out.
![image](https://github.com/user-attachments/assets/eb147de8-2429-4e28-9ad9-970bc1bf7acc)


# Understanding Delegate Control and Account Locked out 
I opened up AD Users and Computers and created a new user named `Scott`. 
I'm going to act like Scott isn't fully part of our organization, and he is just a vendor that needs limited access to some stuff. I also created a new OU named consultants and added him to it.
![image](https://github.com/user-attachments/assets/9cba8821-af4b-4df9-b0cb-34fd6aa6ff38)

I right-clicked on my domain name > Delegate Control > Next > Add > Typed in `Scott` > Next > Reset Password > Next > Finish.

I logged in as scott and opened AD Users and Computers and opened the `HR` OU. I right clicked on the user John > Properties. The only thing Scott was able to do was reset John's password. All the other options were greyed out.
[insert pic]
saw that the only thing he was able to do was reset passwords.





















