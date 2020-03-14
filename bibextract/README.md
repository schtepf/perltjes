# bibextract & bibformat

**bibextract** is a utility that extracts bibTeX entries referenced in a LaTeX file from a large `.bib` database, so they can easily be distributed together with the LaTeX source. See `bibextract -h` for usage information.

**bibformat** renders selected bibTeX entries into LaTeX, HTML, POD plain
text or Creole-style wiki code. It automatically searches all `.bib`
databases in the TeX search path and can apply any installed bibTeX style.
See `bibformat -h` for usage information.


## Installation

Both tools require a reasonably up-to-date
[LaTeX](https://tug.org/texlive/) installation.  Then adjust the path to the Perl interpreter on the first line of each script if necessary, and copy the to a suitable directory in your search path.

On most Unix systems (including Linux and MacOS), use

    install -m 755 bibextract bibformat /usr/local/bin

to install the scripts for all users (requires administrator privileges)
or

    install -m 755 bibextract bibformat ~/bin

for a private installation.


## Bash completion

Use the following code snippet to set up command-line completion for `bibformat` in the `bash` shell:

    _bibformat_complete() 
    {
        local cur opts pubs
        COMPREPLY=()
        cur="${COMP_WORDS[COMP_CWORD]}"
        opts="--output -o --style -s --database -D --html --text --pod --latex --creole --single --single-line -S --utf8 --list -l --verbose -v --quiet -q --debug -d --help -h"

        if [[ ${cur} == -* ]] ; then
            COMPREPLY=( $(compgen -W "${opts}" -- ${cur}) )
            return 0
        fi
        if [[ ${cur} == /* ]] ; then
            return 0   # don't attempt to complete regexp
        fi
        pubs="`bibformat -q -l /^${cur}.*/`"
        COMPREPLY=( $(compgen -W "${pubs}" -- ${cur}) )
        __ltrim_colon_completions "${cur}"  # because completion breaks words on colons
    }
    complete -F _bibformat_complete bibformat



## Licence

Copyright (C) by Stefan Evert
([www.stefan-evert.de](http://www.stefan-evert.de/))

This software is provided AS IS and the author makes no warranty as to
its use and performance. You may use the program, redistribute and
modify it under the same terms as Perl itself.

IN NO EVENT WILL THE AUTHOR BE LIABLE TO YOU FOR ANY CONSEQUENTIAL,
INCIDENTAL OR SPECIAL DAMAGES, INCLUDING ANY LOST PROFITS OR LOST
SAVINGS, EVEN IF HE HAS BEEN ADVISED OF THE POSSIBILITY OF SUCH DAMAGES,
OR FOR ANY CLAIM BY ANY THIRD PARTY.
