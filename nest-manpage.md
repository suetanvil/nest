# NAME

nest - Manage a collection of Git repositories.

# SYNOPSIS

nest \[command\] \[options\]

Commands and options are listed below.  All options are legal with all
commands but do not necessarily have an effect.  The list of commands
below also lists the arguments that affect them.

See **OVERVIEW** below for a more detailed explanation of the use of
`nest`.

`ssh` and `git` must be installed for `nest` to work.

# OPTIONS AND ARGUMENTS

- **setup --host _hostname_ --path _path_ \[--user _user_\]**
- **setup --main**

    Create a local GitNest directory in the current home directory.  If
    `--main` is given, it is a new main nest.  Otherwise, `--host`,
    `--user` and `--path` give the hostname, ssh username and file path
    of the main nest.

    _hostname_ and _path_ are the parts before and after the slash ("/")
    in the ssh URL of the nest and _user_ is the part before the at-sign
    ("@").  If `--user` is not given, the account username is given.
    (More precisely, there is no username in the resulting ssh URL).

- **setupcmd**

    Prints the command that recreates the current settings.  Useful for
    setting up nest on a new account while logged into an account with a
    working nest configuration.  This is not overly helpful on the main
    account.

- **checkout \[--local\] _module_ **

    Create a local clone of _module_ (which must be a module in the
    nest).  The clone's directory is also named _module_.  If such a
    directory already exists, `nest` will refuse to fetch a copy.

    If _--local_ is given, the clone is taken from the backup in the
    local nest instead of from the main nest.  However, the clone's origin
    is still the main nest and subsequent pulls and pushes will go there.
    This allows you to checkout a module when there is temporarily no
    network connection.

- **get \[--local\]**

    `get` is an alias for `checkout`.

- **fill** \[yes\]

    Creates or updates the local nest's backup of the main nest.  If the
    local nest is empty, `nest fill` requires the word 'yes' as an
    argument to confirm that you want to create a local backup.

- **import _module-name_ **

    Imports the current project into the main nest.

    The current directory must be the top of a git repository.  This
    repository is copied to the main nest and the current module's origin
    is set to point to that module.

- **ls \[1\]**

    Lists all of the modules in the main nest.

    If argument '1' is given, outputs in a machine-friendly form: one item
    per line; each output line containing only the name of the
    module. (mnemonic: like the 'ls' option).

    If '1' is omitted, the format is more human-friendly.

- **listLocal _nest-path_    (INTERNAL!)**

    Prints out a list of modules in the nest at _nest-path_.  This is
    used by the `nest` program to query remote nests.  Module names begin
    with a space and errors with a '+'.  If the operation succeeded, the last line contains only the string "OK" followed by a newline.

- **makeEmpty _name_   (INTERNAL!)**

    Creates a new, empty repository in the local nest named by _name_.
    The local nest **must** be the main one.

- **ping _nest-path_    (INTERNAL!)**

    Used by remote users to test for the presence of a local main nest.
    If _nest-path_ points to the local nest and if that nest is a main
    nest, prints "OK".  Otherwise, it prints "+no".  In each case, the
    output is followed by a newline.

- **--dbg-verbose**

    The `--dbg-verbose` option causes `nest` to be extremely verbose,
    including printing out the git commands it runs.  It is intended as a
    debugging aid.

- **--help, --manual**

    These options cause `nest` to print out online help.  `--help` gives
    a brief summary of options while `--manual` prints out the entire man
    page.

# OVERVIEW

`nest` is a tool designed to make it easy to keep all of your
unrelated projects stored in one place, regularly backed up and
properly version-controlled.

## Rationale

If you're like me, you will have accumulated lots of little projects
over the years, things that aren't related except that they're yours,
and you want to hang onto them in case you need them again.  And if,
like me, you've accidentally deleted a source file, you'll religiously
keep all of your work in version control.

If you use `cvs` or `svn`, you probably have a single repository
somewhere with each individual project set up as a submodule.  Then,
when you need to work on one, you just check it out into a working
directory, make your changes and commit them.

This solves a lot of problems:

1. You know where all of your work is kept.
2. You can back it all up by backing up a single directory tree.
3. You can use multiple computers and keep all of your work synched.

If you've switched to Git, however, this becomes difficult.  Git has a
one-repo-per-project policy that makes it difficult to manage multiple
unrelated projects in a single repository.  `nest` solves this
problem by instead managing a collection ("nest") of unrelated
repositories for you.

Projects can easily be added to a nest and then cloned, pulled from or
pushed to as needed.  In addition, nests are easily copied to other
computers, both as a backup mechanism and as a way to develop offline.

