<!-- This work is licensed under the Creative Commons Attribution-NonCommercial-ShareAlike 3.0 Unported License. To view a copy of this license, visit http://creativecommons.org/licenses/by-nc-sa/3.0/. -->
#WebApp Vulnerabilities [ETC: 1.0 Hours]
This section will be about web app vulnerabilities. In this context we will often be talking about php code but there's no reason that other web scripting languages are any more secure.

##Remote Execution
Sometimes bad coding can lead to exploits that allow execution of arbitrary code on victim's servers. Coding practices such as:
* Setting __register_globals__ on in php
* Running __eval()__ on unsantized user input
can allow remote execution vulnerabilities to exist. In the case of eval() this is very simple. Unsanitized input is passed to the server (perhaps in a get or post portion of a website request) and that data is run as a system command. (It could be a command like "rm / -rf"). register_globals is a php setting that, with unsecure coding, can allow a user to manipulate variables in other parts of the php script being run such as replacing an included script source with a remote source that has eval()'s written into it.  
For example the (awful) code below was pulled directly from a public git repository of a live website:

~~~
    if($_POST["isadmin"]==1){
        if (isset($_GET["cmd"]){
            exec($_GET["cmd"])
        }
    }
~~~
As awful as this is, web services you need to maintain during the competition could very well be coded in this way.
The lesson to be learned here is review source code and __always__ sanitize user input.

##SQL Injection
SQL Injection is possible when a website submits an SQL query that is created by concatenating prepared strings with user input to complete the query. This allows the user to inject subqueries or other requests inside the original query string and return data or bypass functionality like password requirements. For example a php script could be:
~~~
    $username = $_POST['username'];
    $password = $_POST['password'];
    
    $sqlquery = "SELECT * FROM users WHERE username='" . $username . "' AND password='" . $password . "';";
    $result = mysqli_query($sqlquery);
    
    if($result)
        echo "Password Correct!";
    else
        echo "Password Incorrect!";
~~~
Supplying the correct username and password will work, but so will SQL injection. Passing both the username and password of:
~~~
    ' OR '1'='1
~~~
will cause the query string to read:
~~~
    SELECT * FROM users WHERE username='' OR '1'='1' AND password='' OR '1'='1';
~~~
which will always evaluate to true and tell you that the password is correct. SQL injection can also be used to return records of your choosing from any table in the database even if the information should be confidential. This has been used to steal account information including social security numbers and credit card numbers from high profile targets. It was even used against us in CCDC2013 to take control of critical infrastructure until the vulnerability was patched.  

If you are not sanitizing user input your website is definitely vulnerable to sql injection. An additional measure of security is to make sure database users in your php code have only the permissions they absolutely need.

##Cross Site Scripting (XSS)
Cross site scripting uses vulnerabilities in a site's code to allow a 3rd party to supply a reference to a real site that can execute code on the users computer. For example embedding a script in a url (or post data for a link) that will be passed back to the users computer by the legitimate site allows javascript to interact with the site in question as the user or steal authentication information from the user. Again, __failing to cleanse user input__ is the major attack vector for this vulnerability:
~~~
    <?php
        echo "your name is" . $_GET["name"];
    ?>
~~~
If the url includes ?name=maome<script>do some javascript stuff</script> the html will look like:
~~~
    <body>
        your name is maome
        <script>do some javascript stuff</script>
    </body>
~~~
Where the script will be executed on the page.

Again, __you should absolutely clean your user input__  
to reiterate __you should clean your user input__  

##Cross Site Request Forgery
CSRF is similar to XSS in that a script or injected parameters will attempt to use the victims browser's previously authenticated (and valid) session or tokens to issue commands to the web app in question as if they were the user. For example if my victim's bank allowed get parameters to initiate transactions and I knew that they were already logged in or had opted for automatic login on their bank's website (which does not check referrer headers, a common mistake) a link in my email to the victim might look like this:
~~~
    <a href="https://yourbank.com/transfer?to=maome&amount=100000">Yo, check out this cute cat video</a>
~~~

##Insecure Direct Object Reference
IDOR occurs when a resource is referenced directly through a user editable parameter. The resources that are available through this interface may be leveraged by an attacker to gain data or execution privileges. For a very basic example look at the following php code:
~~~
    <?php
        echo file_get_contents($_GET["studentID"]);
    ?>
~~~
Could be a simple way to return student information stored in files like W00886514 BUT depending on the permissions the webserver this is running on the string "../secretfiles" could be used to return a file from another directory. In even less secure systems (for example those wishing to authenticate apache users as unix users using mod_auth_pam) the supplied string could be "/etc/shadow" to steal the passwords file and crack it to gain administrator access to the system.  
For this vulnerability simply do not directly reference objects through user parameters. Instead create a map so valid objects are each assigned a name or integer that is passed through the fields. In addition checking authentication for each action is always a good idea.

##Information Leak
The prime example of this is the default php_info() function. This function, if found in a php file can tell the user all sorts of information about the system. Security through obscurity (hoping that since they don't know it's not a secure version, they won't attack it or know how to attack it) should not be your primary method of securing anything but it does not hurt.  
Another way an information leak can be triggered is through intentionally submitting improper or unexpected input and viewing the error messages.  
For example a login page that presents the two errors:
~~~
    Login ID not found.
~~~
and
~~~
    Incorrect Password.
~~~
Are presenting the attacker with an easy way to harvest account information for later use by comparing error messages between different user names.  
Supplying generic error messages but including verbose logging will help prevent information leak while allowing administrators full information on errors.

##URL Access Restriction Failures
Administrative urls often require login but a surprisingly large number of websites fail to follow this security all the way through the transaction. For example admin_user_form.php may be restricted. But if inside admin_user_form.php the script admin_user_reset_password.php is called but not re-authenticated the attacker could access this page without authorization.  
To correct these failures make sure every page that needs to be secure is authenticating the user on every step of the transaction.

##Exercise
Start your vm and connect to the following address:
~~~
    http://yourvmip/login
~~~
Attempting to login should present you with an unhandled SQL error.  
Using the information in this error can you guess which table user information is stored in?  
Using a technique described above (or one found here: http://www.unixwiz.net/techtips/sql-injection.html) try to bypass the login field.  
### Additional exercise
Now that you can login it is possible to fake a login AND exploit an XSS vulnerability in the page. If you need a hint, look at the output after you login.

