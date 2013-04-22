#Routers and Firewalls
##Network Basics
I  
##Network Layout  
A typical network layout will have:  
* A gateway router and a firewall, these can be seperate or combined appliances or software
* One or more workstation subnetworks
* One or more server subnetworks  

Remote threats to both the workstations and the servers exist but they will need to be protected in different ways on the network.  
##Cisco Hardware
If the competition has specific networking hardware it will very likely be Cisco. Cisco creates both the hardware and the software for their lines of routers, switches, and firewalls.
##Access Lists
There are many different syntaxes that firewalls are written in but almost all of them follow the same paradigm and general arguments. Typically software or appliances that use access lists will assume an implicit deny when allow or permit statements are included but it is never a bad idea to include an explicit 'catch-all' statement at the end of your access list. An example acl statement:
~~~
	<action> <source> <source subnet> <destination> <destination subnet> <protocol> [port]
~~~
For example:  
~~~
    block 192.168.1.0 255.255.255.0 192.168.2.0 255.255.255.0 tcp 80  
~~~
Denys web traffic (http on port 80) from the 192.168.1.0/24 network to the 192.168.2.0/24 network  
Most syntaxes allow some shortcuts to be taken when writing firewall or access control lists such as:  
~~~
	permit any 192.168.2.123 ssh  
~~~
Could allow any address to access ssh (tcp port 22 by default) services on the 192.168.2.123 host  
###Access List Requirements by Role
####Workstations
Workstations often require only basic outbound access to known services but require dynamic ports inbound in order to communicate. For example a windows 7 workstation may need very few exceptions in the firewalls:
~~~
	permit <workstation_subnet> any tcp 80
	permit <workstation_subnet> any tcp 443
	deny any any
~~~
Is enough to allow 80,443 (often http and https traffic, including windows update for security updates!) out from the workstation computers but will not allow any malicious program on the workstations to reach out on another port.
	
##Firewall Software