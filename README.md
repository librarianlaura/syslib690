# syslib690
notes on systems librarianship  
Apache2 installed successfully  
Installing the Apache Web Server
Introduction
Apache is an HTTP server, otherwise called web server software. Other HTTP server software exists. Another big one is nginx. At its most basic, an HTTP server essentially makes files available to others who are able to establish a connection to the computer and view the files with a web browser. Ergo, a web browser is, at its most basic, a file viewer.

It's important to understand the basics of an HTTP server, and therefore I ask you to read Apache's Getting Started page before proceeding with the rest of this section. Each of the main sections on that page describe the important elements that make up and serve a website, including

clients, servers, and URLs
hostnames and DNS
configuration files and directives
web site content
log files and troubleshooting
Installation
Before we install Apache, we need to update our systems first.

sudo apt update
sudo apt -y upgrade
Once the machine is updated, we can install Apache2 using apt. First we'll use apt search to identify the specific package name. I already know that a lot of results will be returned, so let's pipe the apt search command through head to look at the initial results:

sudo apt search apache2 | head
The package that we're interested in happens to be named apache2 on Ubuntu. This package name is not a given. On other distributions, like Fedora, the Apache package is called httpd. To learn more about the apache2 package, let's examine it with the apt show command:

apt show apache2
Once we've confirmed that apache2 is the package that we want, we install it with the apt install command. Press Y to agree to continue after running the command below:

sudo apt install apache2
Basic checks
One thing that makes Apache2, and some other web servers, powerful is the library of modules that extend Apache's functionality. We'll come back to modules soon. For now, we're going to make sure the server is up and running, configure some basic things, and then create a basic web site.

To start, let's use systemctl to acquire some info about apache2 and make sure it is enabled and running:

systemctl list-unit-files apache2.service
systemctl status apache2
The output shows that apache2 is enabled, which means that it will start running automatically when the computer gets rebooted.

The output of the second command also shows that apache2 is active, which means that it has started working.

Creating a web page
Since apache2 is up and running, let's look at the default web page.

There are two ways we can look at the default web page. We can use a command line web browser. There are a number available, but I like w3m.

We can also use our regular web browsers and view the site by entering the IP address of the server in our browser URL bar.

To check with w3m, we have to install it first:

sudo apt install w3m
Once it's installed, we can visit our default site using the loopback IP address (aka, localhost). From the command line on our server, we can run either of these two commands:

w3m 127.0.0.1
w3m localhost
We can also get the subnet/private IP address using the ip a command, and then use that with w3m. For example, if ip a showed that my NIC has an IP address of 10.0.1.1, then I could use w3m with that IP address:

w3m 10.0.1.1
If the apache2 installed and started correctly, then you should see the following text at the top of the screen:

Apache2 Ubuntu Default Page
It works!

To exit w3m, press q and then y to confirm exit.

To view the default web page using a regular web browser, like Firefox, Chrome, Safari, Edge, or etc., you need to get your server's public IP address. To do that, log into the Google Cloud Console. In the left hand navigation panel, hover your cursor over the Compute Engine link, and then click on VM instances. You should see your External IP address in the table on that page. You can copy that external IP address or simply click on it to open it in a new browser tab. Then you should see the graphical version of the Apache2 Ubuntu Default Page.

Note that most browsers nowadays may try to force HTTPS mode, and they also often hide the protocal from the URL. If your web page is not loading, make sure your URL is http://IP-ADDRESS and not https://IP-ADDRESS.

Please take a moment to read through the text on the default page. It provides important information about where Ubuntu stores configuration files and what those files do, and the document root, which is where website files are stored.

Create a Web Page
Let's create our first web page. The default page described above provides the location of the document root at /var/www/html. When we navigate to that location on the command line, we'll see that there is already an index.html file located in that directory. This is the Apache2 Ubuntu Default Page that we visited above in our browsers. Let's rename that index.html file, and create a new one:

cd /var/www/html/
sudo mv index.html index.html.original
sudo nano index.html
Note: we use sudo in this directory because we are working on files and directories outside our home directories. Thus, be careful here about the commands you run. Any mistake may result in deleting necessary files or directories.

If you know HTML, then feel free to write some basic HTML code to get started. Otherwise, you can re-type the content below in nano, and then save and exit out.

<html>
<head>
<title>My first web page using Apache2</title>
</head>
<body>

<h1>Welcome</h1>

<p>Welcome to my web site.
I created this site using the Apache2 HTTP server.</p>

</body>
</html>
If you have your site open in your web browser, reload the page, and you should see the new text.

