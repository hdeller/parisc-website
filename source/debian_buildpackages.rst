====================
Debian Buildpackages
====================

Debian Buildservers
-------------------

Since December 2013 we have debian unstable buildservers.

- Current build status is here: https://buildd.debian.org/status/architecture.php?a=hppa&suite=sid

Contacts for the debian HPPA buildds

- Helge Deller <deller@gmx.de>, PA-RISC Kernel maintainer, buildd maintainer
- John David Anglin <dave.anglin@bell.net>, HPPA GCC, and binutils maintainer

Jessica Clarke provides easy-to-use scripts to set up a buildd server:

- https://wiki.debian.org/PortsDocs/BuilddSetup
- https://salsa.debian.org/debian-ports-team/dsa-puppet
- old repo: https://anonscm.debian.org/git/users/jrtc27/dsa-puppet.git/

How to use debian schroot
-------------------------

- https://dsa.debian.org/doc/schroot/
- List of debian porterboxes: https://db.debian.org/machines.cgi?sortby=purpose&sortorder=dsc

How buildds get the lists
-------------------------

::

    ssh -l wb-buildd -S buildd.debian.org.ssh -o BatchMode=yes buildd.debian.org -MN

How to upload a new debian package for debian developers
--------------------------------------------------------

- Set up sbuild: https://wiki.debian.org/sbuild
- Build the package on amd64 with sbuild and argument "--source-only-changes", or use "dpkg-buildpackage --build=source"
- Upload the source.changes file.

How to create backport
----------------------

