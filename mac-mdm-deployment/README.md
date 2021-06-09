# MDM Mass Deployment of Autohosts
Ignore this if you're an end-user and not deploying to a fleet of Macs.  Instructions for singular users is on the main README.

Helper scripts can be found in the [Mac MDM Deployment](./mac-mdm-deployment/) folder.

## Upload the Following to Your MDM
- [ ] [autohosts core script](../autohosts) - from the main repo index
- [ ] [com.github.angela-d-autohosts.plist](./Library/LaunchAgents/com.github.angela-d-autohosts.plist) - plist config from the *mac-mdm-deployment* folder
- [ ] [load-autohosts.sh](./var/scripts/autohosts/load-autohosts.sh) - LaunchAgent loader
- [ ] [unload-autohosts.sh](./var/scripts/autohosts/unload-autohosts.sh) - LaunchAgent unloader
- [ ] [uninstall script](../uninstall-autohosts) - from the main repo index

The core Autohost scripts expects the following files to exist during initial install; it makes a copy of them to the 'current' user's /User directory:
- [ ] [custom_filters](./var/scripts/autohosts/custom_filters) - Anything you want blocked that isn't in upstream (can be partial TLDs)
- [ ] [hosts_source](./var/scripts/autohosts/hosts_source) - By default, only Steven Black's host file is used; add/remove your own
- [ ] [whitelist](./var/scripts/autohosts/whitelist) - Anything you want to whitelist that may be blocked upstream (presence in **whitelist** will remove it upon each update of the hosts file)

### Things to Check For
- [ ] Autohosts core, LaunchAgent plist must load as root/sudo
- [ ] LaunchAgent daemon; ensure this is added to your MDM in fashion so it puts it in `/Library/LaunchAgents`

**Activation Script(s)**

- [ ] load-autohosts.sh

**Postflight Script(s)**

- [ ] autohosts (core script)

**Pre-Uninstallation Script(s)**

- [ ] unload-autohosts.sh

**Post-Uninstallation Script(s)**

- [ ] uninstall-autohosts
