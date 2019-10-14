
Users
=====

Authentication and Authorisation
--------------------------------

Scheduler makes a distinction between authentication (identifying
who any given person is) and authorisation (granting permission to
do certain things).

Authentication is handled by a plug in module called OmniAuth and
makes use of external authentication services.  It is thus not
generally necessary to create user accounts manually within Scheduler.
By linking OmniAuth to an existing authentication mechanism used
by the school (typically Google) users are set up automatically
and do not need to be given a new set of credentials.

Once a user has authenticated, Scheduler then applies various rules
to set up initial permissions.  The system administrator can tweak
the initial settings, and adjust individual users' permissions as desired.

User profiles
-------------

The system by default has three pre-configured user profiles.  These
are:

- Staff
- Pupil
- Guest

At first installation, the Staff user profile gets quite a few permissions,
the Pupil user profile gets very limited ones, and the Guest profile
gets none at all.  The system administrator can alter the permissions
for each of these profiles, and create more profiles if desired.

Each profile carries a flag called "known".  By default, staff and
pupil profiles have the known flag set to true, whilst the guest profile
has it set to false.

Any user who is not "known" has exactly the same access to the system
as someone who is not logged in at all.  It is thus acceptable for
any Google user to authenticate into the system, but they will gain
no authorisation by doing it.  Only known users get into the authorisation
process.

If it is desired to lock a given set of users out of the system - e.g.
ex staff - then an additional user profile can be created with the
"known" flag set to false.  By setting a user to have this profile
the user effectively becomes a guest.  If at a later date the user
is to be re-admitted then the user's profile can simply be changed back.


First login
-----------

When a new user first authenticates in Scheduler, a User record is
created and assigned to the "Guest" user profile.  The system then
does some checks to see whether any more than very basic permissions
can be granted.

The e-mail address of the new user is compared against the e-mail
addresses of all known staff.  If it matches one of them then the
user record is linked to that staff record, and the user's profile
is changed to the staff profile.

If that fails, then the same process is carried out against all
known pupils.  Again, if a match is found the user record is linked
to the pupil record and the user's profile is changed to the pupil
profile.

If both these checks fail, then the new user is left as a guest
user but the same checks will be performed again each time the
user logs in in the future.  This is to cope with the case where
a new member of staff is added to the authentication mechanism before
being added to the list of staff.  At first login, the user will
be a guest, but once the necessary staff record is created then
the next login will cause permissions to be allocated.


Manual setup
------------

It is possible that the system administrator will want to grant
permissions to someone who is neither a member of staff nor a pupil.
E.g. a representative of the PTA.

The extra user will still need to authenticate, and so will need
an account on the authentication system.  If you are using Google,
then a Google e-mail address will suffice.

The user can then be granted permissions by creating a suitable user
profile, setting it to be a "known" one, setting the permissions
as desired and then editing the user record to link it to that new
user profile.

At that point, the user becomes a "known" one and whatever permissions
have been granted will be effective.

Once the user has got a user profile other than "Guest user" any subsequent
changes are under the control of the system administrator.  The automatic
process described in the previous section is applied only to guest
users.

Permission flags
----------------

Each user profile has a set of permission flags, each of which can be
on or off.

Each user record then has the same set of flags, but with the added
possibility that a flag can have no value at all.

When a user record is first created then all its permission flags are
unset, meaning that all the user's permissions are defined by the user's
profile.

The usual way of changing user permissions is by editing the appropriate
user profile and turning the flags on or off.  This then affects all users
with that profile.

Finer grained control can be achieved by editing the user's individual
record, where the three choices are "on", "off" and "unset".

The available flags are:

:editor:           Can create events within the system.

:repeating events: Can create repeating events.
                   
:add resources:    Can add resources dynamically to an event.  Without
                   this permission bit, any events which the user creates
                   will get only the resources configured by the system
                   administrator - typically the corresponding pupil.

