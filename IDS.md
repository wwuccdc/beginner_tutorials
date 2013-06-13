<!--This work is licensed under the Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License. To view a copy of this license, visit http://creativecommons.org/licenses/by-nc-sa/3.0/.-->
#Intrusion Detection [ETC: 45 Minutes]
##Introduction
###Overview
In this module, we will be learning and using Snort, a free an open source network intrusion prevention system and network intrusion detection system. The set-up of snort is difficult and you should have a good understanding of linux package installations, file locations, etc. before you begin.
###Getting Started
Download the latest Snort from [Snort Website](http://www.snort.org/snort-downloads). Extract the Snort Source Code to the /usr/src directory.
~~~
# cd /usr/src
# wget -0 snort-2.9.4.6.tar.gz http://www.snort.org/downloads/2320
# tar xvzf snort-2.9.4.6.tar.gz
~~~
Note that the version and link might change. Check the website to be certain.

####Installing Snort
Snort requires the dev packages libpcap and libpcre. Make sure you have them.
~~~
#apt-cache policy libpcap0.8-dev
#apt-cache policy libpcre3-dev
~~~
If the packages aren't installed, please install them.

To install Snort:
~~~
# cd snort-2.9.4.6
# ./configure
# make
# make install
~~~

Verify snort is installed using:
~~~
# snort --version
~~~

##### Create the required files and directorys for Snort
~~~
# mkdir /etc/snort
# mkdir /etc/snort/rules
# mkdir /var/log/snort
~~~
We need to create snort.conf and icmp.rules by:
~~~
# cat /etc/snort/snort.conf
include /etc/snort/rules/icmp.rules

# cat /etc/snort/rules/icmp.rules
alert icmp any any -> any any (msg:"ICMP Packet"; sid:477; rev:3;)
~~~
The files you created and the rule you created in icmp.rules allerts when there is an ICMP packet - a ping package.

To create your own rules, the structure is:
~~~
<Rule Actions> <Protocol> <Source IP Address> <Source Port> <Direction Operator> <Destination IP Address> <Destination > (rule options)
~~~
Rule Structure and Example
<table>
    <tr>
        <td><b>Structure</b></td>
        <td>Rule Actions</td>
        <td>Protocol</td>
        <td>Source IP Address</td>
        <td>Source Port</td>
        <td>Direction Operator</td>
        <td>Destination IP Address</td>
        <td>Destination Port</td>
        <td>(rule options)</td>
    </tr>
    <tr>
    	<td><b>Example</b></td>
        <td>alert</td>
    	<td>icmp</td>
    	<td>any</td>
    	<td>any</td>
    	<td>-> </td>
    	<td>any</td>
    	<td>any</td>
    	<td>(msg:"ICMP Packet"; sid:477; rev:3;)</td>
    </tr>
</table>

To run Snort:
~~~
# snort -c /etc/snort/snort.conf -l /var/log/snort/
~~~

To test and see how Snort works, attempt to ping an IP address.
~~~
# ping google.com
~~~
The rule we made above, which logs pings packets, shall rise Snort alerts.
The alerts contains
* Message on first line
* Source IP
* Destination IP
* Type of packet, and header information.

If you have different interface for the network connection, you'll need to launch Snort using the -dev -i option.
~~~
# snort -dev -i interfacename -c /etc/snort/snort.conf -l /var/log/snort/
~~~

To make Snort run as a Daemon, add -D parameter.

~~~
# snort -D -c /etc/snort/snort.conf -l /var/log/snort/
~~~

While Snort itself will not automatically block intrusions, plugins are avaliable to do so. Please note that setting up rules and such will take hours and is a delicate task.
You can learn more about the SnortSAM plugin [here](http://www.snortsam.net/).



