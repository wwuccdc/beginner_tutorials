<!--This work is licensed under the Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License. To view a copy of this license, visit http://creativecommons.org/licenses/by-nc-sa/3.0/.-->
# Windows Server [ETC: 45 minutes]
In this example we will be talking about Windows Server 2008 R3 but the neccesary steps to harden any windows servers will be similar.

This tutorial assumes basic understanding of Windows operating systems.
### Acquiring the virtual machine
Download the virtualbox disk image (.vdi) from the download link listed in [vminformation](https://github.com/wwuccdc/beginner_tutorials/blob/master/vminformation.md)
## Automatic Updates
Probably the single most important part of securing a windows server setup is updating the to most recent patches. Windows Update can be configured and started by going to:
~~~
	Start > Control Panel > Windows Updatees
~~~
## Windows Firewall
Minimizing the attack surface on a Windows server is arguably even more important than on any other system. Windows systems are the target of many malware and exploit developers and are especially prone to fall to common attacks due to misconfiguration and extraneous services being run, unprotected, on the server. Windows Firewall can help prevent this even if you have a border firewall, attacks can originate from compromised machines from within your own network.  

### Configuring the Firewall
In it's default state the windows firewall is on and blocking remote connections. This is good for our initial set up, but we will need to change this so that our services are available to client computers. Until we are updated and hardened this measure of firewall protection should remain.  
The firewall can be configured in two ways, through a GUI or through the command line using netsh. We will use the default GUI to configure the firewall in this example (another gui called the Microsoft Management Console allows more firewall configuration options at the cost of increased complexity)  
To open the windows firewall configuration either navigate to the control panel and open __Windows Firewall__ or open run and type
~~~
	firewall.cpl
~~~
Clicking on Advanced Settings will take you to an interface where you can create, edit, and delete inbound and outbound firewall rules. The list of rules you see applies only to initiating a connection. For example we know that web traffic will come in on port 80, when the http server is patched, hardened, and running we will want to open that inbound port. The server will reply on a different dynamic port but because the connection is established, that port will not have to be defined as allowed in the windows firewall.

### Initial Firewall for Windows Update
Windows update uses only two ports:
~~~
	tcp80
	tcp443
~~~
For now we will change the server to allow ONLY traffic on the ports required by windows update. In the main tab of firewall.cpl the overview will show a link to __Windows Firewall Properties__. Opening this link will display the firewall state and the default actions for inbound and outbound connections. Make sure the state is on, and change both the default connections to block and apply the changes.  
Switch to the inbound rules and make sure everything is disabled. (Ctrl-a, __Disable rule__)  
Switch to the outbound rules and do the same.
We will now create and enable a new rule to allow windows update to function. Still in outbound rules click __New rule__ and chose __Port__ in the first dialog. The connection we are trying to allow is tcp on ports "80, 443" The next dialog will ask how you would like to handle the connection, choose __Allow the connection__, we do not choose Allow the connection if it is secure because windows update transmits some non-confidential data in an unsecure manner which we still need to patch the server. Name and create the rules and windows update should be able to communicate with microsoft servers.
### Configuration for Other Services
For the most part these configurations will allow services like IIS and standalone servers to communicate with clients. These changes will be added to the inbound rule list. For example, to allow remote desktop protocol through we will open port tcp 3389 inbound.
