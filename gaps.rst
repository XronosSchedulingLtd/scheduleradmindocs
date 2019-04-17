.. _gaps:

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

You should protect these two Properties by setting up users to
control them as detailed in
:ref:`controls`

--------
Creating
--------

When setting up a **Gap** or a **Suspension**, the first thing is to
decide whether it affects the whole school or just one year group.

If you apply one of the two properties - Gap or Suspension - to an event
with no students attached then it will affect the entire school.  This
would be appropriate for something like the Whole School Sponsored Walk
mentioned above.

If there are any students also attached to the event, then the importer
will make a list of their year groups, and apply the gap or suspension
to all those year groups.

To suspend lessons for year 11, it is therefore sufficient to attach
just one year 11 student to the suspension event.  It makes things a
bit clearer though to attach the group "Year 11 students".

What you need therefore to create a **Gap** or **Suspension** is an
event with the appropriate duration, with either the Gap or the Suspension
property attached to it.  To restrict it to just one year group, attach
some students as well.

.. note::

  You can either attach the Gap/Suspension property to an existing event,
  or you can set one up specially.  Which you do depends on whether there
  is a fully suitable event already in the system.

  For the Whole School Sponsored Walk mentioned earlier, there will probably
  already be an event in the public calendar saying that it's happening.
  One can therefore just attach the "Gap" property to this event.

  On the other hand, for Year 11 study leave it might be the case that
  students can still come in to school, but have to register.  Registration
  lessons are thus still needed, but ordinary lessons are not happening.
  The event "Year 11 Study Leave" in the school's public calendar just
  has a start date and end date so it's not subtle enough.

  Instead one could create a hidden repeating event, running from 09:20
  to 16:00 on each date in that range, with the "Suspension" property
  and "Year 11 pupils" attached to it.  That way all the lessons from
  09:20 to 16:00 for year 11 pupils would be suspended, but registration
  (which runs from 09:00 to 09:20) would not.  The event would be placed
  in the Hidden event category, so no-one sees it apart from its creator.

