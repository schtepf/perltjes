# perltjes – A collection of small but useful Perl scripts


## Hoover

`Hoover` cleans up directories littered with temporary files created by LaTeX, Emacs, MacOS, R and Python. 

See the [README](Hoover/) file for more installation instructions and
`perldoc Hoover` for usage notes and a complete list of files that will
be cleaned up automatically.


## HotSync

HotSync is a file-synchronisation utility. As a front-end to `rsync`, it allows users to define complex synchronisation tasks and automatically runs the necessary `rsync` commands.

See the [README](HotSync/) file for installation and configuration
instructions, and `perldoc HotSync` for command-line usaage. You may also
want to set up command-line completion in `bash`, which is more
convenient than the interactive mode.


## bibextract & bibformat

Utilties for extracting relevant entries from a bibTeX databases
(`bibextract`) and rendering them in LaTeX or various text formats
(`bibformat`). The latter has been used to generate the POD-based
bibliography on [my
homepage](http://www.stefan-evert.de/Publications.html).

See the [README](bibextract/) file for further information. 


## aaf

The ASCII auto formatter ([aaf](aaf/)) pretty-prints TAB-separated
tables, i.e. it automatically computes column widths and justifies values
(left, right or centre) by inserting blanks.

After installing in your search path, type `aaf -h` for usage
instructions. In a modern environment, you will always want to supply the
option `-c utf8`.


## teximage

Render snippets of latex code as EPS, PDF or PNG files. Requires a recent
[LaTeX](https://tug.org/texlive/) installation and the `pstoimg` tool
from the [LaTeX2HTML](https://www.latex2html.org/) distribution.

After installing in your search path, type `teximage -h` for usage
instructions.


## inplace

In-place edit Perl files to update configuration variables from the
command line. Type `inplace -h` for usage notes.


## Licence

All software in this repository is Copyright (C) by Stephanie Evert ([www.stephanie-evert.de](https://www.stephanie-evert.de/)).
Unless specified otherwise, the following licensing conditions apply:

> This software is provided AS IS and the author makes no warranty as to
> its use and performance. You may use the software, redistribute and
> modify it under the same terms as Perl itself.
