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
