# SUPER SYNCD
### A Versitile Linux FS Directory syncing utility
#### written in Bash and using itnotifywait

## What is it

Syncd is a bash script that watches for file changes and rsyncs them to a remote machine or locally. It uses inotify to watch for file system changes and syncs the whole directory using rsync. The script makes sure to aggregate change events during a running rsync, such that after the initial sync a subsequent sync can be triggered (and so on).

## What's it good for?

This works great for syncing folders within a machine or over ssh.  I use to to keep a real-time mirror of my data drive on a USB drive.  I use it for development on other headless machinces like a raspberry pi.  In this way I can work in my fast dev computer with my favorite tools yet the code runs in real-time on this other headless machine.  I am sure you can come up with other uses.

## Super Improvements

I have done several improvements over the now unmaintained 2014 [forked repo](https://github.com/drunomics/syncd).
* Both local and remote syncing.
* Put your configuration files in multiple places
* Added a dyncd script which will load a whole directory of conf files for multiple directory syncs in one command.
* Added some rync excludes you can use and/or modify
* Added explanations inside sample conf file

## Requirements

A linux based system with inotify-tools and rsync installed is required, .e.g for ubuntu/debian based systems run
```
apt-get install inotify-tools rsync
```

## Installation

* Clone the script in a directory of your choice, e.g.
```
cd ~/opt
https://github.com/dkebler/syncd.git
```
*  Best, put syncd in your $PATH, for example by running:
```
cd syncd
sudo ln -s $PWD/syncd /usr/local/bin/syncd
```

## Usage

See the `jobs/syncd.conf.example` conf file for details on what goes in a conf file.
Take this `.conf` file in the jobs directory and copy/rename it (remove `.example`) and put it where it will be found (see list below)

Syncd will find this file if you put it any one of these places.
1. jobs directory or (or subdirectory there of) as set by the `JOBS_DIR` variable in the script (default is `/jobs` under the folder where you cloned the repo )
2. In a `/syncd` sub folder of your pwd  (which could be the directory being synced)
3. pwd
4. Some absolute path given in the command

If syncd can't find your conf file(s) it will let you now.

#### The Commands
* The first argument is your configuration file without the .conf extension
* The Second (Action) argument are (start,stop,restart,status) as well as "run" for manually triggering a rsync and "log" for checking the rsync output.

**alway do a `run` first to test your rsync command before using `start` which creates a background process watching for changes**

once started you can use the `status` command to assure that the process was started.  Then it's a good idea to test by adding dumby file or folder to your source and see if it magically appears in your target.   If you used the delete flag in your rsync options you could then delete the folder/file in your source and confirm it disappears from your target.

```
syncd myjob run                   # always do a one off run first to test your rsync command
syncd myjob start                 # will look in jobs directory, pwd dir, or pwd/syncd
syncd subdirofjobs/myjob start    # relative to jobs directory
syncd /home/name/jobs/myjob start # absolute path
```

By default, the script will create a pid and log file within a directory of the same within the `JOBS_DIR` specified in the script.

To change this default folder for jobs edit the `syncd` script file and change the `JOBS_DIR` to some absolute path of your choice

To run a whole set of conf files put them all in a single directory and run the `dsyncd` script.

```
dsyncd somesubdir start
```
It will look in `somesubdir` of the JOBS_DIR as set in `dsyncd` and process all the `.conf` files it finds.  Again you can set an absolute path for the JOBS_DIR variable in the `dsyncd` script or use the default but it should match the same in the `syncd` script.

------------
Forked from [syncd](https://github.com/drunomics/syncd)
