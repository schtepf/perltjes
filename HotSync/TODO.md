# HotSync: TODO

- try to provide better support for path names containing blanks & other special characters
  - by enclosing paths in single quotes '...', but one may have to be clever about this on remote machines

- automatically create source/destination base directories if they do not exist (possible on remote side only if remote = localhost)
   - either controlled by a global or configuration option
   - or automatically restricted to mirror operations where remote machine is localhost (this is the current implementation)

- add “preflight” subroutine to task configuration options
   - this can be used to make backups of individual files, by copying them to a backup directory before the synchronisation
