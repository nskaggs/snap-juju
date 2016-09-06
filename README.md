THIS WORK IS UPSTREAMED and this repo is deprecated. Please check out https://github.com/juju/juju/.

# Juju Snap

## Building
This requires the godeps plugin -- make sure your snapcraft version is > 2.13.1.

## Current State
Needs devmode.
### Known Issues
 * Small patch to juju source to change path from /var/juju to /var/snap
 * Missing support for abstract mutex socket https://bugs.launchpad.net/snappy/+bug/1604967
 * Missing ssh interface https://bugs.launchpad.net/snappy/+bug/1606574
 * Missing LXD interface
 * Bash completion support https://bugs.launchpad.net/snappy/+bug/1590767

## Want to run under strict mode?
All features work, but require the following changes to apparmor post-installation for strict mode.
Add them to /var/lib/snapd/apparmor/profiles/snap.juju.juju and then rebuild the profile.

sudo apparmor_parser -r /var/lib/snapd/apparmor/profiles/snap.juju.juju

Support for https://bugs.launchpad.net/snappy/+bug/1604967.

unix addr="@/var/snap/@{SNAP_NAME}/**",

Next, until we have a LXD interface, we need to let apparmor know we need access:

  /var/lib/lxd/unix.socket rw,
  /etc/default/lxd-bridge rw,

Until there is an ssh interface:

/usr/bin/ssh ixr,

In order for the juju gui to work, add the following. This should be fixed in snapd 2.0.11.

/usr/bin/sensible-browser ixr,

### One-liner to edit apparmor profile
sudo sed -i '$ i\
unix addr="@/var/snap/@{SNAP_NAME}/**",\n/var/lib/lxd/unix.socket rw,\n/etc/default/lxd-bridge rw,\n/usr/bin/ssh ixr,\n/usr/bin/sensible-browser ixr,\n' /var/lib/snapd/apparmor/profiles/snap.juju.juju

sudo apparmor_parser -r /var/lib/snapd/apparmor/profiles/snap.juju.juju
