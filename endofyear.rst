End of year
===========

--------
MIS feed
--------

It is likely that your Scheduler installation takes a regular feed from
your school's main MIS (SchoolBase, iSAMS or WCBS/Pass) in order to keep
its idea of the timetable and teaching groups up to date.

Soon after the end of the academic year, the information in the MIS is
liable to enter a state of flux.  Firstly there will be some kind of
end-of-year processing within the MIS, ending one academic year and
starting another.  Then the person responsible for your school's
timetable will start entering the new one for the coming academic year,
and all the old teaching sets will be scrubbed (or promoted by a year)
and new ones created and populated.

It is important to make sure that your MIS and Scheduler have a common
idea of which academic year is current whenever information is fetched
across.  For this reason, it is recommended that the following steps
are carried out:

* Stop the cron job fetching data from the MIS to Scheduler.  This is
  best done immediately after your term has ended.
* Perform end-of-year processing in your MIS.
* Perform end-of-year processing in Scheduler.
* Wait until the timetable is reasonably stable and sets have been
  set up for the new year.
* Re-enable the cron job for fetching data.

One would typically expect the cron job to be re-enabled around the
middle of August, assuming your term starts in early September.  Don't
start it before the start date of the era representing your new
academic year.  See below for more about eras.

----
Eras
----

Eras exist in Scheduler primarily for the purpose of keeping track
of groups.  Typically you will set up one era per academic year - e.g.
"Academic Year 2017/18" - although if you have an institution where
the teaching groups change dramatically each term or semester then you
could use one era per term/semester.

Each group within Scheduler belongs to an era.  This is because it
is necessary to distinguish between groups of the same name from
different eras.  Typically you will have a top set for maths in year
11 each academic year, and they will all have the same name - perhaps
11MAT1.  Although two of these from consecutive academic years have
the same name, they are quite different groups - different pupils and
probably a different teacher.  We don't want them to get muddled up
and so all teaching groups belong to the current era, and will end
when the era ends.

Some other groups have continuity through the academic years.  For
instance you might have a group called "Housemasters", or "1st Orchestra".
These will vary a bit from year to year, but a large chunk of the
membership remain will remain the same.  Groups like this belong
to the Perpetual era (typically called "Perpetual Era") and carry on
through the years.  Their membership can be adjusted as required and
the system will keep a permanent record of who was in the group when.
It is thus possible to answer questions like "Who was in the 1st
Orchestra at the beginning of July last year?"

-----------
Preparation
-----------

If you are in the position of wanting to perform end-of-year processing
in Scheduler then it will probably already have the following four eras
set up:

* Previous era
* Current era
* Next era
* Perpetual era

Take a look at them (Menu => Settings to see which they are, and
Menu => Models => Eras to see their dates).

Typically your "Current era" will be set up to end at about the middle
of August (again, assuming an academic era starting in September) and
the "Next era" will be set up to start on the following day.  You can
if you wish adjust these but it's probably not a good idea to make
the eras overlap.

Looking ahead a few steps - don't restart the data feed from
MIS => Scheduler until after the start of what is currently your
"Next era".  If you want to start it earlier than mid-August, then now
would be a good time to adjust the end date of the current era and the
start date of the next era.

--------
Back ups
--------

Take a fresh full backup of your entire Scheduler database.  Put it in
a safe place.

.. warning::
   Seriously - do make sure you have an up-to-date backup before you
   start.  Much pain can be avoided in the event of finger trouble
   or whatever if only there is a good backup.


----------
Processing
----------

The actual end-of-year processing is currently performed from the command
line.  Once you're happy with the dates of your "Current" and "Next" eras,
and you have a good backup, log in as your scheduler user and proceed
as follows:

.. code-block:: console

  $ . ~/etc/whichsystem
  $ cd $SCHEDULER_DIR
  $ rails c
  Loading production environment (Rails 4.2.10)
  2.3.6 :001 > Setting.first.end_of_era
  Rolled over.
  => nil
  2.3.6 :002 > exit
  $


Since this goes through closing out all your existing groups attached
to the current era, it may take 20-30 seconds to run.

-------
Tidy up
-------

If you now go back and look at your system settings again, you should
find that what was your "Current era" is now your "Previous era", and
what was your "Next era" is now your "Current era".

This is quite a good time to create a new era for the following academic
year and then configure it in the system settings as the new "Next era".
You can always tweak its dates nearer the time of the next roll-over.

You probably don't want to re-enable the MIS import immediately (and
in any case, don't do it before the start date of what is now your
"Current era").  Wait until the information in the MIS has stabilized
a bit.  You don't need all the sets to be there, but you really don't
want to be downloading incorrect information which will just need to
be changed later.

The first run of the importer after the summer hiatus will take
much longer than normal.  A normal run *checks* every timetable
entry and group membership.  This run will need to *create* them
all, and creating a record in a database is a much slower job than
reading one.  Pick a time when the system is quiet, and allow
several hours for the run to complete.
