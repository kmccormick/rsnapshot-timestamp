# rsnapshot-timestamp
Rsnapshot wrapper using timestamps as snapshot directory names

`rsnapshot-timestamp` completely replaces `rsnapshot`'s rotation and cleanup. It makes use of the `sync_first` option, calls `rsnapshot sync`, then copies the .sync directory to the current timestamp, and cleans up old snapshots. The cleanup occurs based on values specified in `rsnapshot.conf` `retain` settings. Due to the way the cleanup works, the names of the `retain` periods are no longer arbitrary.

## Quick Start
* Change your 'retain' periods to these values, which mean what they say:
  * `hours`
  * `days`
  * `weeks`
  * `months`
  * `years`
* Enable `sync_first`
* Ensure `link_dest` is disabled
* Replace `rsnapshot` calls with `rsnapshot-timestamp`
  * On your shortest duration `rsnpashot`, now use `rsnapshot-timestamp sync`
  * Remove longer duration `rsnapshot` calls

## Usage
    rsnapshot-timestamp [-c config] [-t] [rsnapshot_options] sync

`rsnapshot-timestamp` passes the entire argument string on to `rsnapshot`. Just call it exactly like you would `rsnapshot`, but ONLY ever with `sync` as the last parameter. `rsnapshot-timestamp` understands `rsnapshot`'s `-c` and `-t` options, and uses them in the same way `rsnapshot` does.

## Example rsnapshot.conf
    config_version	1.2
    snapshot_root	/.snapshots/
    cmd_cp	/bin/cp
    cmd_rm	/bin/rm
    cmd_rsync	/usr/bin/rsync
    cmd_ssh	/usr/bin/ssh
    cmd_logger	/usr/bin/logger
    retain	days	7
    retain	weeks	4
    retain	months	12
    sync_first	1
    backup	/etc/	localhost/
    backup	/usr/local/	localhost/
    backup	/home/	localhost/

## Example crontab
    # Default rsnapshot entries disabled
    #30 2   1 * *   root    /usr/bin/rsnapshot monthly
    #0  3   * * 1   root    /usr/bin/rsnapshot weekly
    #30 3   * * *   root    /usr/bin/rsnapshot daily
    # Rsnapshot-timestamp handles sync, rotation, and cleanup in one go
    30 3    * * *   root    /opt/rsnapshot-timestamp/rsnapshot-timestamp sync
