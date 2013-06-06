#WebApp Vulnerabilities [ETC: 1.0 Hours]
This section will be about web app vulnerabilities. In this context we will often be talking about php code but there's no reason that other web scripting languages are any more secure.

##Remote Execution
Sometimes bad coding can lead to exploits that allow execution of arbitrary code on victim's servers. Coding practices such as:
* Setting __register_globals__ on in php
* Running __eval()__ on unsantized user input
can allow remote execution vulnerabilities to exist. In the case of eval() this is very simple. Unsanitized input is passed to the server (perhaps in a get or post portion of a website request) and that data is run as a system command. (It could be a command like "rm / -rf"). register_globals is a php setting that, with unsecure coding, can allow a user to manipulate variables in other parts of the php script being run such as replacing an included script source with a remote source that has eval()'s written into it.  
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
~~~~
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
