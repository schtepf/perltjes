# Hoover – Clean up temporary files from TeX and Emacs

When TeX/LaTeX is used to format a document, many temporary files are
created in the same directory as the source file. Unfortunately, TeX
does not offer an easy way to remove these temporary files when they are
no longer needed. The Hoover program was designed to take over this job.
It also deletes Emacs backup and cache files, and optionally various
temporary files created by MacOS, R and Python.

Type `perldoc Hoover` for more information and usage instructions.

The current version of **Hoover v1.0.7**.

## Installation

Adjust the path to the Perl interpreter (`#!/usr/bin/perl`) in the first 
line of the "Hoover" script, if necessary.  Then copy the script to a
directory in your search path.  Typical installation directories are
`/usr/local/bin` for a site-wide installation (usually requires
administrator privileges) and `~/bin` for a personal installation.

On most Unix systems (including Linux and MacOS), you can install
Hoover with one of the following commands:

    install -m 755 Hoover /usr/local/bin

or

    install -m 755 Hoover ~/bin


## Licence

This software is provided AS IS and the author makes no warranty as to
its use and performance. You may use the program, redistribute and
modify it under the same terms as Perl itself.

IN NO EVENT WILL THE AUTHOR BE LIABLE TO YOU FOR ANY CONSEQUENTIAL,
INCIDENTAL OR SPECIAL DAMAGES, INCLUDING ANY LOST PROFITS OR LOST
SAVINGS, EVEN IF HE HAS BEEN ADVISED OF THE POSSIBILITY OF SUCH DAMAGES,
OR FOR ANY CLAIM BY ANY THIRD PARTY.

Copyright (C) 2003–2018 by Stefan Evert
([www.stefan-evert.de](http://www.stefan-evert.de/])).


## Version history

  - 2023-01-07:  Version 1.0.7
    - also clean up `.vrb` files and `.ipynb_checkpoints` sudirectories
    - new option `--other` (`-o`) unconditionally deletes MS Office backup files (starting with `~`)

  - 2017-05-20:  Version 1.0.5
    - now also deletes `.spl` files generated by the Elsevier manuscript style
    - as well as `.bcf` and `.run.xml` files created by biblatex / biber

  - 2014-06-17:  Version 1.0.4
    - new option `--rdata` (`-R`) removes R history and workspace (`.Rdata`) files (use with caution!)
    - new option `--python` (`-py`, `-y`) removes `.pyc` bytecode files and `__pydata__` subdirectories

  - 2013-11-14:  Version 1.0.2
    - new option `--macosx` (`-m`) removes Mac OS X support files (`.DS_Store`, extended attributes)
    - various file extensions added (often created by external programs such as `latexmk.pl`)
    - various improvements and bug fixes

  - 2005-06-12:  Version 0.99 – first public release
