<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2025
- Windows 11 

<h2>High-Level Deployment and Configuration Steps</h2>

- Setup Resources in Azure
- Ensure Connectivity between the client and the Domain Controller
- Install Active Directory on Domain Controller
- Create User Accounts in Active Directory
- Join Client to Domain
- Setup Remote Desktop for Non-Administrative Users on Client
- Clean Up Resources by Deleting Resource Groups

<h2>Deployment and Configuration Steps</h2>
<h2>Setting Up Resources in Azure</h2>

<p>
<img width="755" height="1309" alt="setting up resources" src="https://github.com/user-attachments/assets/9b62b9b8-f03e-4c39-8173-9faf1fb4605e" />
/>
</p>
<p>
We first have to create a new resource group in Azure.

Create a new resource group and name it "AD-Lab", then select your region, then click "Review + create", then once validation is passed, click "Create" at the bottom
</p>
<br />

<p>
<img width="543" height="1308" alt="creatingDC-1" src="https://github.com/user-attachments/assets/4a3cb315-d7d0-4121-a79c-86241b68c976" />
/>
</p>
<p>
Now it's time to setup our virtual machines for our domain controller.

Create a new virtual machine and ensure your resource group is "AD-Lab", then name your virtual machine "DC-1", then ensure consistency between regions selected, then select "Windows Server 2025", then select "Standard_D2as_v7" as the size, then enter a username and password (write these down for later), then check the two boxes to continue, then click "Review + create", then once validation is passed, click "Create" at the bottom.
</p>
<br />

<p>
<img width="606" height="1311" alt="creatingClient-1" src="https://github.com/user-attachments/assets/23b44c35-8075-4ef8-9d9f-0bbb1805a2f5" />
/>
</p>
<p>
Now it's time to setup our virtual machine for our client.

Create a new virtual machine and ensure your resource group is "AD-Lab", then name your virtual machine "Client-1", then ensure consistency between regions selected, then select "Windows 11 Pro", then select "Standard_D2as_v7" as the size, then enter a username and password (write these down for later), then check the box to continue, then click "Review + create", then once validation is passed, click "Create" at the bottom.
</p>
<br />

<p>
<img width="785" height="847" alt="configureDC-1" src="https://github.com/user-attachments/assets/66d10b82-a915-4bc2-95f4-6ad5cc1bd15d" />
/>
</p>
<p>
Now we must change our domain controllers Network Interface Card (NIC) Private IP address from dynamic to static.

Find your DC-1 virtual machine in Azure and click on it to open, then select "Networking" from the options available on the left-hand side, then click on "dc-1232(primary)/ipconfig1(primary)"
</p>
<br />

<p>
<img width="1443" height="763" alt="DC-1 static" src="https://github.com/user-attachments/assets/34f2dc9c-8528-4900-9435-7ec4c34769bf" />
</p>
<p>
From "dc-1232", click "IP configurations" from the options available on the left-hand side, then click on "172.16.0.4" (Dynamic) and change it to static
</p>


<p>
Then click "Save" and you have successfully set your domain controller's Private IP address to static instead of dynamic.
</p>
<br />

<h2>Ensuring Connectivity Between Client and Domain Controller</h2>

<!--
<p>
<img width="1412" height="862" alt="client-1 RDC" src="https://github.com/user-attachments/assets/1e18e6e5-9d2d-415f-8b5d-207b7defd60c" />
</p>
<p>
Now we will login to Remote Desktop using Client-1's Public IP address.

<!--
Find Client-1 in Azure then click on it, then copy it's Public IP Address, then on your physical machine, launch "Remote Desktop Connection", then paste the public IP into it, then click "Connect", then enter the credentials we wrote down earlier, then click "Yes" to the prompt
</p>
<br />

<!--
<p>
<img src="https://i.imgur.com/TSAqeaA.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now that you're in Client-1, open "Command Prompt", then ping DC-1's Private IP address (the one we changed from dynamic to static), type "ping 10.0.0.4 -t" in the command prompt, then hit enter.


Notice how the pings are being timed out, that is due to the firewall setting for the domain controller. We must now Remote Desktop into our domain controller.
</p>

-->
<br />

<p>
<img width="777" height="690" alt="DC-1 RDC" src="https://github.com/user-attachments/assets/84a2c10a-9ccb-4ca0-88af-bfbf046ee3a5" />

</p>
<p>
Find DC-1 in Azure then click it. Then copy the public IP address, then open "Remote Desktop Connection" on your physical machine, then click "Connect", then enter the credentials we wrote down earlier, then click "Yes" to the prompt.
</p>
<br />

<p>
<img width="930" height="665" alt="DC-1 firewall" src="https://github.com/user-attachments/assets/f1120792-a4a1-4a70-bd25-f0db40972395" />

</p>
<p>
Once logged into your domain controller, right click the Windows button and click "Run". Open the command "wf.msc" and click "OK".

<p>
<img width="1022" height="577" alt="dc-1 disablefirewall" src="https://github.com/user-attachments/assets/071e5a52-c2ff-408c-877f-26e65a4efb76" />
</p>
<p>
Once "Windows Defender Firewall with Advanced Security on Local Computer" is open, navigate to "Windows Defender Firewall Properties" and turn the "Firewall State" to off in the tabs for "Domain Profile", "Private Profile", and "Public Profile" then click "OK".
</p>

Log back into Client-1 and you should see the pings succeeding. Connection has successfully been established so you can close command prompt on Client-1.
</p>
<br />

<h2>Installing Active Directory on DC-1</h2>

