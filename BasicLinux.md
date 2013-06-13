<!--This work is licensed under the Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License. To view a copy of this license, visit http://creativecommons.org/licenses/by-nc-sa/3.0/.-->
#Basic Linux [ETC: 20mins]
##First Steps
###Logging in
You should be prompted for a username and password. In our example the username is ccdc and the password is cdcc (ccdc reversed)
###The Shell
When you log in to most linux systems you will be running a program called a shell and located in your home directory (most often /home/<username>)  The specific version of this shell isn't very important to us yet but it's most likely BASH (Bourne Again SHell) The shell is a program that helps you run other programs through a command line interface. It has various built in functions that allow you to do things like run programs in the background and navigate the file system.
###Navigating the linux file system
####cd
The command
~~~
    cd <destination>
~~~
allows you to change directory (hence cd)  
In order to reach the root directory of the file system try:
~~~
   cd /
~~~
Using cd without any arguments will return you to your home directory.
We can use another command to view files and directories in the current directory called 'ls'
####ls
ls lists files and other directories in the current directory
~~~
    ls
~~~
May produce zero output if the current directory is empty, try
~~~
    cd /
    ls
~~~
In order to view more information on each file you can add the -la flags to ls
~~~
    ls -la
~~~
##Basic Commands and Information
####Directory Structure
From the root directory a number of directories or files will be visible
~~~
    /dev	/root
    /bin	/sbin
    /home	/etc
    /lib	/mnt
    /opt	/usr
    /var	/tmp
~~~
####Some common directories
__/root:__ the system administrator account home directory, this will look empty to anyone who is not root  
__/bin:__ Contains binary files (programs) that can be run by users  
__/sbin:__ Contains binary files like bin but is used for system binaries  
__/home:__ holds user directories for instance if your username is ccdc your home directory would be /home/ccdc (you can ccdc to that!)  
__/etc:__ Pronounced et-see, holds configuration files for many system functions and installed applications. For instance /etc/motd holds the motd information that is displayed on user login.  
__/tmp:__ A temporary directory for (you guessed it) temporary files. A common trick is to store malware files in this folder to avoid detection.  

###Making new directories
The command is simply mkdir:
~~~
    mkdir <newdirectory>
~~~
###(Re)moving files and folders
Removing files
~~~
    rm <filename>
    rm -r <directory>
~~~
Copying files
~~~
    cp <filename> <destination>
~~~
###Viewing files
cat will output the full file to your terminal, less will output a scrollable view of the file to the terminal
~~~
    cat <filename>
    less <filename>
~~~
###Using Man pages and Apropros
You may find yourself in a place where you don't know how to use a command or maybe even which command to use. This is when man or apropros can be very useful. Issuing the command:
~~~
   man <command>
~~~
Will give you all possible information about the command entered. If you have forgotten a command like cp, typing:
~~~
    apropros copy
~~~
will return man pages that have copy in the header and will help you find the correct command to use.  
Many of the above commands will only work with sudo when changing important root-owned files
###Using sudo
If you are listed in the sudoers file or are part of the sudoers group you may have access to a special command called sudo. The command allows you to execute another command as a superuser.
~~~
    sudo shutdown -r now
~~~
Allows you to run the shutdown command even through you are not root, this will be very useful as many commands require root privileges to work.

###Connecting to Other Linux Machines
Using SSH you can connect to other linux machines. The syntax is very simple:
~~~
    ssh <username>@<target>
~~~
You will be presented with a password prompt and able to use the connection just like a local terminal.

##Other Resources
This information will get you started on the remaining tutorials but is far from comprehensive. For more indepth and complete linux tutorials [this](http://www.ee.surrey.ac.uk/Teaching/Unix/) is a good resource.

##Exercise
We will be using the commands we learned above to install a simple command line text editor into our ubutnu system and use it to edit a file on our system.  
Start by logging in to the system
Vim is a very common and powerful editor and will most likely be installed on any system you're likely to run into. It is, however, not the easiest command line editor to use so we will be downloading and installing nano. If you would like to learn more about vim or vi style editors check [here](http://www.vim.org/) We will start by using sudo apt-get update to get the latest software information. Then we will use apt-get to install nano.
~~~
    sudo apt-get update
    sudo apt-get install nano
~~~~
now navigate to the root directory
~~~
    cd /
~~~
ls to look at the directories you can cd to
~~~
    ls
~~~
change directory to etc
~~~
    cd etc
~~~
use sudo nano to edit the nanorc file
~~~
    sudo nano nanorc
~~~
delete the line 'set nowrap' and press ctrl-x to write the buffer and exit
.
