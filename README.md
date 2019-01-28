# macOS-clamAV

A simple macOS clamAV configuration with scheduled volume scans and on-access scans of user `Downloads` and `Desktop` directories.

This configures [clamAV](http://www.clamav.net) for macOS with regular on-demand scans and on-access scanning of user `Downloads` and `Desktop` directories.

## Installation and Configuration

This uses [MacPorts](https://www.macports.org). It is also easy to use [Homebrew](https://brew.sh).

To install and configure:

```
sudo port install clamav clamav-server fswatch pcregrep
sudo install -b -B .orig ./clamd.conf /opt/local/etc
sudo install -b -B .orig ./freshclam.conf /opt/local/etc
sudo install ./org.macports.clamdscan.plist /Library/LaunchDaemons
sudo mkdir -p /opt/local/etc/LaunchDaemons/org.macports.ClamdScanOnAccess
sudo install -m 755 ./ClamdScanOnAccess.wrapper /opt/local/etc/LaunchDaemons/org.macports.ClamdScanOnAccess
sudo install -m 644 ./org.macports.ClamdScanOnAccess.plist /opt/local/etc/LaunchDaemons/org.macports.ClamdScanOnAccess
sudo install -m 644 ./org.macports.ClamdScanOnDemand.plist /Library/LaunchDaemons
sudo mkdir /opt/local/share/clamav
sudo chown -R clamav:clamav /opt/local/share/clamav
sudo mkdir /opt/Quarantine
sudo -u clamav freshclam
sudo launchctl load -w /Library/LaunchDaemons/org.macports.clamd.plist
sudo launchctl load -w /Library/LaunchDaemons/org.macports.freshclam.plist
sudo launchctl load -w /Library/LaunchDaemons/org.macports.clamdscan.plist
sudo launchctl load -w /Library/LaunchDaemons/org.macports.ClamdScanOnDemand.plist
```

To update the `clamav` engine and database:

```
sudo port selfupdate
sudo port -puN upgrade clamav clamav-server
sudo -u clamav freshclam
```

## Scheduled On-Demand Scanning

On-Demand scanning is controlled with the launchd daemon [org.macports.clamdscan.plist](./org.macports.clamdscan.plist).

## On-Access Scanning

On-Access scanning via [fswatch](https://github.com/emcrisostomo/fswatch) is controlled with the Macports daemon script 
[ClamdScanOnAccess.wrapper](./ClamdScanOnAccess.wrapper), itself invoked using the launchd daemon 
[org.macports.ClamdScanOnDemand.plist](./org.macports.ClamdScanOnDemand.plist). The `Downloads` and `Desktop` directories of 
all active users are watched by default.

## Mojave Privacy Protections

macOS 10.14 Mojave includes new privacy protections under `System Preferences>Security & Privacy>Privacy>Full Disk Access` ("TCC"). Scanning files protected by TCC requires granting access to these binaries:
* `/opt/local/sbin/clamd`
* `/opt/local/bin/clamdscan`
* `/opt/local/bin/clamscan`

and possibly `/Applications/Utilities/Terminal.app` for command line scan calls. Dragging and dropping these files from the Finder app into the pane `System Preferences>Security & Privacy>Privacy>Full Disk Access` will grant access.

## Details

Excluded files are set in [clamd.conf](./clamd.conf), including macOS SIP protected directories. Change this to scan all directories. The default scanned directory is `/`, every week early Sunday morning. Edit the bash command in [org.macports.clamdscan.plist](./org.macports.clamdscan.plist) and unload/load this plist to change this behavior. For example, change the shell array variable `SCAN_TARGETS` to scan these volumes (using XML [compliant](http://xml.silmaril.ie/specials.html) quoted special characters to quote spaces in directory or file names):
> `SCAN_TARGETS=(/ &quot;/Volumes/Server HD&quot;)`
