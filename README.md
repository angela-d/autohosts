# Auto Update Ad-blocking Hosts file on Linux and Mac Systems

Automate hosts file updates on Linux-based and MacOS systems.

![Linux](./img/linux.png)
![Mac](./img/mac.png)

## v3.1.0 Changes

- Nothing of interest to Linux users
- Use LaunchAgents instead of cron (Mac)
- [MDM helper scripts](./mac-mdm-deployment/) added to repo for mass deployment

### Purpose of Hosts Files
Hosts files will reroute unwanted traffic from ad farms, behavioral tracking firms and malware sites to a blackhole; routing to 0.0.0.0 (localhost; your PC) when a request is made to a URL on the blacklist.

Which means any traffic that would have left your system for that destination, is sent inward, to your localhost and then abandoned.

Despite what some may suggest, hosts files are not "1980s technology" and still very useful today, as an additional *layer* of security.

Hosts file are a useful redundancy when coupled with ad blockers like [uBlock Origin](https://github.com/gorhill/uBlock) and [uMatrix](https://github.com/gorhill/uMatrix) - while debugging or 'Temporarily Allow All on this Site' with [Noscript](https://noscript.net/) can open you up to underlying attacks or privacy intrusions.

- If you're a Safari user and want a pretty pre-made GUI for your adblocking, try [AdGuard](https://adguard.com/en/blog/adguard-safari-extension.html) - *note that this app is a plugin for Safari only; if you want cross-browser blocking, they have paid options, or you can use something like Autohosts for free (this very repo!).*

**In-browser filters won't protect you if the [browser itself](https://spyware.neocities.org/articles/firefox.html) is [phoning home](https://dustri.org/b/mozilla-is-still-screwing-around-with-privacy-in-firefox.html).**

If you have an up-to-date hosts file, the risk is severely lessened.

**Auto Hosts** will automate the setup process for maintaining an up to date hosts file, by:
 - Installing a weekly cronjob to pull fresh/updated copies of hosts lists (default is every Sunday at 7:22pm)
 - (Default custom_filters and firefox-includes) source appends Facebook trackers, Linkedin ads, Google fonts, Firefox telemetry and other harvester sites that curated lists for whatever reason, have not added to their blacklists
 - Refreshes DNS to instantiate the re-routed changes (Mac Only)
 - If [Devdom](https://notabug.org/angela/devdom) is installed, append all local virtualhosts (*if it is installed* - it is -not- a requirement!  Autohosts does a conditional check to ensure your dev environment doesn't break **if** you're using Devdom.  Read the code.)
 - Uninstall offers the ability to revert to a fresh hosts file and undo any changes made by Autohosts; a backup will be copied to /tmp
 - Add custom blacklists, whitelists and host sources from ~/autohosts on your desktop

## Who Should Use Autohosts?
If you are frequently mobile, your privacy-hardened installs of Pi-Hole, OpenWRT or DD-WRT on your home router will not protect you.  Autohosts will shield you while on the go, independent of whose network you logon to.

Likewise, not everyone has the means to flash custom router firmware or the skill necessary to do so.  Autohosts can fill that gap.


## Installing:
![Install](./img/install.png)

### Firefox Snooping list, originally by LocalFigurez
For those of us who want our browser to behave like a browser and not a GPS anklet, this list will prove useful by blocking a [plethora of tracking URLs](firefox-includes).  A few of which, some Firefox users have noticed seem to phone home even when the setting is toggled off in about:config.

### Custom filters
You can easily append any domain you want to blacklist directly from your home directory.  Your custom preferences will be stored with each subsequent update - set it and forget it!

### Whitelisting
If the upstream list curators block a domain you want to see, add it to `~/autohosts/whitelist` and run `sudo autohosts`

Be sure to add nothing other than the domain(s) you want to whitelist to this file!  I added 3 examples to the default whitelist file; you can regex it (TLD domain with no prefix or suffix, to whitelist subdomains, too: `example`) or supply a full TLD: `www.example.com`

## To Install
Debian-based Linux distros:
```bash
git clone https://github.com/angela-d/autohosts.git && cd autohosts && sudo apt install ./autohosts.deb
```

MacOS or non-Debian-based Linux distros:
```bash
git clone https://github.com/angela-d/autohosts.git && cd autohosts && sudo ./autohosts
```
That's it !

***
## To upgrade an existing install
To see which version you're running on your system: `grep "VERSION=" /etc/autohosts.conf`


### Non-Debian-based distros or MacOS:
Due to structural changes in the codebase, an uninstall and reinstall is recommended (unless installed via deb).  You can do this without losing your custom filters.
```bash
git clone https://github.com/angela-d/autohosts.git /tmp/autohosts && cd /tmp/autohosts && cp ~/autohosts/custom_filters /tmp/custom_filters
```
Once installation completes, restore your custom filters:
```bash
rm ~/autohosts/custom_filters && mv /tmp/custom_filters ~/autohosts/custom_filters
```

### Debian users
Simply download the latest .deb & run (in the directory where you put the download):
```bash
apt install ./autohosts.deb
```
to upgrade to the latest version.
***

## Adding Custom Blacklists or Whitelists
- These files can be edited in your favorite text editor; command-line modifications are not necessary!

Custom filters are loaded to your home directory:


| Filter Option  | ![Linux](./img/linux.png) Linux File Location | ![Mac](./img/mac.png) Mac File Location|
| ------------- |:-------------:| :-----:|
| **Blacklist**      | `~/autohosts/custom_filters` or `/home/your_username/autohosts/custom_filters` | `~/autohosts/custom_filters` or `/Users/your_username/autohosts/custom_filters` |
| **Whitelist**      | `~/autohosts/whitelist` or `/home/your_username/autohosts/whitelist`      |   `~/autohosts/whitelist` or `/Users/your_username/autohosts/whitelist` |
| **Hosts List Curator** | `~/autohosts/hosts_source` or `/home/your_username/autohosts/hosts_source` | `~/autohosts/hosts_source` or `/Users/your_username/autohosts/hosts_source` |




### Choosing Hosts Curators
Use as many as you'd like, to strengthen your filtering.  Though it would be wise to keep the total to a reasonable amount; as there is not currently any duplicate removal, so the potential for unneeded overhead is certain.

List each curator on a separate line in `~/autohosts/hosts_source` - do not add any comments or whitespace to this file - just a list of the [**raw**](https://github.com/StevenBlack/hosts/blob/master/hosts?raw=true) hosts source.  (ie. the plain-text filters).

When an update is ran, Autohosts will probe each curator to ensure the list is responding with a 200/OK response, so the potential for indexing garbled junk to your hosts file is severely lessened.


### Adjust the cron time
If your computer is not powered on when the cron is scheduled, you'll miss the update.  Ensure the cronjob is set for a time when you're most likely to have it on.  You can adjust it by running:
```bash
sudo crontab -e
```
and modifying the dates to suit.

Cron legend:
```html
* * * * * = minute, hour, day of month, month, day of week (0 = Sunday, 6 = Saturday)
```
(`*` = *every*, so 5 straight stars is equal to every minute of every hour of every day and every month.. which you should never run while pulling 3rd party content!)

Uninstalling:

![Install](./img/uninstall.png)

**Note:** Because this script has to modify `/etc/hosts` - it needs elevated privileges (running as root or a sudo user).  Scripts that require elevated privileges should be read and analyzed so you know what's being done to your system!  Read the source code of this script (and any others requiring such permissions) before you install.

### Alternative uses with Hosts files
- If you're running DD-WRT, you can add a cron to pull a [hosts file for your entire network](https://github.com/angela-d/brain-dump/blob/master/networking/dd-wrt.md) or load [Pi-Hole](https://pi-hole.net/) to a Raspberry Pi.
- OpenWRT also has [network-based adblocking](https://github.com/angela-d/brain-dump/blob/master/networking/openwrt-site-blocking.md) capabilities.
- Rooted Android devices can utilize hosts files, too; simply load the hosts file via `adb push` to `/etc/hosts`
- If you have to use Windows, [Unified Hosts Autoupdate](https://github.com/ScriptTiger/Unified-Hosts-AutoUpdate) offers similar capabilities to Autohosts, in a Windows environment

### Troubleshooting
If it appears autohosts isn't blocking anything, your browser likely has imposed DoH / DNS Over HTTPS on you.

***

**If you'd like to disable DoH:**

Vivaldi or Chrome / Chromium:

- Enter `chrome://flags` in your address bar and find: `Secure DNS lookups` > toggle to disable

Firefox:

- Enter `about:preferences` on your address bar and in the search box, enter `dns` > Settings > scroll to the bottom and un-tick `Enable DNS over HTTPS`

### Known Bugs / To Fix
manpage on Debian isn't seen.  No manpage at all for non-Debian installs.
