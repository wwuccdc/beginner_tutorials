#Routers and Firewalls
Routers allow communication between computer networks. When allowing this communication where one network may be public and out of our control. In this case we would like the router or another peice of hardware or software to protect our network from unwanted traffic that tries to enter the network. Firewalls and some rotuers can inspect network traffic to varying degrees and block malicious or unwanted traffic based on a set of rules. This section will be about configuring a router to correctly forward data and creating a ruleset to be deployed on a firewall or router that will protect the network.
##Network Basics
Required knowledge for this section:
* Basic understanding of the (tcp/ip stack)[http://en.wikipedia.org/wiki/Internet_protocol_suite]  
##Network Layout  
A typical network layout will have:  
* A gateway router and a firewall, these can be seperate or combined appliances or software
* One or more workstation subnetworks
* One or more server subnetworks  

Remote threats to both the workstations and the servers exist but they will need to be protected in different ways on the network.  
##Cisco Hardware
If the competition has specific networking hardware it will very likely be Cisco. Cisco creates both the hardware and the software for their lines of routers, switches, and firewalls.
##Defining Firewall/ACL Rules
There are many different syntaxes that firewalls are written in but almost all of them follow the same paradigm and general arguments. An example acl or firewall statement:
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
  
Most firewall or access list implementations will parse a ruleset top down such that for the ruleset:  
~~~
    deny any any
    permit 192.168.1.0 255.255.255.0 any
~~~
The 192.168.1.0 network will still not be able to pass any packets because the 'deny any any' rule is parsed before the permit rule in the list.
####Example Cisco ACL
~~~
    permit icmp any any
	!--- Allow all ICMP such as pings from anywhere to anywhere
    permit tcp 192.168.1.0 255.255.255.0 any eq 80
    permit tcp 192.168.1.0 255.255.255.0 any eq 443
	!--- Allow our local network to access any address on ports equal to 80 or 443 for web traffic
    permit ip 192.168.1.0 255.255.255.0 192.168.2.0 255.255.255.0
	!--- Allow our local network to access our server network services on any ip protocol
    permit tcp any 192.168.1.0 255.255.255.0 gt 1023 established
	!--- Allow traffic from anywhere to our local network on any port greater than 1023 so long as the connection is established
    deny any any
	!--- Catch all and deny remaining traffic
~~~
###Access List Requirements by Role
####Workstations
Workstations often require only basic outbound access to known services but require dynamic ports inbound in order to communicate. For example a windows 7 workstation may need very few exceptions in the firewalls:
~~~
	permit <workstation_subnet> any tcp 80
	permit <workstation_subnet> any tcp 443
	deny any any
~~~
Is enough to allow 80,443 (often http and https traffic, including windows update for security updates!) out from the workstation computers but will not allow any malicious program on the workstations to reach out on another port. The workstations will use these open ports for outbound web traffic however we also need to allow traffic to return to the computers so that web pages can actually be loaded. Traffic comes back from the servers on dynamic ports so we can't simply say "let 80 and 443 flow both ways", instead the firewall or router can examine a TCP stream and decide if it is "established" meaning traffic is already moving in at least one direction (because we have no rules allowing traffic in yet, this means we must have started the connection) and add an inbound rule along the following lines:
~~~
	permit any <workstation_subnet> tcp established
	deny any any
~~~
This allows any tcp traffic on any port that is an established stream to be forwarded to the workstation computers, this is ideal because we can now browse the web but are protected from malicious programs trying to start connections on other ports and from many possible vulnerabilities that may still be open on each workstation.
##Firewall Software
