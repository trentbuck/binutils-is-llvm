Status
============================================================
As at 2021-04-07:

• binutils-is-llvm **WORKS** for ``needrestart -p`` (on Debian 11 bullseye).

  https://bugs.debian.org/986507 is an alternative.

• binutils-is-llvm **DOES NOT WORK** on for ``apt install build-essential``, because
  that has a versioned dependency on ``binutils (>= X)``.

  To fix this, I would need to either make debian/changelog have corresponding version, or
  change my ``Provides: binutils`` to ``Provides: binutils (= X)``.


Problem
============================================================
I want ``check-support-status`` to be happy, but I need ``needrestart``::

    bash5$ check-support-status
    Limited security support for one or more packages

    Unfortunately, it has been necessary to limit security support for some
    packages.

    The following packages found on this system are affected by this:

    * Source:binutils
      Details: Only suitable for trusted content; see https://lists.debian.org/msgid-search/87lfqsomtg.fsf@mid.deneb.enyo.de
      Affected binary packages:
      - binutils (installed version: 2.35.2-2)
      - binutils-common:amd64 (installed version: 2.35.2-2)
      - binutils-x86-64-linux-gnu (installed version: 2.35.2-2)
      - libbinutils:amd64 (installed version: 2.35.2-2)
      - libctf-nobfd0:amd64 (installed version: 2.35.2-2)
      - libctf0:amd64 (installed version: 2.35.2-2)


    bash5$ aptitude why binutils
    i   needrestart Depends binutils

    bash5$ sudo needrestart -p
    CRIT - Kernel: 5.10.0-4-amd64!=5.10.0-5-amd64 (!!), Microcode: CURRENT, Services: 6 (!), Containers: none, Sessions: none|Kernel=2;0;;0;2 Microcode=0;0;;0;1 Services=6;;0;0 Containers=0;;0;0 Sessions=0;0;;0
    Services:
    - dbus.service
    - getty@tty1.service
    - systemd-logind.service
    - unattended-upgrades.service
    - user@0.service
    - user@1000.service


Has anyone tried to solve this already?::

    /connect irc.oftc.net
    /join #debian-security
    <twb> check-support-status (rightly) complains about binutils, but
          it's hard to avoid installing it.
          For example, both build-essential and needrestart need it.

          Is there a way to replace it with llvm (e.g. /usr/bin/strings ->  llvm-strings-10), so
          binutils can be purged, but without breaking anything?

    <pabs> add an equivs package perhaps?

    <twb> I'm thinking along those lines, yes.
          But I'm not sure how well the different implementation's arguments will line up.
          So it would need quite a bit of testing, and
          I was hoping someone else had already gotten annoyed enough to do it :-) 
