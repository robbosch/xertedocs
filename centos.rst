.. contents::
   :depth: 3
..

CentOS install
==============

CentOS preparation
------------------

For .rpm family of servers. If you do not already have a server in
place, install CentOS 7 from the minimal ISO. Do not install a graphical
user environment. Only select SSH so you can access the server through
SSH. After installation, the server is up to date because of minimal
install gets the rpm’s directly from the repositories. If you used
another ISO, update the install: \``yum update`\`

Note the ip address: \``hostname -I`\`

If you did not create a user, you need to allow SSH for root.

Edit sshd_config: \``nano /etc/ssh/sshd_config`\`

change the following line:

FROM:

#PermitRootLogin without-password

TO:

PermitRootLogin yes

restart SSH: \`\`\ **systemctl restart ssh\ d\ .service**\ \`\`

Install lamp stack
------------------

For all commands, if you are not logged in as root, use sudo for all
commands.

-  **Install apache webserver**: \``yum install httpd`\`

   Start apache webserver: \``systemctl start httpd.service`\`

   Enable apache webserver so it starts after (re)boot: \``systemctl
   enable httpd.service`\`

   Check if apache is running: \``systemctl status httpd.service`\`

If you have a firewall running on your server, open ports 80 and 443

You can check if all went ok by opening a browser: http://IP_SERVER/

You should see a default apache webpage.

-  **Install mariadb database**: \``yum install mariadb-server`\`

   Start the mariadb service: \``systemctl start mariadb.service`\`

   Enable the mariadb service so it starts after (re)boot: \``systemctl
   enable mariadb.service`\`

Once MariaDB installed, it is recommended to run the following security
script that will remove some insecure default settings and disable
access to your database system: \``mysql_secure_installation`\`

Answer all questions with \`y\` You will need to create a mysql root
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

-  **Install php**: Since CentOS7 does not ship with php7.x by default,
   we need to add 2 repositories to make it available. These
   repositories are EPEL and REMI.

   Install EPEL: \``yum -y install epel-release`\`

   Refresh EPEL: \``yum repolist`\`

   Install REMI: \``sudo yum -y install
   https://rpms.remirepo.net/enterprise/remi-release-7.rpm\ \`\`

   Install yum-utils: \``yum install yum-utils`\`

   Enable REMI release 74: \``yum-config-manager --enable remi-php74`\`

   Update the repositories: \``yum update`\`

   Install php74: \``Install php php-cli php-mysql php-xml php-fpm
   php-gd php-ldap`\`

Restart apache webserver: \``systemctl restart httpd.service`\`

-  **install unzip**: \``yum install unzip`\`
-  Install nano: \``yum install nano`\` # install nano editor or use an
   editor you are familiar with.

Create Vhost for Xerte
----------------------

-  Create a directory xerte under /var/www/: \``mkdir -p
   /var/www/toolkits`\`

-  Create a configfile for the vhost: \``nano
   /etc/httpd/sites-available/toolkits.conf`\`

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
   directory: \``ln -s /etc/httpd/sites-available/toolkits.conf
   /etc/httpd/sites-enabled/`\`

   Restart apache: \``systemctl restart httpd.service`\`

-  Download Xerte installer

Since the Xerte installer is not directly available you have to create
an account on the Xerte website: https://www.xerte.org.uk. After logging
in you can download the zipfile to you desktop. Then copy the zipfile to
the server.

Example of using SCP to copy from a linux desktop to your Debian server:
\``scp /path/to/xertetoolkits_xx.zip
user@ip_server:/path/to/destination/directory`\`

Where user is a user on the Debian server. You will be prompted for the
password of the user. Make sure the user can use SSH and has sufficient
permissions to the destination directory.

If you did not copy directly to /var/www/toolkits then SSH into the
server and copy the zipfile to that location.

Go to the location of your vhost: \``cd /var/www/toolkits`\`

Unzip the zipfile: \``unzip xertetoolkits_xx.zip`\`

Since Xerte uses the httpd user, change ownership to www-data
*recursively* for the toolkits directory: \``chown -R apache:apache
/var/www/toolkits/`\`

-  Open your browser for the final install.

   Enter the url you put in your vhost: \``xerte.domain.tld`\`

   You will be redirected to the xerte setup page and greeted with
   **Welcome to Xerte Online Toolkits Installer.**

   The first page will state where xerte will be installed:
   \``/var/www/toolkits`\`

   And how you can access your install after installation:
   \``xerte.domain.tld`\`

-  Click the \``Install`\` button

   The second page will check if your server meets the requirements. If
   you see anything not meeting the requirements, fix that first before
   going further.

-  Click \``Next`\`

   The third page checks your filesystem permissions. If there are any
   problems, make sure the httpd user has sufficient permissions on the
   directories checked.

-  Click \``Next`\`

   The fourth page checks your php settings. If there are any problems
   found, then fix them first before going further.

-  Click \``Next`\`

   The fifth page is for mysql database creation and population.

   Settings for a default Xerte install:

   Database Host: \``localhost`\` # mariadb-server is installed on the
   same server so localhost is ok.

   Database username: \``xertedbadmin`\` # mysql user you created
   previously

   Database name: \``toolkits_data`\` # database you created previously

   Database password: \``password`\` # password you created previously.
   Use a strong password

   Database prefix: leave empty unless you need it for housekeeping.

-  Click \``Next`\`

   The sixth page is the MySQL Database Account Set up page.

   Re-enter xerte DB account credentials:

   Database username: \``xertedbadmin`\` # same user you enetered in
   page 5

   Database password: \``password`\` # same password you entered in page
   5

-  Click \``Next`\`

   The seventh page will create the Xerte administrator account. This
   account can log in the management page for xerte. This account can
   NOT log in xerte as a normal user or content creator.

   Admin account name: \``xerteadmin`\`

   Admin account password: \``adminpassword`\` # Make this a strong
   password and make sure to note it down.

-  Click \``Next`\`

   The nineth page is an overtview of all your settings.

The default setting for user authentication is 'Guest' - which allows
**ANY visitor** to access Xerte's front end with privileges to create,
**edit and delete ALL content**. Using 'Guest' on a public web server
(where anyone could access it) unless you have other security measures
in place is **NOT recommended**.

Choose an authentication method: Change \``Guest`\` to \``Db`\`

Click \``Save`\`

The final page is a confirmation that your Xerte install is complete. It
also shows the URL where your Xerte is availabe: \``xerte.domain.tld`\`

Since Xerte has been configured using a mysql database, you first need
to create a user to be able to log in xerte.

Go to xerte.domain.tld/management.php and log in with your xerteadmin
account

Click the users tab and add name and password for a new user.

click \``save`\`

Now go to your xerte login page: \``xerte.domain.tld`\` and log in with
your created user.

Enable HTTPS using a Lets Encrypt cerificate
--------------------------------------------

For webservers directly accessible from the Internet, it is strongly
advised to use a secure connection to the webserver. This is by
installing a certificate for the website. With Lets Encrypt there is a
free and reliable option to do this.

-  Make sure you have created a DNS record (A or CNAME) for your xerte
   website. In this case there is an A record for domain.tld and a CNAME
   pointing to domain.tld for xerte.domain.tld.
-  If you have IP6 records, make sure the AAAA record(s) point to the
   correct location. Lets Encrypt favors IP6 over IP4. If you do not
   wish to use IP6, make sure to delete all IP6 records from DNS.

-  Install certbot: \``yum install certbot python2-certbot-apache
   mod_ssl`\`

During the installation process you will be asked about importing a GPG
key. This key will verify the authenticity of the package you are
installing. To allow the installation to finish, accept the GPG key by
typing y and pressing ENTER when prompted to do so.

Getting a certificate
---------------------

Before requesting the certificate make sure you have set your
(sub)domain in DNS pointing to the IP address of the CentOS server. Make
sure you have created the Vhost on the CentOS server.

For getting a certificate for a single domain, execute the following
command: \``certbot --apache -d xerte.domain.tld`\`

If you want the certificate to be used for multiple (sub)domains execute
the following command: \``certbot --apache -d domain.tld -d
xerte.domain.tld -d sub2.domain.tld -d sub3.domain.tld`\` # The first
domain name in the list of parameters will be the **base** domain used
by Let’s Encrypt to create the certificate. For this reason, pass the
base domain name as first in the list, followed by any additional
subdomains or aliases.

The certbot command is a program that leads you through the registration
and installation of the Lets Encrypt certificate. Follow the
instructions.

It is recommended to redirect all http traffic to https.

When the installation is successfully finished, you will see a message
similar to this:

*IMPORTANT NOTES:*

-  *Congratulations! Your certificate and chain have been saved at:
   /etc/letsencrypt/live/example.com/fullchain.pem*

   *Your key file has been saved at:
   /etc/letsencrypt/live/example.com/privkey.pem*

   *Your cert will expire on\ YYYY-MM-DD\ . To obtain a new or tweaked
   version of this certificate in the future, simply run certbot again
   with the "certonly" option. To non-interactively renew \*all\* of
   your certificates, run "certbot renew"*

-  *If you like Certbot, please consider supporting our work by:*

   *Donating to ISRG / Let's Encrypt:*\ https://letsencrypt.org/donate

   *Donating to EFF:*\ https://eff.org/donate-le