<p>
<img src="https://i.imgur.com/4QGGyGU.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Log into DC-1 through Remote Desktop, then go to "Server Manager", then click "Add roles and features", then click "Next", then click "Next", then click "Next", then select "Active Directory Domain Services", then click "Add Features", then click "Next", then click "Next", then click "Install", then after installing, click "Close"
</p>
<br />

<p>
<img src="https://i.imgur.com/4QGGyGU.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Click on the flag icon then click "Promote this serve to a domain controller", then select "Add a new forest", then type "mydomain.com" as the Root domain name, then click "Next", then enter "Password1" as the "DSRM" password, then click "Next", then click "Next", then click "Next", then click "Next", then click "Install", then let DC-1 restart.
</p>
<br />

<p>
<img src="https://i.imgur.com/qpzo4AD.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Remote Desktop into DC-1 again. This time we will use another user to sign-in. Enter "mydomain.com\labuser" as the username, then enter your password, then click "Yes" to the prompt.
</p>
<br />

<p>
<img src="https://i.imgur.com/47sQxHA.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Once logged in, go to Windows search and search "Active Directory Users and Computers", then right click "mydomain.com", then hover over "New", then click "Organizational Unit", name it "_EMPLOYEES"

Repeat this process to create a new organizational unit named "_ADMINS"

</p>
<br />

<p>
<img src="https://i.imgur.com/2AtaSSW.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
In the "_EMPLOYEES" tab, right click in the blank space on the right and create a new user. Name the user "Jane Doe" with the user logon name of "jane_admin", then click "Next"

Set password as "Password1", then uncheck the first box, and check the box beside "Password never expires", then click "Finish"


</p>
<br />

<p>
<img src="https://i.imgur.com/u06xl0m.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Right click "Jane Doe" and click "Add to a group...", then enter "domain" into the Enter the object names to select: field, then click "Check Names", then select "Domain Admins", then click "OK", then click "OK" again.
</p>
<br />

<p>
<img src="https://i.imgur.com/RhmIYtk.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Now we will log into DC-1 with Jane Doe's account.

Log out of DC-1, then go back to Remote Desktop Connection and input DC-1's Public IP address, then when prompted for credientials, enter "mydomain.com\jane_admin", then enter "Password1" as the password, then click "Yes" to the prompt.
</p>
<br />

<h2>Joining Client-1 to mydomain.com</h2>

<p>
<img src="https://i.imgur.com/QXuADDn.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Navigate to Azure, then go to "Network Watcher", then click "Topology" from the options on the left hand side, then select the resource group "AD-Lab", then click on "client-1135_z1", which is Client-1's Network Interface Card.
</p>
<br />

<p>
<img src="https://i.imgur.com/FTlxwYz.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Once in Client-1's NIC, click on "DNS Servers" from the left hand side options, then enter the Private IP address of DC-1, then click "Save"
  
Navigate to Client-1 Virtual Machine in Azure then click "Restart"
</p>
<br />

<p>
<img src="https://i.imgur.com/NAfCNGz.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Once Client-1 has restarted, remote desktop into Client-1.
  
Right click the Windows icon on the bottom left of the desktop then click "System", then click "Rename this PC (advanced) from the right hand side options, then click "Change...", then select the box beside "Domain", then input our domain we created on DC-1 (mydomain.com), then click "OK"
  
Then enter the username we created in Active Directory in DC-1 (jane_admin), then enter the password (Password1), then click "OK", then click "OK" again, then allow Client-1 to restart.
</p>
<br />

<p>
<img src="https://i.imgur.com/eNAFncA.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

<p>
Remote desktop back into DC-1 and open "Active Directory Users and Computers", then expand "mydomain.com", then click "Computers", then verify that we see Client-1 in the "Computers" container
</p>
<br />

<p>
<img src="https://i.imgur.com/0JIKK2u.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Create a new Organizational Unit (OU) named "_CLIENTS" within the "domain.com" root, then drag Client-1 from the "Computers" container into the new container we just created (_CLIENTS), then say "Yes" to the popup
</p>
<br />

<h2>Setting Up Remote Desktop for Non-Administrative Users on Client-1</h2>

<p>
<img src="https://i.imgur.com/N9Qap1o.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Remote desktop into Client-1 using the username "mydomain.com\jane_admin", then input the password "Password1"
  
Right-click the Windows icon on the bottom left of the screen, then click "System", then click "Remote Desktop" from the right-hand side options, then click "Select users that can remotely access this PC", then click "Add...", then enter "domain" into the Enter the object names to select: field, then click "Check Names", then select "Domain Users", then click "OK" on all the prompts
</p>
<br />

<h2>Creating Additional Users and Logging Into Client-1 with New User</h2>

<p>
<img src="https://i.imgur.com/nm87W6H.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Remote desktop back into DC-1 as jane_admin, then open "PowerShell_ISE" as an administrator from the Windows search bar, then open this link https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1, then go back to PowerShell, then click "New Script", then paste the contents into the space, then click "Run Script"
  
Once the script has finished running, we will have all these new users in Active Directory
</p>
<br />

<p>
<img src="https://i.imgur.com/lAsJIgQ.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
Open Active Directory Users and Computers, then click the "_EMPLOYEES" container and verify the script made new users. 
  
We will use one of these new users to attempt to log into Client-1. We will use the employee "max.muko"
  
From your physical machine, remote desktop into Client-1 and log in using "mydomain.com\max.muko" as the username and "Password1" as the password, then click "Yes" to the prompt.
  
Make sure to delete the resource groups in Azure when you are finished with the lab.
  
Congratulations! You have successfully learned to install and use Active Directory using virtual machines in Microsoft Azure.
  
  

</p>
<br />