You can still view the original default page by specifying its name in the URL. Remember that web browsers are, at their most basic, simply file viewers. So it makes sense that you simply have to specify the name of the file you want to view. For example, if your external IP address is 55.222.55.222, then you'd specify it like so:

http://55.222.55.222/index.html.original
Conclusion
In this section, we learned about the Apache2 HTTP server. We learned how to install it on Ubuntu, how to use systemd (systemctl) commands to check its status, how to create a basic web page in /var/www/html, how to view that web page using the w3m command line browser and our regular graphical browser,

In the next section, we will install PHP, which will provide the language needed to connect to MySQL, and thus enable more data driven web sites.
PHP installed sucessfully  
Installing and Configuring PHP
Introduction
Client-side programming languages, like JavaScript, are handled by the browser. Major browsers like Firefox, Chrome, Safari, Edge, etc. all include JavaScript engines that use just-in-time compilers to execute JavaScript code (Mozilla has a nice description of the process.) From an end user's perspective, you basically install JavaScript when you install a web browser.

PHP, on the other hand, is a server-side programming language, which means it must be installed on the server in order to be used. From a system or web administrator's perspective, this means that not only does PHP have be installed on a server, but it must also be configured to work with the HTTP server, which in our case is Apache2.

The main use of PHP is to interact with databases, like MySQL, MariaDB, PostgreSQL, etc., in order to create data-based page content. To begin to set this up, we have to:

Install PHP and relevant Apache2 modules
Configure PHP and relevant modules to work with Apache2
Configure PHP and relevant modules to work with MySQL
Install PHP
As usual, we will use apt install to install PHP and relevant modules. Then we will restart Apache2 using the systemctl command. Use apt show [package_name] to read more about each package we will install. The first command below installs the php and the libapache2-mod-php packages. The latter package is used to create a connection between PHP and Apache2.

sudo apt install php libapache2-mod-php
sudo systemctl restart apache2
We can check its status and see if there are any errors:

systemctl status apache2
Check Install
To check that it's been installed and that it's working with Apache2, we can create a small PHP file in our web document root. To do that, we cd to the /var/www/html/ directory and create a file called info.php:

cd /var/www/html/
sudo nano info.php
In that file, add the following text, then save and close the file:

<?php
phpinfo();
?>
No visit that file using the external IP address for your server. For example, in Firefox, Chrome, etc, go to:

http://55.333.55.333/info.php
Again, be sure to replace the IP below with the IP address of your server and be sure to use http and not https.

You should see a page that provides system information about PHP, Apache2, and the server. The top of the page should look like Figure 1 below:

PHP install page
Fig. 1. A screenshot of the title of the PHP install page.
Basic Configurations
By default, when Apache2 serves a web page, it looks for and serves a file titled index.html, even if it does not display that file in the URL bar. Thus, http://example.com/ actually resolves to http://example.com/index.html in such cases.

However, if our plan is to provide for PHP, we want Apache2 to default to a file titled index.php instead of index.html file. To configure that, we need to edit the dir.conf file in the /etc/apache2/mods-enabled/ directory. In that file there is a line that starts with DirectoryIndex. The first file in that line is index.html, and then there are a series of other files that Apache2 will look for in the order listed. If any of those files exist in the document root, then Apache2 will serve those before proceeding to the next. We simply want to put index.php first and let index.html be second on that line. Before modifying this file, it's good practice to create a backup of the original. So we will use the cp command to create a copy with a new name, and then we will use nano to edit the file.

cd /etc/apache2/mods-enabled/
sudo cp dir.conf dir.conf.bak
sudo nano dir.conf
Next we change the line to this:

DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
Whenever we make a configuration change, we can use the apachectl command to check our configuration:

apachectl configtest
If we get a Syntax Ok message, we can reload the Apache2 configuration and restart the service:

sudo systemctl reload apache2
sudo systemctl restart apache2
Create an index.php File
Now create a basic PHP page. cd back to the Apache2 document root directory and use nano to create and open an index.php file:

cd /var/www/html/
sudo nano index.php
Let's add some HTML and PHP to it. We will add PHP that functions as a simple browser detector. Add the following code:

<html>
<head>
<title>Broswer Detector</title>
</head>
<body>
<p>You are using the following browser to view this site:</p>

<?php
$user_agent = $_SERVER['HTTP_USER_AGENT'];

