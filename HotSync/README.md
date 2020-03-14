# HotSync – An `rsync` front-end for synchronising files with a remote host

Type `perldoc HotSync` for usage and installation instructions.

For convenience, instructions are also provided on this page, but may not be fully up-to-date. 
Please refer to the built-in documentation for authoritative information.

Current version: **HotSync v0.8.2**


## Installation

### Prerequisites

HotSync was designed for Perl 5.6.1, but it should be compatible with
earlier versions of Perl5 as well (you may have to install some
additional libraries, though).

HotSync is a user-friendly front-end to the `rsync` program, which must
be installed both on the local _and_ the remote host. Rsync itself uses
an SSH connection for data transfer, so a suitable `ssh` implementation
must be installed as well (more specifically, an SSH client must be
available on the local host, and the remote host must allow SSH login).
If you want to use the interactive HotSync console, you also have to
install the `Term::ReadLine::Gnu` Perl package.

### Installation procedure

In order to install the HotSync script itself, adjust the path to the
perl interpreter on the first line if necessary. Then copy the script
(with `cp -p`) to a suitable directory in your search path (usually
`/usr/local/bin` for a global installation, or `~/bin` for a personal
installation).

It is recommended that you enable RSA authentication for SSH connections
(otherwise you may have to enter your password repeatedly during
synchronisation). First, run 

    ssh-keygen -t rsa

_on the local host_, which will create a public key in
`~/.ssh/id_rsa.pub`. Then copy this file to `~/.ssh/authorized_keys` *on
the remote host*. If that file already exists, append your public key to
it. Note that the `authorized_keys` file must be world-readable (mode
644).

To test the RSA authentication, type `ssh remote_host`, which should now
establish an ssh session without asking for your password. (If you have
an old version of ssh that accepts only protocol version 1, you may have
to omit the `-t rsa` flag. In this case, `ssh-keygen` will create the
public key in `~/.ssh/identity.pub`.)

Finally, you have to create a personal HotSync configuration file named
`~/.HotSync`. This file defines synchronisation and backup tasks that
HotSync will perform. See the following section for a detailed
description of the file format. Note that `~/.HotSync` must contain valid
Perl code. To enable proper syntax highlighting in emacs, the first line
of the file should read

    # -*-cperl-*-

### Important notice

Since the rsync program uses time stamps to synchronise data (i.e., if
a file exists in different versions on the local and remote host, the
older version will be overwritten with the newer one), it is important
to keep the local time on both computers synchronised. It is recommended
to update the computers' clocks regularly from a time server, e.g. with
the `ntpdate` utility (which must be executed by the root user).


## Configuration file

The HotSync configuration file is a small Perl program that registers
synchronisation, archive, and multi tasks by calling the predefined
`Sync()`, `Archive()`, and `Multi()` functions. Two additional functions,
`Global()` and `Config()`, are used to set configuration variables. The
configuration file must be stored in the user's home directory under the
name `~/.HotSync`. To enable proper syntax highlighting in emacs, the
first line of the file should read

    # -*-cperl-*-

All predefined functions take lists of named parameters, some of which
are optional. The prototypes below show all parameters accepted by each
function, while real usage examples will almost always omit some of them.
Note that the global options (i.e. the arguments accepted by `Global()`)
can also be passed to the `Sync()`, `Archive()`, and `Multi()` functions
in order to override the global settings temporarily.

    Config( "tar" => "tar",
            "rsync" => "rsync" );

HotSync relies on the `rsync` program for synchronizing data between the
local and remote host, and on GNU `tar` for wrapping and unpacking backup
archives. If these tools are not installed under the standard names or in
the standard paths, you have to set the corresponding config variables. A
fairly common case is that GNU tar is installed under the name `gtar`
because the operating system ships with a different version.

    Global( "host"     => "<name of remote host>",
            "user"     => "<username for login on remote host>",
            "backup"   => 1,
            "sync−all" => 0,
            "permissions" => 1,
            "extended" => 0 );

