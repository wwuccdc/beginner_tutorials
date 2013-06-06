#Windows Server [ETC: XXmins]
##Introduction
###Overview
In this module, you will learn how to setup active directory, remote authentication, IIS, File sharing and remote access on Windows Server 2008, 2008 R2 or 2012. The virtual machines for the tutorials will be running Windows Server 2008 R2. Changes in Windows Server 2012 for installation are mostly graphical updates and simplification. Steps should mostly be the same. 
###What is Active Directory?
Active Directory is a directory service created by Microsoft for Windows domain networks. A Domain Controller (a Server running the role) authenticates and authorizes all users and computers on a domain. Assigning and enforcing security policies for all computers and users. Active Directory is used in majority with computer networks where user authentication is required.
###What is Remote Authentication & Remote Access? 
Remote Authentication allows for a user outside of immediate Intranet to authenticate with a server running the AC role. This then allows off-site access to resources and usage as if within an Intranet - through the Internet.
###IIS?
[IIS](http://www.iis.net/learn), also known as Internet Information Services, is Microsoft's web server application for the Windows Server family. As of the writing of this module, IIS is the third most popular web server in the world, behind Apache and nginx. 
##Modules
###Active Directory
To set up Active Directory:
~~~
Start __Server manager__ from the task bar. 
In the left navigation bar, navigate to the __Server Roles__ tab and click on the __"Add Roles"__ button.
Click Next when the Add Role Wizard opens up.
The Wizard will display a list of Roles. From the List, choose __Active Directory Domain Services__.
~~~
You will be notified that additional roles are required for Active Directory Domain Services to be installed. 
~~~
Click on __Add Requested Features__.
~~~
Active directory will now be installed. However, it is not configured yet. A warning in Server Manager will display (X) Active Directory Domain Services. 
~~~
Click on __(X) Active Directory Domain Services__. From Server Manager.
~~~
In the Summary of Active Directory Domain Services,
~~~
Click on __Run the Active Directory Domain Services Installation Wizard (dcpromo.exe)__.
_Alternatively, you may use WinKey + R and type dcpromo.exe followed by the enter key to run the wizard_
~~~
For the purpose of CCDC, we will be creating a new domain in a new forest.
~~~
Clicking __Next__, navigate to __Choose a Deployment Configuration__ and select the __Create a new domain in a new forest__ radial button.
Type in the domain name for ccdc (refer to the network diagram)
~~~
Click next until you reach __Additional Domain Controller Options__
~~~
Check __DNS server__. For the purpose of CCDC, this Windows Server machine will have two roles. Active Directory and Primary DNS.
~~~
Continue through Wizard. Note that __Restore mode Administrator account__ is an additional account created that is used for recovery.

Reboot.

You will notice that after rebooting, logging in with the Administrator account will not authenticate.
You will have to login using the Domain Administrator account - the same user and password, but with Domain specified.
~~~
user name:
CCDCDOMAIN\administrator
~~~
###Remote Authentication with Active Directory
Setting up Remote Authentication is easy. You begin just as with setting up Active Directory.
~~~
Start the __Add Roles Wizard__
In the list of server roles, select __Network Policy and Access Services___.
Click __Next__ twice.
In the list of role services, select __Routing and Remote Access Services__. This will select all the roles for Routing and Remote Access Services.
Proceed through the steps in the Add Roles Wizard to complete the installation.
~~~
Configuring __Remote Access__ is a lot of work. Please refer to this [_comprehensive_ guide](http://www.buchatech.com/2010/06/how-to-setup-vpn-access-on-server-2008/) for instructions.
###Remote Access
How that you have Active Directory set up, how do you add users?
#####Adding users to Active Directory
Click __Start__ > __Administrative Tools__ > __Active Directory Users and Computers__.
Click on the domain name specified during setup from above.

In the details pane, right-click the folder in which you want to add a user account.
~~~
Active Directory Users and Computers/domain name/folder
~~~
Go to __New__ then click __User__.
Customize the user to how you want it.

#####Creating user groups 
Go to __New__ then click __Group__.
In the __New Object - Group__ dialog, type in the name of the new group.

Choose whether you want the Group to be local to the domain, global or universal and also the group type - Security or Distribution. (We mostly only use Security).

Click __OK__.

#####Adding users to groups 
To Assign a user to a group, double-click the folder that contains the group you want to add the user to in the left hand side details pane.

In the details pane, right-click the group you want to add a user to, click __Properties__ and in the group __Properties__ dialog box that opens, click the __Members__ tab.

On the __Members Tab__, click __Add__.

Enter the name of the user, group or computer, and click __OK__.
Continue to do this for other members.

###File Sharing
Sharing files to others on the network is simple.
~~~
Right Click the folder you would like to share.
Properties > Sharing > Share...
~~~
The File Sharing dialog box will open. Specify who you would like to share this folder with. You may add a user or user group. Typing in Everyone will enable access for all local users. 
~~~
Click __Share__
~~~
You have just specified which users will have access to the folder. We will now specify _Share Permissions_ and a _Share Name_ by which the folder will be referenced and accessed by users on the network.
~~~
Click __Advanced Sharing__.
Check __Share this folder__.
~~~
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

Within Share Permissions, click the users or groups added from the second step.
Using the check boxes, specify what kind of control you would like to give.

~~~
Click OK. Twice
~~~
You have now shared a folder with specific permissions.
































