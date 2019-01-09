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