if(strpos($user_agent, 'Edge') !== FALSE) {
    $browser = 'Microsoft Edge';
} elseif(strpos($user_agent, 'Firefox') !== FALSE) {
    $browser = 'Mozilla Firefox';
} elseif(strpos($user_agent, 'Chrome') !== FALSE) {
    $browser = 'Google Chrome';
} elseif(strpos($user_agent, 'Opera Mini') !== FALSE) {
    $browser = "Opera Mini";
} elseif(strpos($user_agent, 'Opera') !== FALSE) {
    $browser = 'Opera';
} elseif(strpos($user_agent, 'Safari') !== FALSE) {
    $browser = 'Safari';
} else {
    $browser = 'Unknown';
}

if(strpos($user_agent, 'Windows') !== FALSE) {
    $os = 'Windows';
} elseif(strpos($user_agent, 'Linux') !== FALSE) {
    $os = 'Linux';
} elseif(strpos($user_agent, 'Mac') !== FALSE) {
    $os = 'Mac';
} elseif(strpos($user_agent, 'iOS') !== FALSE) {
    $os = 'iOS';
} elseif(strpos($user_agent, 'Android') !== FALSE) {
    $os = 'Android';
} else {
    $os = 'Unknown';
}

if($browser === 'Unknown' || $os === 'Unknown') {
    echo 'No browser detected.';
} else {
    echo 'Your browser is ' . $browser . ' and your operating system is ' . $os . '.';
}
?>