:add notes:        Can attach notes to events within the system.
                   It is not necessary to be the owner of an event in
                   order to attach notes to it.

:edit all:         A user with this privilege bit can edit any event
                   within the system.  Typically granted only to a
                   system adminstrator.

:subedit all:      This is a lesser permission - the user can change
                   aspects of any event within the system, adding and
                   removing resources, but can't change the fundamentals
                   of the event.

:privileged:       The user can make use of the privileged event
                   categories.  These are things like "Date - crucial"
                   which if selected will cause an event to show through
                   on everyone's schedule all the time.

:groups:           The user can create groups - collections of other
                   things within the system.  E.g. a list of members
                   of a football team.

:public:           In conjunction with the previous flag, this one grants
                   the ability to make the groups public - that is, visible
                   to all users of the system.

:forms:            Can create and edit forms within the system.  Forms
                   can be attached to resources to gather in more
                   information about what is needed.  For instance,
                   if someone requests catering for their event, a form
                   can be used to collect more information about exactly
                   what is needed.

:find free:        The user can access Scheduler's "Find free" facility,
                   which allows the retrieval of information about who
                   or what is free at a given time.  E.g. a free
                   meeting room, or a free prefect.

:adjust view:      This flag allows the user to select what schedules
                   he or she wants to look at.  Without this permission
                   bit a user can view only the schedules pre-configured
                   by the system administrator.  Typically a pupil would
                   not have this bit set, and thus would be able to look
                   at his or her own timetable, plus public calendars.
                   A staff member would have it set, and could thus look
                   at the timetable of any pupil or staff member.

:can roam:         If set, allow the user to browse through linked
                   records within the system.  When looking at a group
                   for instance, the user can click on any pupil within
                   the group and get more information about that pupil,
                   including all the other groups of which he or she
                   is a member.

:can su:           Short for "Can set user".  A very powerful permission
                   bit.  If this is set then the user can choose to become
                   effectively any other user in the system.  Generally
                   set only for system administrators.

:exams:            Can access the functionality specifically related
                   to setting up invigilation slots for exams.

:relocate lessons: Any staff member can use Scheduler's relocation
                   facilities to move one of his or her own lessons to
                   a different free room - e.g. to an ICT room.

                   A user with this bit set can do the same for anyone's
                   lessons, and can choose to move a lesson to a room
                   which is not apparently free - e.g. because two
                   lessons are being merged due to most of the pupils
                   being absent.

:view forms:       When an event has a form attached to it, e.g. specifying
                   catering requirements, the owner of the event and
                   the owner of the catering resource can view the contents
                   of the form.  In addition, any user with this flag
                   set can view all such forms.

:admin:            If set then the user gains full administrative
                   privileges over the system.

:view unconfirmed: Some resources within the system require approval
                   to be attached to events.  For instance, most users
                   cannot simply put an event into the school's public
                   calendar.  When a user tries to put an event in the
                   calendar, the connection between the two starts
                   in an "unconfirmed" state, and until the calendar
                   administrator confirms it, the event will not
                   appear in the calendar to most people.  Only the
                   requester and the administrator can see it, and then
                   greyed out.

                   Users with this permission bit set will see it to,
                   but still greyed out.

:edit members:     When a user is editing a group, the normal interface
                   simply allows elements to be added to or removed
                   from the group - effective as at the date of
                   editing.

                   Scheduler's groups are however a lot more sophisticated
                   than that - they store the entire chronology of
                   a group's membership.  Scheduler can keep track of
                   who was a member of a group on any given date.

                   Users with this privilege bit can edit the underlying
                   membership records and set start and end dates
                   directly.  It is thus possible to set a specific
                   duration for a membership.

:can use API:      Users with this bit set can make use of Scheduler's
                   Application Programming Interface, or API.

:can upload files: The user can upload files for storage within Scheduler.
                   Such files can be attached to events for download by
                   others.

:journals:         Scheduler keeps a journal of every manual edit to
                   each event.  Users with this bit set can view these
                   journals.

