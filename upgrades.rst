Upgrading the application
=========================

...or, upgrading the software of Scheduler itself.

The home of the Scheduler software is
`GitHub <https://github.com/XronosSchedulingLtd/scheduler>`_

If you already have a working Scheduler installation then this information
should be embedded within it but for reference, the URL for fetching
the software is https://github.com/XronosSchedulingLtd/scheduler.git

At any given moment, the "master" branch on GitHub will point to the latest
released version, whilst in addition there are tags for each release, of the
form "v1.2.0".

You might choose to track the master branch (this is the default option)
or you can lock your installation to a chosen version.  The latter is useful
if you need to do a progressive upgrade from a very old version - see below.

---------
Cron jobs
---------

Before you start to do an upgrade, think about when your various
cron jobs are configured to run.  They happen in the background and
some of them can take quite a few hours to run - those which check
all the events for student clashes and flag them.

If you start doing an upgrade whilst a cron job is running, odd things
could happen.

-------------
Release notes
-------------

Before any upgrade, read the release notes.  These are in a file
called RELEASE_NOTES in the root directory of the application, and
you can read it directly on GitHub.

There may be specific instructions there relating to a particular
release.  Release v0.28.3 is of particular note because it involves
switching to a later version of Ruby, and thus needs extra steps.

------
Backup
------

Before you start, always take a fresh backup of your database.  You
know it makes sense.

---------------------
Check current version
---------------------

Before you start you need to know two things:

* What version of software do you currently have?
* What version is your system set up to request?

The first of these is easy - unless you have a very old version of the
software, the version number is displayed in the bottom right-hand corner
of the main screen of the application.

.. note::

  If you have admin privileges within the application you can also get
  additional information by hovering your mouse pointer over this version
  number.  The hover text will tell you the both the version of Ruby and
  the version of Rails in use.

If your version is too old to display the version number like this then
you will find it in a file called "VERSION" in the root directory of
the application.

Then you want to know what version of software your system is currently
configured to expect.  This will affect how it behaves when you connect
to GitHub for an update.

The 'git status' command will give you this information.  As the scheduler
user, change into the SCHEDULER_DIR and type 'git status' and you should see
something like this:

.. code-block:: console

  $ . ~/etc/whichsystem
  $ cd $SCHEDULER_DIR
  $ git status
  On branch master
  Your branch is up-to-date with 'origin/master'
  nothing to commit, working directory clean
  $

If you have changed any local configuration files that last line won't
appear and instead you will see a list of the changed files.

.. note::

  The "Your branch is up-to-date..." line is potentially confusing.  It
  doesn't mean that your branch is necessarily up-to-date with the
  'master' branch held on GitHub.  'master' and 'origin/master' are both
  branches on your local machine.  What it means is, the last time
  your system checked with GitHub it was up to date.

The interesting line of output however is the first one - "On branch master".
That tells us that our system is set up to follow the latest release
from GitHub, even if we haven't got it yet.  If instead you get something
like this:

.. code-block:: console

  $ git status
  HEAD detached at v1.2.3
  ...

Then your system is currently locked to version 1.2.3 of the software.
We'll see why you might want to have it like this in upcoming sections
of this page.

-----------
Basic steps
-----------

Assuming your system is on branch 'master' and you simply need to upgrade
by one version, the steps are as follows:

* Stop the application
* Install the new version of software from GitHub
* Install any required support packages
* Apply any required database structure changes
* Pre-compile the CSS and JavaScript assets
* Re-start the application

Note that some care is needed if you have let your installation get
out of date.  It is not always possible to go straight from a very old
version to the current version.  See below for instructions on how to
update a very old version.

For this simple case, your upgrade session should look like this:

.. code-block:: console

  $ . ~/etc/whichsystem
  $ cd $SCHEDULER_DIR
  $ sudo service nginx stop
  [sudo] password for scheduler:
  $ git pull
  $ bundle install
  $ rake db:migrate
  $ rake assets:precompile
  $ sudo service nginx start

Note that, unless you've taken a very long time over this, you won't be
prompted for your password a second time.

.. note::

  The "git pull" command both fetches the new version from GitHub and
  places it into your application's root directory.  That's fine for
  this simple upgrade case.

------------------
Upgrading in steps
------------------

If your system is more than one release behind GitHub then the general
rule is to upgrade one release at a time.  If you have only two or three
to do then this isn't too hard.  If you have more, then see the next section.

.. note::

  Upgrading by several versions all in one go may be fine, but there
  are circumstances in which it will not work.

  Imagine you're on version 1 of the software.

  The migration to version 2 causes an update to all the user records.

  The migration to version 3 introduces a new field to the user record
  *and* adds an application software constraint that this field must always
  be populated.  It then immediately populates the field.

  If you try to go straight from version 1 to version 3, then the
  new (version 3) software will be installed before any of the database
  updates are done.  Then the database migrations are executed sequentially
  and the one for version 2 will fail because the constraint requiring the
  new field to be populated is already in place but the field does not
  exist.

  If however you upgrade from version 1 to version 2, then from version 2
  to version 3, all will be well.


Let's assume you are currently running v1.2.8 of the software
and the current version is v1.2.11.  We'll assume further that your
installation is on the 'master' branch - you just haven't got around to
updating for a while.

.. warning::

  At this point, your system's idea of what the 'master' branch is
  differs from GitHub's.  We need to bring them gently back into line.

Start by shutting down nginx (as above) and then lock your system to
v1.2.8 of the software.  Once you've done that you can bring down
all the new versions from GitHub:

.. code-block:: console

  $ . ~/etc/whichsystem
  $ cd $SCHEDULER_DIR
  $ sudo service nginx stop
  [sudo] password for scheduler:
  $ git checkout v1.2.8
  $ git fetch

Note the use of "git fetch" instead of "git pull".  "git pull" says,
"Grab the latest of everything and put my chosen version in my
application directory."  If you do that whilst your system is on the
'master' branch then you'll jump straight to the latest version - not
what is wanted in this case.

The "git fetch" command on the other hand says, "Fetch down anything new
which you can find on GitHub, but don't do anything with it for now -
just fetch it".

You're now in a position to apply the updates one by one.  Still working
on our imagined task of going from v1.2.8 to v1.2.11 you would do the
following:

.. code-block:: console

  $ git checkout v1.2.9
  $ bundle install
  $ rake db:migrate
  $ git checkout v1.2.10
  $ bundle install
  $ rake db:migrate
  $ git checkout v1.2.11
  $ bundle install
  $ rake db:migrate
  $ rake assets:precompile

And then assuming that v1.2.11 is indeed the latest version we can put
the system back into its original state with:

.. code-block:: console

  $ git checkout master
  $ sudo service nginx start

.. note::

  When you check out an explicit version like v1.2.9 you get what looks
  like a dire warning from Git about your system being in a "Detached
  HEAD" state.  This is not a problem, because you're not doing development
  work on the system - just installing specific versions of the code.

If you have set yourself up with a test system then you can short-circuit
this process by trying the update on your test system first.  If all the
database migrations run without error then it will be fine to go straight
from your start version to your target version (but see note about v0.28.3
below).