</body>
</html>
Next, save the file and exit nano. In your browser, visit your external IP address site (again, replace your server's IP address):

http://55.333.55.333/
Although your index.html file still exists in your document root, Apache2 now returns the index.php file instead. However, if for some reason the index.php was deleted, then Apache2 would revert to the index.html file since that's what is listed next in the dir.conf DirectoryIndex line.

Conclusion
In this section, we installed PHP and configured it work with Apache2. We also created a simple PHP test page that reported our browser user agent information on our website.

In the next section, we'll learn how to complete the LAMP stack by adding the MySQL relational database to our setup.
MySQL installed successfully  
Installing and Configuring MySQL
Introduction
We started our LAMP stack when we installed Apache2 on Linux, and then we added extra functionality when we installed and configured PHP to work with Apache2. In this section, our objective is to complete the LAMP stack and install and configure MySQL.

If you need a refresher on relational databases, see: Introduction to Relational Databases.

Install and Set Up MySQL
In this section, we'll learn how to install, setup, secure, and configure the MySQL relational database so that it works with the Apache2 web server and the PHP programming language.

First, let's install MySQL Community Server, and then log into the MySQL shell under the MySQL root account.

sudo apt install mysql-server
This should also start and enable the database server, but we can check if it's running and enabled using the systemctl command:

systemctl status mysql 
We can login to the database to test it. In order to do so, we have to become the root Linux user, which we can do with the following command:

sudo su
Note: we need to be careful when we enter commands on the command line, because it's a largely unforgiving computing environment. But we need to be especially careful when we are logged in as the Linux root user. This user can delete anything, including files that the system needs in order to boot and operate.

After we are Linux root, we can login to MySQL, run the show databases; command, and then exit with the \q command:

NOTE: we need to distinguish between the regular user prompt of our Linux accounts and the MySQL prompt below. In the following, I will use the greater than symbol > to represent the MySQL prompt. Do not type that prompt when you are using MySQL.

First, connect to the MySQL server as the MySQL root user:

mysql -u root
Then request a list of the databases:

show databases;
And the following databases should be returned:

+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
+--------------------+
3 rows in set (0.002 sec)
Note: If we are logging into the root database account as the root Linux user, we don't need to enter our password.

Create and Set Up a Regular User Account
We need to reserve the root MySQL user for special use cases and instead create a regular MySQL user, or more than one MySQL user, as needed.

To create a regular MySQL user, we use the create command. In the command below, I'll create a new user called opacuser with a complex password within the single quotes at the end (marked with a series of Xs here for demo purposes). From the MySQL prompt:

create user 'opacuser'@'localhost' identified by 'XXXXXXXXX';
If the prompt returns a Query OK message, then the new user should have been created without any issues.

Create a Practice Database
As the root database user, let's create a new database for a regular, new user.

The regular user will be granted all privileges on the new database, including all its tables. Other than granting all privileges, we could limit the user to specific privileges, including: CREATE, DROP, DELETE, INSERT, SELECT, UPDATE, and GRANT OPTION. Such privileges may be called operations or functions, and they allow MySQL users to use and modify the databases, where appropriate. For example, we may want to limit the opacuser user to only be able to use SELECT commands. It totally depends on the purpose of the database and our security risks.

From the MySQL query prompt, run the following commands to create a new database opacdb and to grant all privileges to opacdb to the MySQL user opacuser:

create database opacdb;
grant all privileges on opacdb.* to 'opacuser'@'localhost';
show databases;
Exit out of the MySQL database as the root MySQL user, and then exit out of the root Linux user account, and you should be back to your normal Linux user account:

\q
And then exit out of the Linux root user account:

exit
Note: relational database keywords are often written in all capital letters. As far as I know, this is simply a convention to make the code more readable. However, in most cases I'll write the keywords in lower case letters. This is simply because, by convention, I'm super lazy.

Logging in as Regular User and Creating Tables
We can now start doing MySQL work. As a reminder, we've created a new MySQL user named opacuser and a new database for opacuser that is called opacdb. When we run the show databases command as the opacuser user, we should see the opacdb database. Note below that I use the -p option. This instructs MySQL to request the password for the opacuser user, which is required to log in.

mysql -u opacuser -p
Then from the MySQL prompt, list the available databases and switch to the new opacdb database:

show databases;
use opacdb;
A database is not worth much without data. In the following code, I create and define a new table for our opacdb database. The table will be called books, and it will contain data describing some books. We will keep this table very simple and use only three fields:

create table books (
id int unsigned not null auto_increment,
author varchar(150) not null,
title varchar(150) not null,
copyright date not null,
primary key (id)
);
You can confirm that the table was created by running the following two commands, which lists the available tables and then describes the books table:

show tables;
describe books;
Congratulations! Now create some records for that table.

Adding records into the table
We can populate our opacdb database with some data. (I simply picked the first book listed from the NYTimes best lists of books for the years 2019-2022.) We'll use the insert command to add our records into our distribution table:

insert into books (author, title, copyright) values
('Jennifer Egan', 'The Candy House', '2022-04-05'),
('Imbolo Mbue', 'How Beautiful We Were', '2021-03-09'),
('Lydia Millet', 'A Children\'s Bible', '2020-05-12'),
('Julia Phillips', 'Disappearing Earth', '2019-05-14');
Now we can view all the records that we just created with the MySQL select command:

select * from books;
Success! Now let's test our table.

Testing Commands
We will complete the following tasks to refresh our MySQL knowledge:

retrieve some records or parts of records,
delete a record,
alter the table structure so that it will hold more data, and
add a record
Reminder: each MySQL command ends with a semi-colon. Some of the following MySQL commands are single-line, but others are multi-line. Regardless if a MySQL command is one-line or multi-line, it doesn't end until it ends with a semi-colon:

select author from books;
select copyright from books;
select author, title from books;
select author from books where author like '%millet%';
select title from books where author like '%mbue%';
select author, title from books where title not like '%e';
select * from books;
alter table books add publisher varchar(75) after title;
describe books;
update books set publisher='Simon \& Schuster' where id='1';
update books set publisher='Penguin Random House' where id='2';
update books set publisher='W. W. Norton \& Company' where id='3';
update books set publisher='Knopf' where id='4';
select * from books;
delete from books where author='Julia Phillips';
insert into books
(author, title, publisher, copyright) values
('Emma Donoghue', 'Room', 'Little, Brown \& Company', '2010-08-06'),
('Zadie Smith', 'White Teeth', 'Hamish Hamilton', '2000-01-27');
select * from books;
select author, publisher from books where copyright < '2011-01-01';
select author from books order by copyright;
\q
Install PHP and MySQL Support
The next goal is to complete the connection between PHP and MySQL so that we can use both for our websites.

First install PHP support for MySQL. We're installing some modules alongside the basic support. These may or may not be needed, but I'm installing them to demonstrate some basics.

sudo apt install php-mysql php-mysqli
And then restart Apache2 and MySQL:

sudo systemctl restart apache2
sudo systemctl restart mysql
Create PHP Scripts
In order for PHP to connect to MySQL, it needs to authenticate itself. To do that, we will create a login.php file in /var/www/html. We also need to change the group ownership of the file and its permissions so that the file can be read by the Apache2 web server but not by the world, since this file will store password information.

cd /var/www/html/
sudo touch login.php
sudo chmod 640 login.php
sudo chown :www-data login.php
ls -l login.php
sudo nano login.php
In the file, add the following credentials. If you used a different database name than opacdb and a different username than opacuser, then you need to substitute your names below. You need to use your own password where I have the Xs:

<?php // login.php
$db_hostname = "localhost";
$db_database = "opacdb";
$db_username = "opacuser";
$db_password = "XXXXXXXXX";
?>
Next we create a new PHP file for our website. This file will display HTML but will primarily be PHP interacting with our books database.

Create a file titled opac.php.

sudo nano opac.php
Then copy over the following text (I suggest you transcribe it, especially if you're interested in learning a bit of PHP, but you can simply copy and paste it into the nano buffer):

<html>
<head>
<title>MySQL Server Example</title>
</head>
<body>

<h1>A Basic OPAC</h1>

<p>We can retrieve all the data from our database and book table
using a couple of different queries.</p>

<?php

// Load MySQL credentials
require_once 'login.php';

// Establish connection
$conn = mysqli_connect($db_hostname, $db_username, $db_password) or
  die("Unable to connect");

// Open database
mysqli_select_db($conn, $db_database) or
  die("Could not open database '$db_database'");

echo "<h2>Query 1: Retrieving Publisher and Author Data</h2>";

// Query 1
$query1 = "select * from books";
$result1 = mysqli_query($conn, $query1);

while($row = $result1->fetch_assoc()) {
    echo "<p>Publisher " . $row["publisher"] .
        " published a book by " . $row["author"] .
        ".</p>";
}

mysqli_free_result($result1);

echo "<h2>Query 2: Retrieving Author, Title, Date Published Data</h2>";

$result2 = mysqli_query($conn, $query1);
while($row = $result2->fetch_assoc()) {
    echo "<p>A book by " . $row["author"] .
        " titled <em>" . $row["title"] .
        "</em> was released on " . $row["copyright"] .
        ".</p>";
}

// Free result2 set
mysqli_free_result($result2);

/* Close connection */
mysqli_close($conn);

?>

</body>
</html>
Save the file and exit out of nano.

Test Syntax
After you save the file and exit the text editor, we need to test the PHP syntax. If there are any errors in our PHP, these commands will show the line numbers that are causing errors or leading up to errors. Nothing will output if all is well with the first command. If all is well with the second command, HTML should be outputted:

sudo php -f login.php
sudo php -f index.php
Conclusion
Congratulations! If you've reached this far, you have successfully created a LAMP stack. In the process, you have learned how to install and set up MySQL, how to create MySQL root and regular user accounts, how to create a test database with play data for practicing, and how to connect this with PHP for display on a webpage.

In regular applications of these technologies, there's a lot more involved, but completing the above process is a great start to learning more.
Wordpress 
Install WordPress
Introduction
WordPress is a free and open source content management system (CMS). Originally, its focus was on providing a platform for blogging, but throughout the last decade plus, it has become a general purpose CMS that can serve as a website builder. Two sites exist to provide access to WordPress: WordPress.com and Wordpress.org. WordPress.com is a hosting solution, which means that customers can sign up and create a free WordPress site. Since its hosted, customers are only responsible for their content and not for managing the WordPress installation and its updates. Various paid plans can extend the functionality offered to WordPress.com customers.

WordPress.org is maintained by the WordPress Foundation, which oversees the development of and provides access to the WordPress software. When we download the WordPress software, we download it from WordPress.org. Unlike the hosted solution, when we install and setup WordPress on our own servers, we become responsible for administrating its installation and for keeping the software updated.

WordPress is widely used software, and because of that, it's often the focus of attack. Take a moment to read about the developers's efforts to protect WordPress: Security. We will not need to update our WordPress installs during the course of this course, but you should be familiar with the update process in case you decide to maintain your install or an install at a future date: Updating WordPress.

Libraries and WordPress
Many libraries use WordPress as as their main website and a quick web search will reveal them. For example, I quickly found an example of a (beautiful) WordPress library site for the Reading Public Library (RPL) in Massachusetts. These library websites coordinate with additional solutions that provide integrated library systems and other electronic resource services. RPL, for instance, connects their WordPress installation, which serves as their main website page, with the open source Evergreen ILS, which serves their OPAC. Check this by clicking on RPL's Library Catalog link, and you will see that it takes you to a different URL.

Aside: it is this need to coordinate so many services across all these websites that in part drives the need to develop standards for data exchange and work flow processes. For those of you have taken my electronic resource management course, you will recall we spent an entire module on this topic.

Many library websites are partitioned like this. Thus, when we install WordPress soon, it is as if we are only installing the front entrance to the library. Libraries are generally like this. They have one main website (like https://libraries.uky.edu) but then connect to other sites that provide access to OPACS, discovery systems, OverDrive or other eBook vendors, bibliographic databases, and more. This is part of the confusion around how libraries provide electronic resources. There are efforts to make all these components connect more seamlessly (e.g., through discovery systems), but if we were to model this to the walking around world, it would be like having a library that has multiple buildings, where each building provides one thing: one building for books, one building for journals, another building for other journals, another building for another set of journals, another building for looking up where to find journals, another building for special collections, and so on. I digress.

You can read the announcement about RPL's WordPress launch at: Reading Public Library Launches New WordPress Site. The above announcement page also describes how various plugins were used to offer patrons additional functionality. These include plugins to display business hours and to manage events and event attendees. Plugins are often used with WordPress sites to offer all sorts of additional capabilities. Currently, there are over 60 thousand plugins available for WordPress, but some are of higher quality and utility than others. In addition to the thousands of available plugins, there are over 10 thousand free themes for WordPress sites. Plus, many businesses offer paid themes or can offer customized themes based on customer needs. These themes can drastically alter the appearance and usability of a WordPress site.

Installation
So far I have shown you how to install software using two methods:

using the apt command
downloading from GitHub
In this lesson, we are going to install WordPress by downloading the most recent version from WordPress.org and installing it manually. The WordPress application is available via the apt command, but the apt process makes it a bit more confusing than it should be, oddly.

We are going to kind of follow the documentation provided by WordPress.org. You should read through the documentation before following my instructions, but then follow the process I outline here instead because the documentation uses some different tools than we'll use.

Another reason we do this manually is because it builds on what we have learned by building our bare bones ILS. That is, the two processes are similar. In both cases, we create a specific database for our platform, we create a specific user for that database, and we provide login credentials in a specific file.

First, read through but don't follow the following instructions:

How to install WordPress

Customized Installation Process
After you have read through the WordPress.org documentation, follow the steps below to complete the manual install:

Step 1: Requirements
All major software has dependencies. For example, our bare bones OPAC depends on MySQL and PHP to provide the database (MySQL) and the glue (PHP) between our HTML and the database. The same is true for WordPress. However, since WordPress is much more complicated software than our bare bones OPAC, its dependencies are stricter. This means that when we plan to download software outside of the apt ecosystem, we need to make sure that our systems meet the requirements for our installation. The WordPress.org Requirements page states that the WordPress installation requires at least PHP version 7.4 and MySQL version 5.7. We can check that our systems meet these requirements with the following commands:

php --version
mysql --version
The output from php --version shows that our systems have PHP 7.4.3, which is greater than PHP 7.4. The output from mysql --version show that our systems have MySQL 8.0, which is greater than MySQL 5.7. This means we can proceed.

Next, we need to add some additional PHP modules to our system to let WordPress operate at full functionality. We can install these using the apt command:

sudo apt install php-curl php-xml php-imagick php-mbstring php-zip php-intl
Then restart Apache2 and MySQL:

sudo systemctl restart apache2
sudo systemctl restart mysql
Step 2: Download and Extract
The next step is to download and extract the WordPress software, which is downloaded as a tar.gz file. This is very much like a compressed zip file. Although we only download one file, when we extract it with the tar command, the extraction will result in a new directory that contains multiple files and subdirectories. The general instructions include:

Change to the /var/www/html directory.
Download the latest version of WordPress using the wget program.
Extract the package using the tar program.
Specifically, we do the following on the command line:

cd /var/www/html
sudo wget https://wordpress.org/latest.tar.gz
sudo tar -xzvf latest.tar.gz
As noted in the WordPress documentation, this will create a directory called wordpress in the same directory. Therefore the full path of your installation will located at /var/www/html/wordpress

Step 3: Create the Database and a User
The WordPress documentation describes how to use phpMyAdmin to create the database and a user for WordPress. phpMyAdmin is a graphical front end to the MySQL relational database that you would access through the browser. But I like to minimize the software that we install on servers to reduce the server's security exposure. Therefore, we are going to create the WordPress database and a database user using the same process we used to create a database and user for our bare bones OPAC. The general instructions are:

Switch to the root Linux user
Login as the MySQL root user
Specifically, we do the following on the command line:

sudo su
mysql -u root
The mysql -u root command places us in the MySQL command prompt. The next general instructions are to:

Create a new user for the WordPress database
Be sure to replace the Xs with a strong password
Create a new database for WordPress
Grant all privileges to the new user for the new database
Examine the output
Exit the MySQL prompt
Specifically, this means the following (be sure to replaces the Xs with a unique and strong password of your own):

create user 'wordpress'@'localhost' identified by 'XXXXXXXXX';
create database wordpress;
grant all privileges on wordpress.* to 'wordpress'@'localhost';
show databases;
\q
Step 4: Set up wp-config.php
When we created our bare bones OPAC, we created a file called login.php that contained the name of the database (e.g., opacdb), the name of the database user (e.g., opacuser), and the user's password. WordPress follows a similar process, but instead of login.php, it uses a file called wp-config.php.

Follow these general steps:

Change to the wordpress directory, if you haven't already.
Copy and rename the wp-config-sample.php file to wp-config.php.
Edit the file and add your WordPress database name, user name, and password in the fields for DB_NAME, DB_USER, and DB_PASSWORD.
This means that we specifically do the following:

cd /var/www/html/wordpress
sudo cp wp-config-sample.php wp-config.php
sudo nano wp-config.php
In nano, add your database name, user, and password in the appropriate fields, just like we did with our login.php file for our bare bones OPAC.

Additionall, we want to disable FTP uploads to the site. To do that, navigate to the end of the file and add the following line:

define('FS_METHOD','direct');
Step 5: Optional
The WordPress files were installed at /var/www/html/wordpress. This means that your site would be located at:

http://11.111.111.11/wordpress
If you want to, you can rename your wordpress directory to something else. The WordPress documentation uses blog as an example. But it could be something else, like the name of a fictional library that you might be using WordPress to build a site. If you decide to change it, be sure to keep the name lowercase and one word (no spaces and only alphabetic characters). For example, if I want to change mine to blog, then:

cd /var/www/html
sudo mv wordpress blog
Step 6: Change File Ownership
WordPress will need to write to files in the base directory. Assuming your still in your base directory, whether that is /var/www/html/wordpress or /var/www/html/blog or like, run the following command:

sudo chown -R www-data:www-data *
Step 7: Run the Install Script
The next part of the process takes place in the browser. The location (URL) that you visit in the browser depends on your specific IP address and also includes the directory in /var/www/html that we extracted WordPress to or that you renamed if you followed Step 5. Thus, if my IP address is 11.111.111.11 and I renamed by directory to blog, then I need to visit the following URL:

http://11.111.111.11/blog/wp-admin/install.php
IF I kept the directory named wordpress, then this is the URL that I use:

http://11.111.111.11/wordpress/wp-admin/install.php
Finishing installation
From this point forward, the steps to complete the installation are exactly the steps you follow using WordPress's documentation.

Most importantly, you should see a Welcome screen where you enter your site's information. The site Username and Password should not be the same as the username and password you used to create your WordPress database in MySQL. Rather, the username and password you enter here are for WordPress users; i.e., those who will add content and manage the website.

Two things to note:

We have not setup Email on our servers. It's actually quite complicated to setup an email server correctly and securely, but it wouldn't work well without having a domain name setup anyway. So know that you probably should enter an email when setting up the user account, but it won't work.

Second, when visiting your site, your browser may throw an error. Make sure that the URL is set to http and that it's not trying to access https. Setting up an https site also generally requires a domain name, but we are not doing that here. So if there are any problems accessing your site in the browser, be sure to check that the URL starts off with http.

Conclusion
Congrats on setting up your WordPress library site. It's now time to explore and build a website. Use free themes and free plugins to alter the look of the site, its usability, and its functionality. Try to create a nice looking website. Generally, your goal for the next week is to create an attractive, yet fictional, front entrance for a library website. It's also a break from the command line!
Omeka  
Install Omeka
Omeka is an "Open-source web publishing platforms for sharing digital collections and creating media-rich online exhibits." Most if not all of you have already used Omeka in a prior course. Here our task is not to learn information/knowledge organization, per se, but to learn how to administer the Omeka digital library platform.

The Task
So far we have created our own bare bones OPAC/ILS and we have downloaded, installed, and configured WordPress on our servers. We will use the same basic process to download, install, and configure Omeka.

However, instead of providing comprehensive instructions, your goal is to take what you learned from the bare bones OPAC/ILS and WordPress assignments, and apply them to the Omeka installation and setup. Below are some additional prerequisites that you should complete first. After you've completed them, move on to the General Steps section to remind yourself of the overall process.

You can do it! Be sure to ask and discuss on our chat server.

Prerequisites
When we installed WordPress, we installed most of the prerequisites that Omeka needs, but there are a couple of additional things we need to do.

Some prerequisites:

Install ImageMagick: this is a suite of utilities to work with photo files. It's used by Omeka to create thumbnail images of any photo uploaded to the digital library. See Imagemagick for more information.
sudo apt install imagemagick
Enable Apache mod_rewrite. This is an Apache module used to rewrite URLs. Omeka uses this to create appropriate URLs for items and collections in its digital libraries.
sudo a2enmod rewrite
You should be instructed to restart Apache after enabling rewrite:

sudo systemctl restart apache2
General Steps
You have already completed all the steps below when you created a bare bones OPAC/ILS and installed WordPress. Your task is to apply what you've learned by completing an Omeka installation on your own. (You can work together and discuss on our chat server.)

Note that the process is very similar to what we have already done with our bare bones OPAC/ILS and our WordPress installations. Use this handbook to remind you of the specific commands. In short, you are going to complete the following steps:

Create a new user and a new database in MySQL for the Omeka installation (do not re-use the WordPress database, user, or credentials).
Use wget from your server to download Omeka Classic as a Zip file and extract it in /var/www/html:
https://github.com/omeka/Omeka/releases/download/v3.1/omeka-3.1.zip
unzip it with the unzip command, which you will have to install with the apt command.
the extracted directory will be named omeka-3.1. You might want to rename it simply omeka.
In the extracted directory, find the db.ini file and add your database credentials, and replace all values containing XXXXXX, with the appropriate information. This is the same thing we did with the login.php file for our bare bones OPAC/ILS and the wp-config.php file for WordPress.
Use the chown command like we did with WordPress on the files directory in the omeka directory. The user and owner should again be www-data.
Restart Apache2 and MySQL
In your web browser, go to http://your-ip-address/omeka/ and complete the setup via the web form, just like you did with WordPress.
Helpful Links
The user manual below is helpful, but it does not provide explicit instructions.

Be sure to download Omeka Classic and not Omeka S.

Omeka: https://omeka.org/
Omeka Classic: https://omeka.org/classic/
Omeka Classic User Manual: https://omeka.org/classic/docs/
Koha
Install Omeka
Omeka is an "Open-source web publishing platforms for sharing digital collections and creating media-rich online exhibits." Most if not all of you have already used Omeka in a prior course. Here our task is not to learn information/knowledge organization, per se, but to learn how to administer the Omeka digital library platform.

The Task
So far we have created our own bare bones OPAC/ILS and we have downloaded, installed, and configured WordPress on our servers. We will use the same basic process to download, install, and configure Omeka.

However, instead of providing comprehensive instructions, your goal is to take what you learned from the bare bones OPAC/ILS and WordPress assignments, and apply them to the Omeka installation and setup. Below are some additional prerequisites that you should complete first. After you've completed them, move on to the General Steps section to remind yourself of the overall process.

You can do it! Be sure to ask and discuss on our chat server.

Prerequisites
When we installed WordPress, we installed most of the prerequisites that Omeka needs, but there are a couple of additional things we need to do.

Some prerequisites:

Install ImageMagick: this is a suite of utilities to work with photo files. It's used by Omeka to create thumbnail images of any photo uploaded to the digital library. See Imagemagick for more information.
sudo apt install imagemagick
Enable Apache mod_rewrite. This is an Apache module used to rewrite URLs. Omeka uses this to create appropriate URLs for items and collections in its digital libraries.
sudo a2enmod rewrite
You should be instructed to restart Apache after enabling rewrite:

sudo systemctl restart apache2
General Steps
You have already completed all the steps below when you created a bare bones OPAC/ILS and installed WordPress. Your task is to apply what you've learned by completing an Omeka installation on your own. (You can work together and discuss on our chat server.)

Note that the process is very similar to what we have already done with our bare bones OPAC/ILS and our WordPress installations. Use this handbook to remind you of the specific commands. In short, you are going to complete the following steps:

Create a new user and a new database in MySQL for the Omeka installation (do not re-use the WordPress database, user, or credentials).
Use wget from your server to download Omeka Classic as a Zip file and extract it in /var/www/html:
https://github.com/omeka/Omeka/releases/download/v3.1/omeka-3.1.zip
unzip it with the unzip command, which you will have to install with the apt command.
the extracted directory will be named omeka-3.1. You might want to rename it simply omeka.
In the extracted directory, find the db.ini file and add your database credentials, and replace all values containing XXXXXX, with the appropriate information. This is the same thing we did with the login.php file for our bare bones OPAC/ILS and the wp-config.php file for WordPress.
Use the chown command like we did with WordPress on the files directory in the omeka directory. The user and owner should again be www-data.
Restart Apache2 and MySQL
In your web browser, go to http://your-ip-address/omeka/ and complete the setup via the web form, just like you did with WordPress.
Helpful Links
The user manual below is helpful, but it does not provide explicit instructions.

Be sure to download Omeka Classic and not Omeka S.

Omeka: https://omeka.org/
Omeka Classic: https://omeka.org/classic/
Omeka Classic User Manual: https://omeka.org/classic/docs/
