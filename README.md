notmuch-rsync
=============

Sync a local maildir using notmuch and rsync.

Caveat: this is a solution for a very particular use case (mine), and
published here to share and get feedback, it does not claim to be
generally useful.

It syncs all emails that don't exist on either the local client or the
remote server, in both directions. So it works append-only, which
should correspond with the notmuch approach.

It uses a notmuch search on both the client and the server to find the
message-ids of mails within a recent timespan. It compares both lists
of message-ids to determine what needs to be synced, uses notmuch to
get the file names of the mails to sync, and passes those on to rsync
to transfer the files.

Requires a relatively recent ruby, propably >= 1.9 will do.

Usage
-----

    notmuch-rsync <local-directory> <remote-host> <remote-directory>

Example output

    $ notmuch-rsync /home/foo/Maildir/cur example.com /home/foo/Maildir/cur

    Running: notmuch new
    Running: notmuch search --exclude=false --sort=newest-first --output=messages 1394921697..1397600097
    Number of local mails: 2182
    Running: ssh example.com -C notmuch new
    Running: ssh example.com -C notmuch search --exclude=false --sort=newest-first --output=messages 1394921697..1397600097
    Number of remote mails: 2183
    Only local: 0
    Only remote: 1
    Running: ssh example.com -C 'notmuch' 'search' '--exclude=false' '--output=files' 'id:xxx1234@xxx.com'
    Running: rsync -v -z example.com:/home/foo/Maildir/new/redacted12341234@example.com,S=22826 /home/foo/Maildir/cur
    Running: notmuch new
