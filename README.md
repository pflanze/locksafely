# Lock safely

Better locks via lock files for shell scripts:

- No stale locks hang around if a process ends (e.g. gets killed)
  without unlocking (could also add a feature to mark abandoned
  locks). Said in a different way: automatically unlock if process
  exists, in which ever way (including machine restarts)

- also offers shared locks (see `--help`)

- also offers semaphores (see `--help`)

- easy to use

Drawbacks:

- uses flock, hence only works for processes on the same machine (and
  better don't put lock files on networked file systems)

- depends on the flock system call always working correctly (todo: verify)

- does polling (with currently a 0.3 sec sleep time), thus unlocks
  somewhat slowly (probably fine for typical shell scripts)

## Usage

The `lockfile` program is called from a process and prints "ok" or
"already_locked"; your program has to check that answer. Currently it
doesn't have a mode to wait on the lock being released; the main usage
target is to avoid re-running things while already running in parallel
(e.g. cronjobs).

The lock is released when (up to 0.3 seconds after) the process that
got the lock exits. This is done by a daemon process polling to see if
the process is still there. There's no filehandle that the calling
process needs to keep open or prevent from being inherited
accidentally.

# Copyright

Copyright by Christian Jaeger <ch@christianjaeger.ch>.
Published under the [MIT license](https://opensource.org/license/MIT).
