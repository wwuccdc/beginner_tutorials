#Windows Server [ETC: XXmins]
##Introduction
###Overview
In this module, you will learn how to setup active directory, remote authentication, IIS, File sharing and remote access on Windows Server 2008, 2008 R2 or 2012. The virtual machines for the tutorials will be running Windows Server 2008 R2. Changes in Windows Server 2012 for installation are mostly graphical updates and simplification. Steps should mostly be the same. Powershell commands used in 2008 for role installation and management can also be used in 2012. Though certain new commands in 2012 will not work in 2008.
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

Configuring __Remote Access__ is a lot of work. Please refer to this [_comprehensive_ guide](http://www.windowsecurity.com/articles-tutorials/authentication_and_encryption/Configuring-Windows-Server-2008-Remote-Access-SSL-VPN-Server-Part1.html) for instructions.

###File Sharing

###Remote Access

###Learning