# Juju Snap

## Building
After running snapcraft, the build will fail as it uses godeps.

godeps -u parts/juju/go/src/github.com/juju/juju/dependencies.tsv
go install github.com/juju/juju/...

## Current State

All features work on LXD, but require the following changes to apparmor post-installation.
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

## One-liner to run after install for apparmor issues
sudo sed -i '$ i\                                   
unix addr="@/var/snap/@{SNAP_NAME}/**",\n/var/lib/lxd/unix.socket rw,\n/etc/default/lxd-bridge rw,\n/usr/bin/ssh ixr,\n/usr/bin/sensible-browser ixr,\n' /var/lib/snapd/apparmor/profiles/snap.juju.juju
sudo apparmor_parser -r /var/lib/snapd/apparmor/profiles/snap.juju.juju

## Known Issues
Credentials aren't found, preventing deployments on public clouds
Small patch to juju source to change path from /var/juju to /var/snap.
Godeps needs a plugin to snappify with mucking
