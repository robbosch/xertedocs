.. contents::
   :depth: 3
..

System Administrator
====================

Installation guide
------------------

------------------

Requirements :
~~~~~~~~~~~~~~

-  PHP v5.1.2 or later. v7.3x works fine.\*
-  PHP v8 is not yet supported.
-  Apache, nginx or some other web server that is setup to execute PHP.
-  Write permission to USER-FILES.
-  MySQL/MariaDB. MySQL 5x and/or MariaDB 10x or MySQL 8.0 in
   combination with apache 2.4 work fine.

Optional additions
~~~~~~~~~~~~~~~~~~

-  TSUGI - Tsugi enables sharing of individual Learning Objects as LTI
   tools. Please see docs/lti_support.html to see how Tsugi can be added
   to an install.Please note: Tsugi requires PHP version 5.6 or later
-  ClamAV - if /usr/bin/clamscan exists, uploads will be checked for
   viruses. Requires appropriate AV definitions are in place.
-  Transcoding support for video files - see/read cron/transcoding.php -
   when run it will attempt to convert .flv files to .mp4 files to
   improve template viewing on Adobe-flash-free devices.

Some assumptions:
~~~~~~~~~~~~~~~~~

-  The server used is an internet facing webserver. In order to be able
   to access the webserver from both the internet and the internal
   network, a reverse proxy will be set up.
-  A (sub) domain is set up in DNS where xerte will be accessible on. We
   will assume to use http(s)://xerte.domain.tld
-  Use a SSL certificate for https access. LetsEncrypt is probably the
   easiest to set up.
-  All prerequisites are installed and running.

Debian install
--------------

Debian preparation
~~~~~~~~~~~~~~~~~~

For .deb family of servers. If you do not already have a server in
place, install Debian from the netinstall ISO. Do not install a grafical
user environment. Only select SSH so you can access the server through
SSH. After installation, the server is up to date because of the
netinstall. If you used another ISO, update the install: \``apt
update`\`

Note the ip address: \``ip a`\`

If you did not create a user, you need to allow SSH for root.

Edit sshd_config: \``nano /etc/ssh/sshd_config`\`

change the following line:

FROM:

#PermitRootLogin without-password

TO:

PermitRootLogin yes

restart SSH: \``systemctl ssh restart`\`

Install lamp stack
~~~~~~~~~~~~~~~~~~

For all commands, if you are not logged in as root, use sudo.

-  **Install apache webserver**: \``apt install apache2`\`
-  Check if apache is running: \``systemctl status apache2`\`

If you have a firewall running on your server, open ports 80 and 443

You can check if all went ok by opening a browser: http://IP_SERVER/

You should see a default apache webpage.

-  **Install mariadb database**: \``apt install mariadb-server`\`

Once MariaDB installed, it is recommended to run the following security
script that will remove some insecure default settings and disable
access to your database system: \``mysql_secure_installation`\`

answer all questions with \`y\` You will need to create a mysql root
password. Make sure to note it down. Be aware this is not the same
account as the system root account. This is the mariadb root account. It
is best practice to create separate mariadb accounts for each
application you install. For the Xerte online Toolkits application we
will create an account in mariadb. This way, mariadb accounts can only
change the database of the application they are created for.

-  Open de mysql console as root: \``mysql -u root -p`\`

You will be prompted for the password. It is the password you previously
created (and noted down)

MariaDB [(none)]> \``CREATE DATABASE toolkits_data;`\`

MariaDB [(none)]> \``GRANT ALL ON toolkits_data.\* TO
'xertedbadmin'@'localhost' IDENTIFIED BY 'password' WITH GRANT
OPTION;`\`

MariaDB [(none)]> \``FLUSH PRIVILEGES;`\`

MariaDB [(none)]> \``exit;`\`

In this example the database name will be toolkits_data. After that the
xerte database user is created and given permission on the xerte
database. In this example the user **xertedbadmin** is created with
password **password** (CHANGE THIS TO A SECURE PASSWORD). The GRANT
option makes the account able to grant other mysql accounts permissions
on the xerte database.

Check the newly created user by logging back in mysql with that user:
\``mysql -u xertedbadmin -p`\`

Log in with the newly created password for xertedbadmin. You should see
a mysql prompt.

Look for available databases: \``show databases;`\`

Log out mysql: \``exit;`\`

-  **Install php**: \``apt install php libapache2-mod-php php-mysql
   php-xml php-fpm php-gd php-ldap`\`

Restart apache webserver: \``systemctl restart apache2.service`\`

-  **install unzip**: \``apt install unzip`\`

Create Vhost for Xerte
~~~~~~~~~~~~~~~~~~~~~~

-  Create a directory xerte under /var/www/: \``mkdir -p
   /var/www/toolkits`\`

-  Create a configfile for the vhost: \``nano
   /etc/apache2/sites-available/toolkits.conf`\`

   Add the following lines to the file:

<VirtualHost \*:80>

ServerName xerte.domain.tld

ServerAlias xerte.domain.tld

ServerAdmin adminuser@domain.tld

DocumentRoot /var/www/toolkits/

<Directory /var/www/toolkits/>

Options -Indexes +FollowSymLinks

AllowOverride All

</Directory>

ErrorLog ${APACHE_LOG_DIR}/xerte.domain.tld-error.log

CustomLog ${APACHE_LOG_DIR}/xerte.domain.tld.log combined

</VirtualHost>

-  Create a simlink of the configuration file in sites-enebled
   directory: \``ln -s /etc/apache2/sites-available/toolkits.conf
   /etc/apache2/sites-enabled/``

   Restart apache: \``systemctl restart apache2.service`\`

-  **Download Xerte installer**

Since the Xerte installer is not directly available you have to create
an account on the Xerte website: https://www.xerte.org.uk. After logging
in you can download the zipfile to you desktop. Then copy the zipfile to
the server.

Example of using SCP to copy from a linux desktop to your Debian server:
\``scp /path/to/xertetoolkits_xx.zip
user@ip_server:/path/to/destination/directory``