# USING NEST

## Introduction and Terminology

`nest` (the program) keeps all of your projects inside a single
directory which we call the nest.  (To avoid (some) confusion, I will
always quote `nest` when it refers to the program and never when it
does not.)

All of your work is stored in the **main nest**. This may be located on
any computer to which you have network access.  If you only use one
computer, you can create a main nest and just start using it.  (But
see **More on Nests** below.)

If you use multiple computers however, you will need to create a
**backup nest** on each of them.  The backup nest does two things:

1. It holds the config file which points `nest` at your main nest.
2. It can optionally contain a backup copy of your main nest.  This copy
can then be used when working offline.

Once you have all the nests set up, you simply check out a copy of a
module from the nest with **nest checkout** into a work directory.
This checkout is a complete `git` repository--you can commit, revert,
branch, merge or do any other of the many things `git` is good for.

The checkout has the nest module as its origin so when you are ready
to save your work in the nest, just push the changes to the origin.

## More on Nests

Your nest (main or backup) is always located in your home directory
and is always named **GitNest**.  The rule is that there can only be
one nest per user on a computer.

However, even if you are only using one computer, it may be worthwhile
to put the main nest on a different account from the one you normally
use.  This will make it much harder to accidentally delete or corrupt
your main nest.

## Using nest

`nest` uses ssh for all remote operations, so before you do anything
else, you will almost certainly want to set up ssh-agent.  Note that
`nest` always logs in to the main nest's account machine and never
the reverse.

Also, make sure `nest` and a sufficiently advanced version of `perl`
are both installed on all of the machines you are going to use.
`nest` **must** be in the `PATH` of the account containing the main
nest.

The next step is to set up your main nest.  To do this, login to the
account that will contain it and type:

    nest setup --main

Then, we need to add the backup nests.  On each computer that you are
going to use for development, type:

    nest setup --host compy386 --user strongbad \
               --path /home/strongbad/GitNest

(With the option values changed to yours, of course.)

`nest setup` queries the main nest when you do this so you will need
to be able to access the main nest.

**Optional:** If you want to have a backup copy of your main nest here,
you can do that now with:

    nest fill yes

From then on, you update your backup cache with

    nest fill

(The "yes" is to tell `nest` that yes, you really want to create a
backup.)

To import a project (aka "module") into the main nest, you first need
to put it under Git version control.  Once that's done, just go to the
project's top directory and use `nest import`:

    cd myproj
    nest import myproj

This will copy myproj to the **main nest** so it will need to be
accessible.  Doing this will also **alter the repository** by pointing
the origin URL to the main nest.  If this makes you nervous, you can
always make a scratch clone of `myproj` first and import that
instead.

To create a working copy of `myprog` (on a different computer, say),
just do this:

    nest checkout myproj

If your backup nest is an actual backup, you can fetch the code from
there instead:

    nest checkout --local myproj

This is handy if you are temporarily offline or have a very slow
network connection.  In the latter case though, you'll probably want
to follow the checkout with a `git pull origin`.

The checked-out copy of `myproj` is a full, working git repository
and you can treat it as such.  It is configured as a clone of the
corresponding entry in the main nest, so to save your changes to the
nest, you just push them to the origin:

    cd myproj
    edit file1.c
    git commit -a -m "Made unspecified changes."
    git push --all origin

(You can, of course, be selective in what you push to the main nest.
I generally just keep everyting, however, and sort it out when I'm
ready to publish.  But that's a matter of personal preference.)

# NEST LAYOUT

Each nest contains the following files:

- 1.  `nest.cfg`

    This is the nest configuration file.  It is a git config file and can
    be accessed using `git-config` with `--file`.  It contains the
    location of the main nest or a flag to indicate that this nest is the
    main nest.

- 2. `git-log.txt`

    This is a logfile of all git commands executed on nest repositories.
    It is mostly there for troubleshooting and should be deleted if it
    gets too big.

In addition, a nest may contain one or more bare repositories.  These
appear as directories whose names end with `.git`.

# ENVIRONMENT

`nest` uses the `HOME` environment variable to find the local
nest. It also needs to be in the local `PATH` along with `git` and
`ssh`.

# AUTHOR

`nest` is written and maintained by Chris Reuter,
[https://github.com/suetanvil](https://github.com/suetanvil).

# COPYRIGHT

`nest` is Copyright 2009,2015,2019 Chris Reuter.

It is released under the terms of the GNU General Public License
version 2.0.  You use it at your **OWN RISK**.
