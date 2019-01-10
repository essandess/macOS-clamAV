# macOS-clamAV

A simple clamAV configuration for macOS with scheduled on-demand scanning.

This configures [clamAV](http://www.clamav.net) for macOS with regular on-demand scans.

## Installation and Configuration

This uses [MacPorts](https://www.macports.org). It is also easy to use [Homebrew](https://brew.sh).

To install and configure:

```
sudo port install clamav clamav-server fswatch
sudo install -b -B .orig ./clamd.conf /opt/local/etc
sudo install -b -B .orig ./freshclam.conf /opt/local/etc
sudo install ./org.macports.clamdscan.plist /Library/LaunchDaemons
sudo mkdir /opt/local/share/clamav
sudo chown -R clamav:clamav /opt/local/share/clamav
sudo mkdir /opt/Quarantine
sudo -u clamav freshclam
sudo launchctl load -w /Library/LaunchDaemons/org.macports.clamd.plist
sudo launchctl load -w /Library/LaunchDaemons/org.macports.freshclam.plist
sudo launchctl load -w /Library/LaunchDaemons/org.macports.clamdscan.plist
```

To update the `clamav` ehngine and database:

```
sudo port selfupdate
sudo port -puN upgrade clamav clamav-server
sudo -u clamav freshclam
```

## Mojave Privacy Protections

macOS 10.14 Mojave includes new privacy protections under `System Preferences>Security & Privacy>Privacy>Full Disk Access` ("TCC"). Scanning files protected by TCC requires granting access to these binaries:
* `/opt/local/sbin/clamd`
* `/opt/local/sbin/clamdscan`
* `/opt/local/sbin/clamscan`

and possibly `/Applications/Utilities/Terminal.app` for command line scan calls.

## Details

Excluded files are set in [clamd.conf](./clamd.conf), including macOS SIP protected directories. Change this to scan all directories. The default scanned directory is `/`, every week early Sunday morning. Edit the bash command in [org.macports.clamdscan.plist](./org.macports.clamdscan.plist) and unload/load this plist to change this behavior.
