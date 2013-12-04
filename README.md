commotion-deb-builder
=====================

A simple Python script to clone Commotion Wireless repos, and build them into Debian packages.

It reads through a configuration file which specifies repos and branches to build from; and then uses `git-buildpackage` to turn them into debian packages. It is presumed that your system already has all of the build prerequisites. The script will likely crash if you do not.


Usage
-----

Make all of the changes you need to the config files (described below) and run `./build-commotion`

The script references two files, `build-commotion.conf` and `repos.txt`.

### repos.txt
This file contains all of the information about a repository needed to build a Debian package. The information for each package is a space separated series of values; one package per line. The format is.

    (URL of repository)  (local work branch)  (upstream build branch)  (short package name)

Here are a few lines from the current (as of writing) version of the file:

    https://github.com/opentechinstitute/olsrd.git commotion commotion olsrd
    https://github.com/opentechinstitute/serval-dna.git commotion-wireless commotion-wireless serval-dna
    https://github.com/opentechinstitute/commotion-service-manager.git master master commotion-service-manager

### build-commotion.conf
Right now the only configuration options in the configuration file are:

    [default]
    repos_file = /home/oti/debian/repos.txt
    dst = /home/oti/debian

`repos_file` is the complete system path to the file that has all of the repos and associated information described above.

`dst` is the directory where a directory named after the time at which the script rant. In that sub-directory,  all of the source repos will be cloned, and the finished debs placed.


What is it doing?
-----------------
As an example of how the script works, lets look at a single package, in this case `olsrd`, as it appears in the example obove.

    https://github.com/opentechinstitute/olsrd.git commotion commotion olsrd

The build script first creates a clean directory to work in, this will ensure that you are getting a clean repostory clone, and build environment. Next it will clone `https://github.com/opentechinstitute/olsrd.git` into olsrd. Then it will run `git-buildpackage`, plugging in supplied values as it goes. For example the command it runs will look something like:

    git-buildpackage -us -uc --git-debian-branch=commotion --git-upstream-tree=branch   --git-upstream-branch=commotion --git-ignore-new

Were I to change the line of `repos.txt` to:

    https://github.com/opentechinstitute/olsrd.git local-feature-23 commotion olsrd 

The build line will then be.

    git-buildpackage -us -uc --git-debian-branch=local-feature-23 --git-upstream-tree=branch   --git-upstream-branch=commotion --git-ignore-new

The script repeats this process for every line in `repos.txt`.
