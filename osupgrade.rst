Upgrading Debian
================

How to upgrade the underlying operating system - Debian GNU/Linux.
This section is written to deal specifically with the upgrade
from Debian 8 (Jessie) to Debian 9 (Stretch) because that involves
some particular issues, but it should be useful for any such upgrade.

Debian GNU/Linux is famed for its stability - once a release is
issued it's changed only to fix security issues and it lasts a good
long time.  This makes it ideal for running servers which need to
Just Work(TM).

However, even Debian releases eventually go out of date and stop
receiving security fixes, so the point comes where one needs to
upgrade.  Debian Jessie is due to reach end of life on 30th June, 2020.
At the time of writing that's more than a year away, but it does
no harm to plan ahead.

Background
----------

Upgrading Debian is generally pretty simple.  You edit /etc/apt/sources.list
(and all the files under /etc/apt/sources.list.d) changing the name
of the release that you want, then do:

::

  $ sudo apt-get update
  $ sudo apt-get upgrade
  $ sudo apt-get dist-upgrade

Then re-boot and you're done, *but*...

There are some small extra steps needed to make sure your Scheduler
installation still works.

.. note::

  You may be interested to check out the full
  `Debian upgrade instructions <https://www.debian.org/releases/stable/amd64/release-notes/ch-upgrading.html>`_

The two things which make your Scheduler system upgrade slightly
more complicated are:

- MySql
- Nginx + Passenger

Up to and including release 8 (Jessie) the Debian distribution included
the MySql database system as one of its packages.  With the release of
version 9 (Stretch) they decided to include MariaDB (a fork of MySql)
instead.  Rather unfortunately they chose to name the packages
containing MariaDB as if they were direct replacements for MySql.
They are called "mysql-server", etc.

Thus, if you do a plain upgrade your MySql installation will be ripped
out and replaced with MariaDB.  The latter is meant to be a compatible
replacement, but initial testing quickly threw up a number of incompatible
changes which mean that it isn't.

To keep Scheduler running properly, you need to keep MySql on your
system, and the following instructions tell you how to do this.

Between Debian 8 and Debian 9 there was also a slight change to how 
Nginx + Passenger are installed.

In Debian 8, you need to install a special version of Nginx, packaged
by Phusion (the makers of Passenger) and compiled to include Passenger.

In Debian 9, this is no longer necessary - you can install the standard
Nginx packages from the Debian distribution, then add Phusion Passenger
as a plug-in module.  This is much tidier, but means an extra step is
needed to undo the way it was installed originally.

Step by step
------------

The first thing to check is that you have enough free disk space for
the upgrade.  5G free should be plenty.

If you are running your Scheduler installation as some kind of Virtual
Machine, you might want to take a snapshot of its state before you start.
That way you have an easy disaster recovery path if things go horribly
wrong.  In any case, make sure you have a good backup of your database
and system.

You will need to have root access to your system, or at least full
access to sudo.  All the following assumes you are logged in as an
ordinary user with sudo access.

The whole process will take something of the order of 1-2 hours depending
on the speed of your system and Internet connection.  It's something
to do at the weekend, or during a school holiday.


- Step 1 - Shut down Nginx

  We need to stop access to Scheduler completely, and in any case
  we're going to do interesting things to Nginx which would stop
  things from working.

  ::

    $ sudo service nginx stop

  This will stop both the Nginx web server, and the Passenger processes
  which run Scheduler.

- Step 2 - Uninstall Nginx and Passenger

  We can now uninstall both Nginx and Passenger.  Don't worry - this
  won't lose any of your individual configuration.

  ::

    $ sudo apt-get remove nginx-extras nginx-commong nginx passenger passenger-dev passenger-doc

- Step 3 - Switch to using non-Debian MySql packages

  The makers of MySql provide packages explicitly designed to work on
  Debian - both Debian 8 and Debian 9.  We can install those to
  replace the ones provided by Debian.

  ::

    $ wget https://dev.mysql.com/get/mysql-apt-config_0.8.12-1_all.deb
    $ sudo dpkg -i mysql-apt-config_0.8.12-1_all.deb

  At this point you'll get a dialogue asking you which version of
  MySql you want.  Accept the suggested version - 5.6.

  ::

    $ sudo apt-get update
    $ sudo apt-get dist-upgrade

  Because you already have the debian MySql packages installed, the
  last command will upgrade them to the ones provided by MySql
  themselves.

- Step 4 - Upgrade the Debian GNU/Linux operating system

  This is the process mentioned briefly at the top of this page.

  Edit ``/etc/apt/sources.list`` and all the files under
  ``/etc/apt/sources.list.d``.  There should be two in the latter
  directory - one for MySql and one for Passenger.  Change all
  occurrences of "jessie" to "stretch".

  In vim you can do this with:

  ::

    :g/jessie/s//stretch/g


  Then do the actual upgrade with:

  ::

    $ sudo apt-get update
    $ sudo apt-get upgrade
    $ sudo apt-get dist-upgrade
    $ sudo apt-get clean

  Those middle two can take half an hour to an hour each, and have the
  annoying trick of stopping after a while to ask you a question.

  Each of them will first download all the needed files, then start
  the upgrade.  The main point for asking for input is between these
  two steps, but it can happen at any point in the upgrade.

  Once those steps have completed you can re-boot your system and
  it should then be running Debian 9 (Stretch).

- Step 5 - Temporarily disable Scheduler's Nginx configuration

  We're about to re-install Nginx, but when it first starts it
  won't have the Passenger plugin.  Since Scheduler uses Passenger,
  the Scheduler configuration file will prevent Nginx from starting.

  The actual Scheduler configuration file exists as ``/etc/nginx/sites-available/scheduler``, and then there is a link to it in ``/etc/nginx/sites-enabled``.
  Delete the link with the following command:

  ::
  
    $ sudo rm /etc/nginx/sites-enabled/scheduler

  The original file will still exist in ``/etc/nginx/sites-available``.

- Step 6 - Install Nginx

  ::

    $ sudo apt install nginx

- Step 7 - Add Phusion Passenger

  ::

    $ sudo apt install libnginx-mod-http-passenger

- Step 8 - Re-enable Scheduler

  ::

    $ sudo ln -s /etc/nginx/sites-available/scheduler /etc/nginx/sites-enabled

- Step 9 - Re-start Nginx

  ::

    $ sudo service nginx restart


And you should be there!
