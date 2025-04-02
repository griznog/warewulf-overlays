# `systemd-munge.service`

Munge setup overlay for Warewulf

## Dependencies

* RHEL and derivatives:
    * `dnf install -y epel-release`
    * `dnf install -y munge`
    * (Optional) `dnf install -y munge-devel`

* SUSE:
    * Untested.

* Debian/Ubuntu:
    * Untested.

## Creating the `munge` key.

Due to the way WW templates work, the last byte in the munge key file matters.
This will produce a key which works with this overlay and only contains
printable ASCII characters and ends with a linefeed:

```
dd if=/dev/urandom | base64| head -c 1024 > /etc/munge/munge.key
echo >> /etc/munge/munge.key
```

## Permissions and ownership.

The `munge` packages create a local `munge` user in the node image. The overlay
adds an `override.conf` file on the `munge.service` service which corrects file
permissions and ownership when the service is started. Since the ownership will
be corrected post-boot at service start, we only need to worry about having the
correct mode on teh local file to make sure the key remains readable only by
`root` until the booted node starts the `munge` service. There's no need to
sync the `munge` user between master or nodes or have a gloabl `munge` account.  

## Tags and resources

This overlay reads the munge key from teh master node and does not require
setting any tags or resources. 
