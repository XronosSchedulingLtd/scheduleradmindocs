Locations (and aliases)
=======================

Scheduler uses the term "Location" to refer to any location where you
might want to schedule events - these might be classrooms, meeting rooms,
halls, fields, or anything you choose.

In dealing with locations, it is important to understand also the
concept of a "Location Alias" - why they exist and what they do for you.

---------
Locations
---------

The location records within Scheduler are fairly simple and straightforward
to understand.  Each one has a name, and refers to a real identifiable
place within your organisation.  Typically the name will be fairly short
and could be a classroom identifier (e.g. SB112) or other short name.

The internal event processing of Scheduler uses location records *only* -
it has no interest at all in Location Aliases.  Location records are
attached to events in just the same way as records relating to people,
services etc.

Each real place within your organisation should have one (and only one)
Location record within Scheduler.

A location with no aliases (see below) will always be referred to by
the name in its Location record.  Adding aliases can affect the way the
name is displayed (and searched for), but never has any effect on the
Location record itself, nor how it is attached to events.

One place - one Location record.

----------------
Location Aliases
----------------

Location Aliases exist within Scheduler to cope with several related
issues.

* A location might be stored in the MIS with a less than friendly name.
* A location might be stored in the MIS more than once under different
  names. (It happens!)
* A location might be referred to by more than one name within the school.
  E.g. as SB112 when used as a classroom, but as "Governors' Meeting Room"
  when used for meetings.

Each Location Alias record contains a name, and is linked to a Location
record.  The name in the Location Alias record need not be the same
as the name in the Location record - and indeed it often isn't.  One
Location record can have as many Location Alias records as is needed.

To cope with the first issue, data import from the school's MIS is always
done by way of a Location Alias record.  Each reference in the MIS to
a location causes Scheduler's importer to find a corresponding Location
Alias, and thus the correct Location record.  The name stored in the
Location record can be different from the one used by the MIS and the
lookup will still work.

.. note::
  In reality, after the initial record creation, the Location Alias
  record will be found not by name but by reference number.  It is
  however useful to retain the MIS's idea of the location's name in
  the Location Alias record for documentation purposes.

Very similar processing takes care of the second issue.  If a single
location exists more than once in the MIS, then Scheduler copes by
having the appropriate number of Location Alias records, each matching
one instance in the MIS, and all linked to the same Location record.
See the section below on
:ref:`initial import <initial-import>`
for details on how to set this up.

Each time the MIS refers to any of its duplicate entries, Scheduler
will understand that they all mean the same place.

For the case where users refer to the same location by two or more
different names, some additional facilities of the Location Alias record
come into play.

Imagine a school has a library, which also has a classroom number - say,
BR218.  People at the school might refer to it by either name, but it's
the same place.

For this instance, the Location record should be set up with a name
of "BR218", and then a Location Alias record should be created with
the name "Library", and its "Display" flag ticked.  This will cause
the system as a whole, when wanting to refer to the location by a long
name, as "BR218 / Library".  This name is referred to as its "Display
name" and is constructed by taking the name from the Location record,
and following it with the names of each of the Location Alias records
which have the "Display" flag ticked.

Any searches will be fulfilled using this same display name, so users
can find the location in Scheduler using either name.

Scheduler also has the concept of a long or "Friendly" name for a location.
Using the earlier example of SB112 being the Governers' Meeting Room,
it might also be referred to by the abbreviation "GMR".  It could
be set up in Scheduler as follows:

* Location record containing the name "SB112"
* Location alias containing the name "GMR", with "Display" ticked.
* Location alias containing the name "Governors' Meeting Room", with both
  "Display" and "Friendly" ticked.

This will result in a display name of "SB112 / GMR / Governors' Meeting Room",
making it clear that they are all the same place, and allowing searches
for any of these strings to find it.

Reports intended for the general public to read (e.g. the public view
of a public calendar) will use the friendly version of a location's
name.  In this case, the public will see "Governors' Meeting Room"
rather than an internal acronym like GMR, which might not be understood.

Code which needs a short name for a location (e.g. code to print a timetable)
will always use just the name from the Location record and ignore
any aliases.  This is why it makes sense to ensure that each Location record
contains a short, clear name for its location.


.. _initial-import:

------------------
Initial MIS import
------------------

When you first do a data import from your MIS, it will cause the
creation of a lot of pairs of Location and Location Alias records.
It's best to do this before you start manually creating Location
records.  After the initial import, you can add in any locations
which the MIS doesn't know about.

.. note::
  The MIS importer creates Location and Location Alias records as
  pairs - one for each location in the MIS.  The Location Alias record
  in this pair exists solely so that it can find the same record for
  subsequent imports, even if you've renamed the Location, or even
  deleted it and linked the Location Alias to a different Location.

  When you create your own Location records you don't need to create
  any corresponding Location Alias records, unless you want the Location
  to have multiple names.

If there is a location which appears twice in the MIS but is really
just one place then you can merge the records after the initial import.

Take the example of BR218 being the library as well, and let's assume
it appears as two separate locations in the MIS.  After the initial
import you will have:

* Location record, named BR218
* Location Alias, also named BR218, linked to the Location BR218
* Location record, named Library
* Location Alias, also named Library, linked to the Location Library

We want to keep both those Location Alias records, because they will
be used in subsequent data imports, but we want only one Location record.
We also want all events which reference either of the separate
Location records to reference instead the one merged record.

Happily, there is a helper method which will do all this for you,
although it does need to be invoked from the command line.

Decide which one makes the best short name - in this case BR218.
You then absorb the other location record into it.

.. code-block:: console

  $ . ~/etc/whichsystem
  $ cd $SCHEDULER_DIR
  $ rails c
  Loading production environment (Rails 4.2.10)
  2.3.6 :001 > l = Location.find_by(name: "BR218")
  ...
  2.3.6 :002 > l2 = Location.find_by(name: "Library")
  ...
  2.3.6 :003 > l.absorb(l2)
  ...
  Absorbed 121 commitments and 1 alias.
  Deleting Library
  => nil
  2.3.6 :003 > exit
  $ 

Note that the location which you're keeping absorbs the one which is
going away.  After this process, your database records will be as
follows:

* Location record, named BR218
* Location Alias, also named BR218, linked to the Location BR218
* Location Alias, named Library, linked to the Location BR218

So any future imports will direct all events to the one single location.
