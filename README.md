# nest

`nest` is a lightweight, personal, *private* GitHub-alike but without
the browsing and merging tools, wiki, bug tracker, social aspects or
any kind of web interface at all.

So really, it's *nothing* like GitHub.

What it *will* do is let you store any (reasonable) number of git
repositories on a remote *nix system to which you have login
privileges and easily clone, pull and push to them from remote clients
using only git, Perl 5 and ssh.

## Basic Use

Installation is as simple as copying `nest` into a directory in your
path.

Setting up on the server takes one command:

    nest --main

as does setting up a client:

    nest --host my.server.com --user nestacct --path GitNest

(Note: it helps a lot if you have ssh-agent set up.)

You import a git repository like this:

    cd my-project
    nest import my-project

and clone a new copy in the current directory like this:

    nest get my-project

Finally, you can make or update a local copy of the entire collection
of repositories on the main server like this:

    nest fill

This is useful both as a backup and as a way to get at your work when
you're offline.

`nest` checkouts are just git clones and you can treat them as such.
An ordinary `git push` will push your changes back to the server.

## Documentation

You can find the manual [here](nest-manpage.md).

The source for this is in the Pod section of `nest` and was generated
with `pod2markdown` as a convenience.  If your Perl installation
includes `Pod::Usage`, you can also read it with:

    nest --manual

or with `perldoc`:

    perldoc ./nest

## License

`nest` is released under the terms of the GNU General Public License
version 2.0.  You use it at your **OWN RISK**.