Where user is a user on the Debian server. You will be prompted for the
password of the user. Make sure the user can use SSH and has sufficient
permissions to the destination directory.

If you did not copy directly to /var/www/toolkits then SSH into the
server and copy the zipfile to that location.

Go to the location of your vhost: ``cd /var/www/toolkits``

Unzip the zipfile: ``unzip xertetoolkits_xx.zip``

Since Xerte uses the httpd user, change ownership to www-data
*recursively* for the toolkits directory: ``chown -R www-data:www-data
/var/www/toolkits/``

-  Open your browser for the final install.

   Enter the url you put in your vhost: ``xerte.domain.tld``

   You will be redirected to the xerte setup page and greeted with
   **Welcome to Xerte Online Toolkits Installer.**

   The first page will state where xerte will be installed:
   ``/var/www/toolkits``

   And how you can access your install after installation:
   ``xerte.domain.tld``

-  Click the ``Install`` button

   The second page will check if your server meets the requirements. If
   you see anything not meeting the requirements, fix that first before
   going further.

-  Click ``Next``

   The third page checks your filesystem permissions. If there are any
   problems, make sure the httpd user has sufficient permissions on the
   directories checked.

-  Click ``Next``

   The fourth page checks your php settings. If there are any problems
   found, then fix them first before going further.

-  Click ``Next``

   The fifth page is for mysql database creation and population.

   Settings for a default Xerte install:

   Database Host: ``localhost`` # mariadb-server is installed on the
   same server

   Database username: ``xertedbadmin`` # mysql user you created
   previously

   Database name: ``toolkits_data`` # database you created previously

   Database password: ``password`` # password you created previously.
   Use a strong password

   Database prefix: leave empty unless you need it for housekeeping.

-  Click ``Next``

   The sixth page is the MySQL Database Account Set up page.

   Re-enter xerte DB account credentials:

   Database username: ``xertedbadmin`` # same user you entered in
   page 5

   Database password: ``password`` # same password you entered in page
   5

-  Click ``Next``

   The seventh page will create the Xerte administrator account. This
   account can log in the management page for xerte. This account can
   NOT log in xerte as a normal user or content creator.

   Admin account name: ``xerteadmin``

   Admin account password: ``adminpassword`` # Make this a strong
   password and make sure to note it down.

-  Click ``Next``

   The nineth page is an overtview of all your settings.

The default setting for user authentication is 'Guest' - which allows
**ANY visitor** to access Xerte's front end with privileges to create,
**edit and delete ALL content**. Using 'Guest' on a public web server
(where anyone could access it) unless you have other security measures
in place is **NOT recommended**.

Choose an authentication method: Change ``Guest`` to ``Db``

Click ``Save``

The final page is a confirmation that your Xerte install is complete. It
also shows the URL where your Xerte is availabe: ``xerte.domain.tld``

Since Xerte has been configured using a mysql database, you first need
to create a user to be able to log in xerte.

Go to xerte.domain.tld/management.php and log in with your xerteadmin
account

Click the users tab and add name and password for a new user.

click ``save``

Now go to your xerte login page: ``xerte.domain.tld`` and log in with
your created user.
