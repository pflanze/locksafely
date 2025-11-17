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

### Force release

If you need to re-run a job and the lock gets in the way, do the
following:

* To check if the lock is actually being held:

        lsof path/to/the/lockfile

    If no process shows up then it's not being held. Note that the
    processes shown by lsof are not the processes carrying out the
    work, but the daemon waiting for the actual processes to finish,
    so that it can release the lock.

* To check up on the process causing the lock to be held:

        ps auxwwf | less
        
    then `/<pid>` where `<pid>` is what lsof showed for locksafely
    processes. You'll find those processes, their second argument is
    the pid of the process that they are looking
    for. `/<looked-for-pid>` will show you that process (group). You
    can `kill` those (you might have to kill child processes
    explicitly, too).

# Copyright

Copyright by Christian Jaeger <ch@christianjaeger.ch>.
Published under the [MIT license](https://opensource.org/license/MIT).