Use the `Global()` function for global parameters that apply both to
synchronization and to archive tasks. These settings will be used for all
following task definitions until they are changed with another `Global()`
function call. The mandatory host and user parameter specify the name of
the remote host and the account to which HotSync will connect. In order
to synchronise files with a local volume (for the same user account), set
host to an empty string. This should speed up file transfer, but will no
longer display progress messages (with `-v` option). If backup is true,
previous versions of updated files will be saved in files ending in `~`
(recommended for safety reasons, but may consume large amount of disk
space). By default, various types of temporary and generated files are
excluded from synchronization and backups. Set the `sync-all` parameter
to true if you want to synchronize/backup these file types as well. By
default, HotSync attempts to preserve file access permissions when
copying files. Set `permission` to false in order to disable this. The
`extended` option can only be used when copying files between two
machines running Mac OS X and Apple's patched `rsync` binary. If set to
true, extended attributes (such as resource forks and metadata) will be
preserved in the copy. This setting can be overriden with the `-E`
command-line option.

    Sync( "<name of task>",
          "description" => "<short description of task>",
          "local-root"  => "<root of directory tree on local host>",
          "remote-root" => "<root of directory tree on remote host>",
          "subdirs"     => ["<dir1>", "<dir2>", ... ],
          "exclude"     => ["<pattern1>", "<pattern2>", ... ].
          "mirror"      => 0,
          "download−mirror" => 0,
        );

The `Sync()` function defines a sychronization task, which synchronizes a
directory tree between the local and remote host. Files that are missing
from one of the trees are copied from the other tree. If a file exists in
both trees but in different version, the newer version will be used to
replace the older one. Note that the local tree can be copied to the
remote host (possibly deleting files there) when the synchronization task
is invoked with the `-push` flag, and vice versa with the `-pop` flag.
When the mirror parameter is set to 1 (or any other true value), the
synchronization is automatically carried out in `-push` mode, *without
asking the user for confirmation*. This behaviour is intended for
mirroring local directory trees to a remote server or external drive,
mostly for backup purposes (especially when the trees are very large so
that wrapping them in a Tar archive would be too wasteful). Note that
when mirroring to localhost, the remote root directory will be
automatically created if it does not exist. If the download‐mirror
parameter is set, the synchronization is carried out in `−pop` mode; this
option is useful for creating local mirrors of remote servers (e.g. a
project Web site at SourceForge).

The only required parameters are a short description of the task
(`description`), the root directory of the tree on the local host
(`local-root`) and the root directory of the tree on the remote host
(`remote-root`). If the subdirs parameter is specified, it must be an
array reference containing a list of subdirectories (relative to the
local and remote root of the directory tree) to be synchronized. All
other files and directories in the tree will be ignored. You can also
specify directory pairs `[<local-dir>, <remote-dir>]` in this list if the
names of subdirectories differ on the local and remote machine.
Alternatively, you can explicitly exclude files and directories from the
synchronization with a list of patterns in the exclude parameter (see the
rsync manual for details on the pattern syntax).

Note that the name of the task has to be specified as the first argument
of the `Sync()` function rather than as a named parameter. All parameters
of the `Global()` function can be specified as well, which will override
their global values for this task.

    Archive( "<name of task>",
             "description" => "<short description of task>",
             "local-root"  => "<root of directory tree on local host>",
             "remote-root" => "<backup directory on remote host>",
             "subdirs"     => ["<dir1>", "<dir2>", ... ],
             "exclude"     => ["<pattern1>", "<pattern2>", ... ] );

The `Archive()` function defines a backup archive task, wrapping up a
directory tree on the local host in a compressed tar archive, which is
then backed up on the remote host. The parameters of `Archive()`
correspond to those of `Sync()`, with the following exceptions: (i)
`remote-root` does not refer to the root of a directory tree but to a
directory on the remote host where the compressed archive will be backed
up; (ii) `subdirs` does not accept directory pairs; (iii) the syntax of
file and directory patterns in `exclude` is slightly different from the
syntax used by synchronization tasks (see the GNU `tar` manual for
details).

    Multi( "<name of task>",
           "description" => "<short description of task>",
           "subtasks"    => ["<name1>", "<name2>", ... ] );

