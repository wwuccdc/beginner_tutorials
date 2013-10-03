<!--This work is licensed under the Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License. To view a copy of this license, visit http://creativecommons.org/licenses/by-nc-sa/3.0/.-->
#Windows Server[ETC: 2.5 Hours]
###Acquiring the virtual machine
Download the virtualbox disk image (.vdi) from the download link listed in [vminformation](https://github.com/wwuccdc/beginner_tutorials/blob/master/vminformation.md)
##Introduction
###Overview
In this module, you will learn how to setup active directory, remote authentication, IIS, File sharing and remote access on Windows Server 2008, 2008 R2 or 2012. The virtual machines for the tutorials will be running Windows Server 2008 R2. Changes in Windows Server 2012 for installation are mostly graphical updates and simplification. Steps should mostly be the same. Unlike the previous modules where you are provided with a single VM training section, this entire module is to set-up and get you familiar with the VM. Please get the VM ready before procceding.
###What is Active Directory?
Active Directory is a directory service created by Microsoft for Windows domain networks. A Domain Controller (a Server running the role) authenticates and authorizes all users and computers on a domain. Assigning and enforcing security policies for all computers and users. Active Directory is used in majority with computer networks where user authentication is required.
###What is Remote Authentication & Remote Access? 
Remote Authentication allows for a user outside of immediate Intranet to authenticate with a server running the AC role. This then allows off-site access to resources and usage as if within an Intranet - through the Internet.
###IIS?
[IIS](http://www.iis.net/learn), also known as Internet Information Services, is Microsoft's web server application for the Windows Server family. As of the writing of this module, IIS is the third most popular web server in the world, behind Apache and nginx. 
##Modules
###Active Directory
To set up Active Directory:

* Start __Server manager__ from the task bar. 
* In the left navigation bar, navigate to the __Server Roles__ tab and click on the __"Add Roles"__ button.
* Click Next when the Add Role Wizard opens up.
* The Wizard will display a list of Roles. From the List, choose __Active Directory Domain Services__.

You will be notified that additional roles are required for Active Directory Domain Services to be installed. 

* Click on __Add Requested Features__.

Active directory will now be installed. However, it is not configured yet. A warning in Server Manager will display (X) Active Directory Domain Services. 

* Click on __(X) Active Directory Domain Services__. From Server Manager.

In the Summary of Active Directory Domain Services,

* Click on __Run the Active Directory Domain Services Installation Wizard (dcpromo.exe)__.
* _Alternatively, you may use WinKey + R and type dcpromo.exe followed by the enter key to run the wizard_

For the purpose of CCDC, we will be creating a new domain in a new forest.

* Clicking __Next__, navigate to __Choose a Deployment Configuration__ and select the __Create a new domain in a new forest__ radial button.
* Type in the domain name for ccdc (refer to the network diagram)

* Click next until you reach __Additional Domain Controller Options__

* Check __DNS server__. For the purpose of CCDC, this Windows Server machine will have two roles. Active Directory and Primary DNS.

Continue through Wizard. Note that __Restore mode Administrator account__ is an additional account created that is used for recovery.

Reboot.

You will notice that after rebooting, logging in with the Administrator account will not authenticate.
You will have to login using the Domain Administrator account - the same user and password, but with Domain specified.
~~~
user name:
CCDCDOMAIN\administrator
~~~
###Remote Access
How that you have Active Directory set up, how do you add users?
#####Adding users to Active Directory
* Click __Start__ > __Administrative Tools__ > __Active Directory Users and Computers__.
* Click on the domain name specified during setup from above.

* In the details pane, right-click the folder in which you want to add a user account.
~~~
Active Directory Users and Computers/domain name/folder
~~~
* Go to __New__ then click __User__.
Customize the user to how you want it.

####Creating user groups 
* Go to __New__ then click __Group__.
* In the __New Object - Group__ dialog, type in the name of the new group.
* Choose whether you want the Group to be local to the domain, global or universal and also the group type - Security or Distribution. (We mostly only use Security).
* Click __OK__.

####Adding users to groups 
To Assign a user to a group, double-click the folder that contains the group you want to add the user to in the left hand side details pane.

In the details pane, 
* Right-click the group you want to add a user to
* Click __Properties__ and in the group __Properties__ dialog box that opens
* Click the __Members__ tab.
* On the __Members Tab__, click __Add__.
* Enter the name of the user, group or computer, and click __OK__.

Continue to do this for other members.

###Remote Authentication with Active Directory
Setting up Remote Authentication is easy. You begin just as with setting up Active Directory.

* Start the __Add Roles Wizard__
* In the list of server roles, select __Network Policy and Access Services___.
* Click __Next__ twice.
* In the list of role services, select __Routing and Remote Access Services__. This will select all the roles for Routing and Remote Access Services.
Proceed through the steps in the Add Roles Wizard to complete the installation.

Configuring __Remote Access__ is a lot of work. Please refer to this [_comprehensive_ guide](http://www.buchatech.com/2010/06/how-to-setup-vpn-access-on-server-2008/) for instructions.
###File Sharing
Sharing files to others on the network is simple.

* Right Click the folder you would like to share.
* Properties > Sharing > Share...

The File Sharing dialog box will open. Specify who you would like to share this folder with. You may add a user or user group. Typing in Everyone will enable access for all local users. 

* Click __Share__

You have just specified which users will have access to the folder. We will now specify _Share Permissions_ and a _Share Name_ by which the folder will be referenced and accessed by users on the network.

* Click __Advanced Sharing__.
* Check __Share this folder__.

By default, the name of the folder will be used as the Share name. Feel free to change it as you please.
If the number of concurrent users accessing the shared folder is a concern, modify the number of simultaneous users accordingly.

If you would like users to specify which files they would like to access without being connected to the network, click the _Caching_ button and choose accordingly.

To finish setting up file sharing, we will click on _Permissions_ to configure _Share Permissions_.
~~~
FULL CONTROL - Grants the specified users permission to change file and folder permissions, execute, read, modify, create and delete files and sub-folders.
CHANGE - Grants users permission to change file and folder attributes, read, modify, execute, create and delete files and sub folders.
READ - Grants users permission to read and execute files and view and access file lists and sub-folders.
~~~
Note that Permissions are cumulative in that the user will inherit the permissions of the groups the user is a member of. However, if a permission is specifically __Denied__, it will override any permissions that are granted.

* Within Share Permissions, click the users or groups added from the second step.
Using the check boxes, specify what kind of control you would like to give.
* Click OK. Twice

You have now shared a folder with specific permissions.
##Secure the Server.
###Securing IIS.
Install the latest service pack and security updates for IIS. As well as the components running on the web server. Microsoft acknolwedges weaknesses in IIS and provides an IIS Lockdown Wizard that disables optional features of IIS to secure IIS against attacks. The IIS Lockdown Wizard can be downloaded [here](http://support.microsoft.com/kb/325864).

Enable logging for IIS, in addition, it would be good to log firewall as well.
To prevent unauthorized modifications of logs, it would be good practice to move logs away from the default location.

Enable Windows auditing - enabling Windows auditing will make it easier to backtrack any attacker's activity, ontop of logs.

###Group Policy for Active Directory
Group Policy allows you to implment specific configurations for users and computers. Policies are linked to active directory containers such as sites, domains, or organizational units. 

There are around 9 types of Group Policy security features, they are
* Account Policies
* Local Policies
* Event Log
* Restricted Groups
* System Services
* Registry
* File System
* Public Key Policies
* Internet Protocol Seucirty Policies on Active Directory

####Account Policies
Group policy account policies can be used to specify a Password Policy, allowing you to specify password requirements, such as complexity, length and life of password. Included among Password Policy is Account Lockout Policy, enabling you to lock out a user after a specified number of failed logon attempts - securing against bruteforcing of user logins. Kerberos Authentication Policy can also be modified to set the maximum lifetime of a user ticket.

####Local Computer Policies
Local Computer Policies includes Audit Policy for logging of security events, User rights Assignment allowing you to control the rights assigned to user accounts and security groups for local computers. For example, you can specify who can access computers from the network, who can log on locally to even who can shutdown the computer. One of the most important features of Local COmputer Policies I think is most important is Security Options. Security options allow you to specify policies that force users to log off after a specified period of time, securing physical attacks. the CTRL+ALT+DEL for logon is part of Security Options under Local Computer Policies. 

####Event Log Policies
You can use Event Log policies to control the settings of the application, system, and security event logs on local computers. For example, you can specify maximum log sizes, how long logged events are maintained, and log retention methods.

####Restricted Group Policies
You can define Restricted groups policies to manage and enforce the membership of built-in or user-defined groups that have special rights and permissions. Changes to group membership by the local computer administrator are overwritten by the Restricted Groups policy defined in Active Directory. Restricted Groups can be used to manage membership in the built-in groups. Built-in groups include local groups such as Administrators, Power Users, Print Operators, and Server Operators, as well as global groups such as Domain Administrators. 

####System Services
System services offer a mechanism for potential exploitation by intruders, who can take over the service or use the service as an entry point to gain access to computers and network resources. For example, an intruder can try to exploit weaknesses in a running Web server to gain access to a computer's operating system or files. Using System Services Policies, you can specify the startup mode of Windows to disable services that are unnecessary for everyday tasks of the user. You can also specify rights and permissions graned to system services when they run.

####File System Policies
You can use File System policies to configure security for files and folders and control security auditing of files and folders. For example, to ensure that only administrators can modify system files and folders, you can use File System policies to grant administrators full control over system files and folders and to grant read-only permission to other users. You can also use File System policies to prevent certain users from viewing files and folders.

####IP Security Policies on Active Directory
The policies in this section tell the server how to respond to a request for IPSec communications. The server might require secure communication, permit secure communication, or communicate without using IPSec. The predefined policies are not intended for immediate use.

###How to define Group Policies:
To create and modify Group Policies, launch the Group Policy Editor through command line, or as a MMC snap-in. 

* Click Start, type gpedit.msc in the Start Search and press Enter.
* Hold the Start key + R, type gpedit.msc and press Enter.

Depending on what you would like defined, browse through the "Folders" on the left navigation bar.

For example, to enable Roaming Profiles in Group Policy, you would go to: 
~~~
Computer Configuration\Administrative Templates\System\User Profiles\

Set roaming profile path for all users logging onto this computer
~~~

Good Luck!
