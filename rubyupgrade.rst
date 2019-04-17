Upgrading Ruby
==============

How to upgrade the Ruby interpreter used by Scheduler.

.. warning::

  All the usual caveats about having a good backup of your
  system and database apply.  Just do it.

Sometimes a new version of Scheduler will require a new version of
Ruby as well.  Ruby is an evolving language, and much work goes in
to adding new features, improving the speed and fixing security issues.

The Scheduler software aims to make use of a reasonably up-to-date
version of Ruby, whilst avoiding the bleeding edge.

When a new version of Scheduler becomes available, the release notes
will indicate whether it also requires a new version of Ruby.  This
is an unusual occurence, so the steps described here are not generally
needed.

.. note::

  Stick to the version of Ruby recommended by Scheduler.  Much testing
  is done to ensure compatibility, and whilst Scheduler itself is
  not terribly version sensitive, some of the supporting packages
  can be.

  The current recommended version of Ruby is 2.5.5, and the previous
  recommended one was 2.3.6.


These instructions are written on the assumption that you're upgrading
Ruby from version 2.3.6 to version 2.5.5, but you should adjust them
appropriately if you're installing a later version of Scheduler which
wants a later version of Ruby.

Preparatory steps
-----------------

One convenience of the upgrade process is that you can do some of
it in advance, without disturbing your running Scheduler system.

We will assume here that you're about to install Scheduler version
1.18.0, which is the first version which requires Ruby 2.5.5.  Again,
adjust for later versions.

The Ruby versions used by Scheduler are managed by the Ruby Version
Manager (RVM) which allows you to have several versions installed
at the same time.

As a first step, it's a good idea to make sure you have the latest
stable version of RVM.

::

  $ rvm get stable

Note that this is done as your Scheduler user, not as root.  All the
Ruby stuff is kept in the user's area.

.. note::

  If you have currently a very old version of RVM, it's possible
  that the above command will fail with a message saying that it
  is not possible to verify the signature on the new package.

  If that happens, you need to get the latest signing keys for RVM,
  using the following command.

  ::

    gpg --keyserver hkp://pool.sks-keyservers.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB

  Once that has completed, repeat the ``rvm get stable`` command.

With the latest version of RVM installed, installing Ruby is just
a question of typing:

::

  $ rvm install 2.5.5

This will take between 2 and 10 minutes to run, depending on the
speed of your system.  It downloads the relevant Ruby source, compiles
it and then installs it under ~/.rvm.  It will not affect the current
version of Ruby which you have installed.

The upgrade
-----------

You will switch to using this new version of Ruby as part of the
process of upgrading the Scheduler software.

As a first step, shut down Nginx.  This will prevent access to your
system for the duration of the upgrade.

::

  $ sudo service nginx stop

We can then upgrade the Scheduler software in the usual way.

::

  $ cd
  $ . etc/whichsystem
  $ cd $SCHEDULER_DIR
  $ git pull
  $ cd ..
  $ cd scheduler

Note those last two steps which are unusual and quite important.  In
getting the new version of software the file ``.ruby-version`` in the
Scheduler directory will have been changed.  By moving out of that
directory and then back into it we get RVM to notice the change.  You
will see a message from RVM saying it is switching to the new version
of Ruby.

Next we need to install all the ancillary packages used by Scheduler.
This is done using a utility called Bundler, which needs to be installed
first.

::

  $ gem install bundler --version 1.16.6
  $ bundle install

The latter command will take a couple of minutes to run, as it also
does a bit of compilation.

Then do the usual steps for an application software upgrade:

::

  $ rake db:migrate
  $ rake assets:precompile

and one extra one to set up our new version of Ruby to be used by
background jobs:

::

  $ rvm alias create scheduler ruby-2.5.5@scheduler

Finally you need to edit the file ``/etc/nginx/sites-available/scheduler``
and change the line:

::

  passenger_ruby /home/scheduler/.rvm/gems/ruby-2.3.6@scheduler/wrappers/ruby;

to read instead:

::

  passenger_ruby /home/scheduler/.rvm/gems/ruby-2.5.5@scheduler/wrappers/ruby;

It's just the version number which you're changing there - don't change
the path if you happen to be using a user called something other than
"scheduler".

This tells Passenger where to find the right Ruby for your instance of
Scheduler.

Finally, you can re-start Scheduler with:

::

  $ sudo service nginx start