The `Multi()` function defines a multi taks, which collects a number of
related tasks under a single name so they can be invoked conveniently.
The only relevant parameter is `subtasks`, which specifies the names of
the individual tasks to be invoked. Note that all subtasks must be of
the same type (either synchronization or backup archive), which is
checked when parsing the configuration file. It is also not allowed to
nest multi tasks recursively (i.e. to specify one or more multi tasks in
the `subtasks` parameter).

Individual tasks can be deleted with

    Delete( "<task 1>", "<task 2>", ... );

e.g. as an easy way to disable them temporarily, or when multiple tasks
are defined automatically by a subroutine (e.g. backups on different
remote hosts) and some of these should be excluded (e.g. because of
insufficient disk space on the backup drive).

See [`sample.HotSync`](sample.Hotsync) included in the HotSync
distribution for an annotated example of a configuration file. For a
quick start, edit this file according to your requirements, then copy it
to `~/.HotSync`.


## Bash completion

Use the following code snippet to set up command-line completion for HotSync tasks and arguments in the `bash` shell:

    _HotSync_complete() 
    {
        local cur prev opts tasks
        COMPREPLY=()
        cur="${COMP_WORDS[COMP_CWORD]}"
        prev="${COMP_WORDS[COMP_CWORD-1]}"
        opts="-h --help -p --perldoc --doc -V --version -i --interactive -l --list -n --dry-run -v --verbose -a --all -ul --upload-limit= -dl --download-limit= -f --config-file= -np --no-permissions -E --extended-attributes -d --debug"

        if [[ ${cur} == -* ]] ; then
            COMPREPLY=( $(compgen -W "${opts}" -- ${cur}) )
            return 0
        fi

        tasks="`HotSync -l | awk '$1 ~ /^(SYNC|ARCH|MULTI)$/ {print $2}'`"
        COMPREPLY=( $(compgen -W "${tasks}" -- ${cur}) )
    }
    complete -F _HotSync_complete HotSync



## Licence

This software is provided AS IS and the author makes no warranty as to
its use and performance. You may use the program, redistribute and
modify it under the same terms as Perl itself.

IN NO EVENT WILL THE AUTHOR BE LIABLE TO YOU FOR ANY CONSEQUENTIAL,
INCIDENTAL OR SPECIAL DAMAGES, INCLUDING ANY LOST PROFITS OR LOST
SAVINGS, EVEN IF HE HAS BEEN ADVISED OF THE POSSIBILITY OF SUCH DAMAGES,
OR FOR ANY CLAIM BY ANY THIRD PARTY.

Copyright (C) 2004-2012 by Stefan Evert
[http://purl.org/stefan.evert]


## Version history

  - Version 0.8.2
  
    - new option `permissions` can be used to avoid preserving access
      permissions when synchronising files (which e.g. Web hosting
      services may not allow)
    - instructions for setting up `bash` command-line completion

  - 2008-10-21  Version 0.8.1

    - quote command line arguments so that file paths containing blanks
      work (but single quotes are disallowed in all filenames)

  - 2008-10-07  Version 0.8 – public release

    - new option `download-mirror` automatically synchronizes in `--pop`
      mode (useful for creating local backup mirrors of remote servers,
      e.g. a SourceForge project Web site)

  - 2008-07-12  Version 0.7.9

    - new option (`extended`) and command-line flag (`-E`) to preserve
      extended file attributes on OS X
    - release candidate for public version 0.8

  - 2007-11-13  Version 0.7.7 – public relase

    - mirror tasks overwrite remote copy without confirmation
    - new configuration option `Delete()` to disable individual tasks
    - various minor extensions

  - 2005-06-06  Version 0.7.2 – public release

    - fixes the major bugs introduced in version 0.7

  - 2005-06-05  Version 0.7 – first public release

    - substantial code refactoring to improve modularity and
      maintainability
    - new interactive HotSync console with GNU readline support
