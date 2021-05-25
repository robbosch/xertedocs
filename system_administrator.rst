System Administrator
====================

Installation guide
------------------

Requires :

    PHP v5.1.2 or later. v7.3x works fine.*
    PHP v8 is not yet supported.
    Apache, nginx or some other web server that is setup to execute PHP.
    Write permission to USER-FILES.
    MySQL/MariaDB. MySQL 5x and/or MariaDB 10x or MySQL 8.0 in combination with apache 2.4 work fine.

Optional additions

    TSUGI - Tsugi enables sharing of individual Learning Objects as LTI tools. Please see docs/lti_support.html to see how Tsugi can be added to an install.
    Please note: Tsugi requires PHP version 5.6 or later
     
    ClamAV - if /usr/bin/clamscan exists, uploads will be checked for viruses. Requires appropriate AV definitions are in place.
     
    Transcoding support for video files - see/read cron/transcoding.php - when run it will attempt to convert .flv files to .mp4 files to improve template viewing on Adobe-flash-free devices.

Some assumptions:
    - The server used is an internet facing webserver. In order to be able to access the webserver from both the internet and the internal network, a reverse proxy will be set up.
    - A (sub) domain is set up in DNS where xerte will be accessible on.
    - Use a SSL certificate for https access. LetsEncrypt is probably the easiest to set up.
    - All prerequisites are installed and running.

Debian install
^^^^^^^^^^^^^^

Debian preparation
~~~~~~~~~~~~~~~~~~

For .deb family of servers. If you do not already have a server in place, install Debian from the netinstall ISO. Do not install a grafical user environment. Only select SSH so you can access the server through SSH. After installation, the server is up to date because of the netinstall. If you used another ISO, update the install: ``apt update``
    
|   Note the ip address: ``ip a``
    
|   If you did not create a user, you need to allow SSH for root.
|   Edit sshd_config: ``nano /etc/ssh/sshd_config``

|   change the following line:
|   FROM:
|   #PermitRootLogin without-password
|   TO:
|   PermitRootLogin yes

restart SSH: ``systemctl ssh restart``

Install lamp stack
~~~~~~~~~~~~~~~~~~

|   For all commands, if you are not logged in as root, use sudo.
- **Install apache webserver**: ``apt install apache2``
|   Check if apache is running: ``systemctl status apache2``

If you have a firewall running on your server, open ports 80 and 443

You can check if all went ok by opening a browser: http://IP_SERVER/
You should see a default apache webpage.

- **Install mariadb** database: ``apt install mariadb-server``
Once MariaDB installed, it is recommended to run the following security script that will remove some insecure default settings and disable access to your database system: ``mysql_secure_installation``

|   answer all questions with `y` You will need to create a mysql root password. Make sure to note it down. Be aware this is not the same account as the system root account. This is the mariadb root account. It is best practice to create separate mariadb accounts for each application you install. For the Xerte online Toolkits application we will create an account in mariadb. This way, mariadb accounts can only change the database of the application they are created for.

Open de mysql console as root: ``mysql -u root -p``

|   You will be prompted for the password. It is the password you previously created (and noted down)
|   MariaDB [(none)]> CREATE DATABASE toolkits_data; 
|   MariaDB [(none)]> GRANT ALL ON toolkits_data.* TO 'xertedbadmin'@'localhost' IDENTIFIED BY 'password' WITH GRANT OPTION;
|   MariaDB [(none)]> FLUSH PRIVILEGES;
|   MariaDB [(none)]> exit;

In this example the database name will be toolkits_data. After that the xerte database user is created and given permission on the xerte database. In this example the user **xertedbadmin** is created with password **password** (CHANGE THIS TO A SECURE PASSWORD). The GRANT option makes the account able to grant other mysql accounts permissions on the xerte database.

|   Check the newly created user by logging back in mysql with that user: ``mysql -u xertedbadmin -p``
|   log in with the newly created password for xertedbadmin. You should see a mysql prompt.
|   look for available databases: ``show databases;``
|   log out mysql: ``exit;``

- **Install php**: ``apt install php libapache2-mod-php php-mysql``

restart apache webserver: ``systemctl reload apache2``

Create Vhost for xerte
~~~~~~~~~~~~~~~~~~~~~~

