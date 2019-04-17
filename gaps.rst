Gaps and Suspensions
====================

-----------
Terminology
-----------

The average MIS will store your school's timetable and tell you what
would be happening in a perfect week within the school.

Scheduler aims to go one better and tell you what *is* happening this
week (or any particular week).

Sometimes some timetabled lessons do not happen - either for a particular
year group or for the entire school.  This might be for a wide range
of reasons, e.g.

- The whole school is doing a sponsored walk
- Year 11 are on study leave
- Year 6 are going to the Science Museum

For the first of these - the whole school sponsored walk - the entire
timetable is simply not going to happen.  This is what Scheduler calls
a **Gap**.  For that particular day, all lessons should simply disappear.

The other two are slightly more subtle.  Lessons will still be happening,
although some of them won't, and it can be useful to know what lessons
*would* have happened if there hadn't been this special event.  For the
case of study leave, it might well be that the staff just freed up are
then needed for supervised study periods or exam invigilation.  For the
year 6 trip, staff who would otherwise have been teaching year 6 might
be used to cover the lessons of staff accompanying the trip.

For this reason, Scheduler can keep the affected lessons visible in the
system, but mark them as not actually happening.  In Scheduler's terms,
this is a **Suspension**.

By setting up suitable events within Scheduler, the system administrator
can cause these modifications to the timetable.  The modifications are
effected each time the MIS importer runs - usually each night.

.. note::

  Gaps and Suspensions do not affect events entered by users into the
  system - just the academic and activity timetables brought in through
  an automated import.

-----------
Controlling
-----------

Obviously it isn't a good thing for every user of the system to be
able to create Gaps and Suspensions on a whim.  Chaos would ensue.

There are two special Properties within Scheduler called "Gap" and
"Suspension" which are used to implement the functionality described
here.

You should 

--------
Creating
--------

.. note::

  A new event or use an existing one?