- Wait until package is in testing (unstable isn't sufficient)
- run ``dch --bpo`` on this package to get backports changelog
- Build package with ``sbuild --debbuildopts="-vVERSION"``. VERSION is
  current version of package in stable.

- Then upload to ftp-master independen of distribution, and
  stretch-backports-sloppy see:
  https://backports.debian.org/news/buster-backports/

How to upload a debian package to debian unreleased
---------------------------------------------------

- Build with ``dpkg-buildpackage -F -sa``, or ``sbuild --source
  --force-orig-source``.

- Check that the changes file: "**Distribution: unreleased**" & includes
  the sources, that is the .dsc and the .debian.tar.xz and also includes
  the orig tarball

- Sign it with **your private GPG key** (key should be known from
  mini-dak) and upload it to the debian-ports archive.

- Is using the magic +string or ~string necessary?

  - You do not necessarily need to use it as there is no hppa specific
    change there, but you need to use a version that is higher than the
    one currently in the archive.

Some other info
---------------

- `Setting up buildd server with btrfs <https://lists.debian.org/debian-68k/2013/10/msg00006.html>`__

- `DebianWannaBuildInfrastructureOnOneServer <https://wiki.debian.org/DebianWannaBuildInfrastructureOnOneServer>`__

- `Another description on how to set up a buildd <https://www.wzdftpd.net/redmine/projects/general/wiki/DebianBuildd>`__

- `How to use subkeys for signing on buildds <http://blog.cihar.com/archives/2010/09/29/using-pgp-subkeys-signing/>`__

Missing GPG key
---------------

when getting this error::

    The following signatures couldn't be verified because 
    the public key is not available: 

      NO_PUBKEY B523E5F3FC4E5F2C
      NO_PUBKEY 8D69674688B6CB36

run::

    sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys

Build chroot requirements
-------------------------

- openfoam requires up to 40GB disc space
- I'm running the buildd chroot in a ramdisc. In /etc/fstab I have::

    tmpfs /srv/buildd/unpack tmpfs size=40G 0 2

Tips to manually build debian Linux kernel
------------------------------------------

- by adding "debug-info: false" to the [build] section in
  debian/config/hppa/defines avoids building the debug info

- Autosign packages via cron::

    @hourly buildd find upload -name *changes -type f -cmin -60 -cmin +20 -exec debsign -k 43374411AA94178C84FE52A8489F10615531D26E --re-sign '{}' \\;

- ::

    nohup nice -n 10 sbuild --apt-update --no-apt-upgrade --no-apt-distupgrade \
      --batch --stats-dir=/home/buildd/stats --dist=sid --sbuild-mode=buildd \
      --keyid= --mail-log-to=logs@buildd.debian.org --build-dep-resolver=apt \
      --arch=hppa --chroot=sid-hppa-sbuild --make-binNMU="Rebuild against ..." \
      --binNMU=2 > ../build.log 2>&1 &

- If it's a binNMU, add ``--make-binNMU`` and ``--binNMU`` options to command

Prevent regeneration of mandb
-----------------------------

- ``/var/lib/man-db/auto-update`` (touch it or remove it)

Prevent cronjob errors on buildds from apt-cacher-ng
----------------------------------------------------

- The cron job of apt-cacher-ng randomly reports errors:

   - Fix is to increase the value of NetworkTimeout in /etc/apt-cacher-ng/acng.conf

How to manually build debian packages
-------------------------------------

Prevent mandb run
~~~~~~~~~~~~~~~~~

::

   echo 'man-db man-db/auto-update boolean false' | sudo debconf-set-selections

How to get a specific version of a source package
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

   apt-get source packagename=version

How to build a package (without modifying source)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

::

   dpkg-buildpackage --force-sign -B -sa -m

e.g.::

   dpkg-buildpackage --force-sign -sa -m'Helge Deller <deller@gmx.de>' -kB51E3864 -j5
   DEB_BUILD_OPTIONS=nocheck

or in a chroot environment::

   DEB_BUILD_OPTIONS=nocheck sbuild -s -m'Helge Deller <deller@gmx.de>' -kB51E3864 \
       --dist=unstable --no-arch-all --no-source -j5

building kernel, ignore kernel module dups (from kernel-wedge)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
::

   touch debian/installer/ignore-dups or set env variable: KW_CHECK_NONFATAL=1

How to build a package (*with* modified source)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
::

   dch --distribution unstable --bin-nmu Rebuild b1
   dpkg-buildpackage --force-sign -B -sa -m

How to modify debian changelog
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
::

   dch --distribution unstable --bin-nmu Rebuild b1
   dch-git --since

How to sign packages (with debsign)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
::

   debsign -k packagename.changes

How to sign packages (without debsign)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- http://purplefloyd.wordpress.com/2009/02/05/signing-deb-packages/

Create Debian chroot
~~~~~~~~~~~~~~~~~~~~

- https://www.wzdftpd.net/redmine/projects/general/wiki/DebianBuildd
- ::

    apt-get install debian-ports-archive-keyring
    /usr/sbin/sbuild-createchroot --keyring=/usr/share/keyrings/debian-ports-archive-keyring.gpg \
        --include=debian-ports-archive-keyring,eatmydata unstable \
        build/chroot-unstable http://ftp.ports.debian.org/debian-ports/
- https://wiki.debian.org/sbuild
- https://www.ca-zephyr.org/mediawiki/index.php/Some_Notes

On the physical server (outside of chroot) for building experimental::

   adduser --force-badname --system -home /var/empty --no-create-home --quiet _apt
   chmod 666 /dev/ptmx # if permissions aren't that yet

In each chroot to be fixed::

   Remove the link in the /usr/hppa64-linux-gnu directory to /usr/include:
   ls -lad /home/build*/build*/chroot-\*/usr/hppa64*/include

In each chroot, /etc/hosts has to look like this (e.g. needed for
mod-gnutls package)::

    127.0.0.1       localhost sibaris localhost.localdomain
    ::1             localhost ip6-localhost ip6-loopback
    ff02::1         ip6-allnodes
    ff02::2         ip6-allrouters

In each chroot modify (for unstable) /etc/apt/sources.list::

    deb [arch=all] http://incoming.debian.org/debian-buildd buildd-unstable main
    deb [arch=all] http://ftp.debian.org/debian unstable main
    deb http://ftp.ports.debian.org/debian-ports unstable main
    deb http://incoming.ports.debian.org/buildd unstable main
    # deb http://ftp.ports.debian.org/debian-ports experimental main
    # main/debian-installer
    deb-src http://ftp.debian.org/debian unstable main
    deb-src http://incoming.debian.org/debian-buildd buildd-unstable main

For building debian packages in the chroots, store the following script
as /usr/bin/fix-buildd into the chroots, and modify the chroot
definitions in /etc/schroot/chroot.d/ to include this line:
``command-prefix=fix-buildd``

.. code-block:: sh

    #!/bin/sh
    #-
    # Various fixes for the debian builds
    # everything in a function to avoid environment pollution
    runprog() {
      local cmd
      # 1. vtk6 builds fail if DOT_NUM_THREADS is not set to 1 (machine gets unresponsive)
      DOT_NUM_THREADS=1
      export DOT_NUM_THREADS
      # 2. enable parallel building of packages
      NUMCPU=$(getconf _NPROCESSORS_ONLN)
      DEB_BUILD_OPTIONS="parallel=$NUMCPU"
      export DEB_BUILD_OPTIONS
      # execute the command
      cmd=$1
      shift
      exec "$cmd" "$@"
    }
    runprog "$@"


wanna-build interface:

- https://wiki.debian.org/M68k/Porting#binNMU_notes
- http://lists.debian.org/debian-68k/2012/12/msg00124.html
- http://lists.debian.org/debian-68k/2013/10/msg00021.html
- http://kmuto.jp/open.cgi?buildd

How to create multiple buildds on one server
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- Create new user "buildd2", use new UID, but keep GID "buildd" (without
  "2"). Use new homedir, e.g. /home/buildd2

- Add user buildd2 to groups sbuild and buildd in /etc/group

- Copy original homedir from buildd to /home/buildd2 (you won't need
  existing Logs and upload-debs)

- Change ownership of /home/buildd2::

    chmod -R buildd2.buildd /home/buildd2

- Create a new chroot with /usr/sbin/sbuild-createchroot (name e.g.
  "unstable2"). Update the generated /etc/schroot/chroot.d/XXX file.
  Usually I have my chroots then under /home/buildd2/build-trees/chroots/

- In ~buildd2 create new .sbuildrc und .builddrc files, and change
  wanna_build_db_user => "buildd_hppa-phantom2" and sbuild_chroot =>
  "unstable2-hppa-sbuild"

- Log in as buildd2 and manually run "buildd" to check if everything
  works.

- If everything worked add the buildd-cronjobs for buildd2 in
  /etc/cron.d/buildd (copy existing entries and change copied entries to
  use "buildd2" instead of "buildd"

How to setup a dd porterbox
~~~~~~~~~~~~~~~~~~~~~~~~~~~

- https://wiki.debian.org/NonDSAPorterBoxSetUp

How to import a new porterbox apt key
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
- Problem is like this::

   Err:7 https://db.debian.org/debian-admin debian-all Release.gpg
   The following signatures were invalid: EXPKEYSIG 661EBB0E456D79AB db.debian.org archive key 2014

- Solution: Download key from https://salsa.debian.org/debian-ports-team/dsa-puppet/-/blob/cf872d5d5fe797e40f1bdf3855cda8309e2e10e1/modules/debian-org/files/db.debian.org.gpg
- then import it with::

    apt-key import db.debian.org.gpg

How to create a palo iplboot image from official Debian install CD
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
::

   palo --init-tape=lifimage -k vmlinux-3.13-1-parisc.gz -k vmlinux-3.13-1-parisc64-smp.gz -r initrd.gz --commandline='0/vmlinux initrd=0/ramdisk panic_timeout=60 panic=-1 debian-installer/allow_unauthenticated=true mirror/http/hostname=mkhppa3.esiee.fr mirror/http/directory=/debian'

How to prevent debian package to be built on failing architectures
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
Please, no, don't put a specific list of architectures in d/control. The
inability to build pandas on those architectures is a bug, whereas explicit
architecture lists in d/control should only be used if the package
fundamentally cannot work on others. Otherwise these failures disappear on
buildd.d.o and porters are unaware of them.
See: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=877419#30

How to build haskell/uuagc
~~~~~~~~~~~~~~~~~~~~~~~~~~
- http://foswiki.cs.uu.nl/foswiki/HUT/AttributeGrammarManual

.. code-block:: sh

    cd
    ghc --make Setup.hs -o setup -package Cabal
    ./setup configure
    ./setup build
    ./setup install

Debian packages and Java
~~~~~~~~~~~~~~~~~~~~~~~~
We don't have java support yet.
There seems to be a possibility on the buildds to set DEB_BUILD_PROFILES=nojava, see jpeg-xl rules file:

    # Override if profile states so:
    ifneq (,$(filter nojava,$(DEB_BUILD_PROFILES)))
    .... ENABLE_JNI=OFF
    endif


How to cross-compile debian kernel on x86 for hppa
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

#. git clone git://git.kernel.org/pub/scm/linux/kernel/git/stable/linux.git linux-5.10.y --depth 1
#. copy .config from debian into kernel directory

How to cross-build debian kernel from sources from kernel.org on Fedora
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

for 32bit::

      DPKG_FLAGS="-d -nc" make -C ../linux O=`pwd\` -j 20 ARCH=parisc bindeb-pkg

for 64bit::

      DPKG_FLAGS="-d -nc" make -C ../linux O=`pwd\` -j 20 ARCH=parisc64 bindeb-pkg

or for older kernels

- v5.10::

    DPKG_FLAGS="-d -nc" make -C ../linux O=`pwd\` -j 8 ARCH=parisc bindeb-pkg

- v5.16::

    make -j 30 ARCH=parisc64 CROSS_COMPILE=hppa64-linux-gnu- bindeb-pkg

- v4.19::

    DPKG_FLAGS="-d -nc" make -C ../linux O=`pwd\` -j 8 ARCH=parisc CROSS_COMPILE=hppa64-linux-gnu- bindeb-pkg 

  (when building on fedora maybe need to remove the "bc kmod.." dependencies in scripts/package/mkdebian)

If you choose to build debian source package kernel, you may need to:

- symlink in /usr/bin::

    ln -s hppa64-linux-gnu-gcc-10 hppa64-linux-gnu-gcc

- disable ABI checks in debian/bin/buildcheck.py, and
- drop \*perf\* package from: debian/control

Large file support for debian packages
--------------------------------------

- STATUS: Migration has started in March 2024.
- HINTS: https://wiki.debian.org/BrainDumpT64
- Possible to disable -Werror=implicit-function-declaration via **DEB_BUILD_MAINT_OPTIONS=qa=-bug-implicit-func**

Typical error message: **Value too large for defined data type**

Debian RELEASE GOAL: https://wiki.debian.org/ReleaseGoals/64bit-time https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=1026204 (tar missing time64 support)

| See debian BZ (glibc readdir() failure): https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=1020335
| New BZ: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=1028320

See musl time64 transition: https://musl.libc.org/time64.html

Debian bug (gdnsd depends on libev, both needs LFS support enabled): https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=1030983

https://sourceware.org/bugzilla/show_bug.cgi?id=23960 Specification: https://www.opengroup.org/platform/lfs.html

See Gentoo's "On time64 and Large File Support" https://sourceware.org/pipermail/libc-alpha/2022-November/143386.html

See Joseph Myers' comments on "[PATCH] Deprecate 32-bit off_t support": https://sourceware.org/pipermail/libc-alpha/2019-January/100410.html

Good summary: https://www.mjr19.org.uk/sw/inodes64.html

Examples of failing programs: - tar: https://bugs.debian.org/cgi-bin/bugreport.cgi?bug=1026204

If 32-bit applications use the "struct dirent" or "readdir()" glibc functions, e.g. like this::

    struct dirent *dp;
    while ( nullptr != (dp = readdir(dir)) )

then they need to be recompiled with this flags set::

    _FILE_OFFSET_BITS=64  _LARGEFILE_SOURCE

TODO: Maybe add too::

    __USE_TIME_BITS64

Solution::

    # manually add "-D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64" to CFLAGS,
    # manually add "future=+lfs" to DEB_BUILD_MAINT_OPTIONS, e.g.:
    -export DEB_BUILD_MAINT_OPTIONS = hardening=+all
    +export DEB_BUILD_MAINT_OPTIONS = hardening=+all future=+lfs
    # cmake doesn't respect CPPFLAGS, use the workaround as suggested in
    # https://wiki.debian.org/Hardening#Notes_for_packages_using_CMake
    CFLAGS += $(CPPFLAGS)
    CXXFLAGS += $(CPPFLAGS)

How to detect if an executable still uses non-large-file-accesses::

    LD_DEBUG="symbols,files" LD_BIND_NOW=1 /usr/bin/sed
    check output for readdir  (instead of readdir64)

Alternative solution::

    export DEB_BUILD_MAINT_OPTIONS = future=+lfs
    export DEB_CFLAGS_MAINT_APPEND = -Wall -Wno-analyzer-null-argument
    ifneq ($(shell dpkg-architecture -qDEB_TARGET_ARCH_BITS),64)
      export DEB_CPPFLAGS_MAINT_APPEND = -D_TIME_BITS=64
    endif
    DPKG_EXPORT_BUILDFLAGS = 1
    include /usr/share/dpkg/buildflags.mk 

Others::

    getconf LFS_CFLAGS
    # returns: -D_LARGEFILE_SOURCE -D_FILE_OFFSET_BITS=64
    CFLAGS := $(shell dpkg-buildflags --get CFLAGS) $$(getconf LFS_CFLAGS)
