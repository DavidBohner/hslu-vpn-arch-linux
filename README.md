# pulse-secure (Arch Linux) for HSLU

A modified [AUR `pulse-secure` package](https://aur.archlinux.org/packages/pulse-secure) for use with HSLU's VPN.

**Credit:** This package is based almost entirely on the work of Chiwan Park (AUR maintainer).

---

## Why this exists

I struggled for an afternoon trying to determine why the AUR's pulse-secure was suddenly out of compliance and unable to connect.
The endpoint has started to verify file signatures, and makepkg defaults to stripping binaries, which while optimising the result does change file hashes.

This variant:
- Targets version `22.8r5-b41063` as currently distributed by HSLU
- Disables binary stripping (`!strip !debug`) to preserve file integrity for compliance checks
- Removes a now redundant `setup_cef.sh` patch for a bug in the CEF-installer's hash matching that was fixed upstream

---

## Requirements

- Dependency packages
    - libsecret
    - gtkmm3
    - webkit2gtk

    (`gtkmm3` and `webkit2gtk`, while optional for pulse itself, are required for the UI)
- The RPM installer downloaded from HSLU's software portal

---

## Installation

1. **Download the RPM** from [HSLU's software center](http://softwaredownload.hslu.ch/).

2. **Clone this repository** and place the RPM in the same directory:
```bash
   git clone https://github.com/DavidBohner/hslu-vpn-arch-linux.git
   cd hslu-vpn-arch-linux
   cp /path/to/pulse-installer.rpm .
```

3. **Build and install:**
```bash
   makepkg -sri
```
   This will resolve dependencies, build the package, install it via pacman, and remove build dependencies.

4. **Enable and start the service:**
```bash
   sudo systemctl enable --now pulsesecure
```

5. **Install the CEF:**
```bash
   sudo /opt/pulsesecure/bin/setup_cef.sh install
```

6. Launch **Pulse Secure / Ivanti Secure Access** from your application menu or run `/opt/pulsesecure/bin/pulseUi`.

---

## Updating to a new version

When HSLU distributes a newer RPM:

1. Download the new RPM from the software portal.
2. `git pull`, or update `pkgver`, `pkgrel`, and the filename in `source_x86_64` in `PKGBUILD` if I haven't already.
3. Update the checksum in `md5sums_x86_64`: (or run step 4 with --skipchecksums)
```bash
   md5sum ps-pulse-linux-<newversion>-installer.rpm
```
4. Place the new RPM in the repo directory and run `makepkg -sri` again.
5. (opt) If you had to do step 2 manually .. let me know? I don't automatically get notified when a new version becomes available.
---

## Troubleshooting

**Compliance check fails on connect**  
Make sure you built with this PKGBUILD and not the standard AUR one. The `!strip !debug` options are essential - stripping the binaries modifies them, causing the integrity check to fail.

**Service not running**  
```bash
sudo systemctl status pulsesecure
sudo journalctl -u pulsesecure -e
```

**UI doesn't launch**  
Install dependencies `gtkmm3` (extra) and `webkit2gtk` (AUR).

**Chromium Embedded Framework (CEF) won't start**  
Check if the install script ran successfully.
```bash
sudo /opt/pulsesecure/bin/setup_cef.sh check_installed
sudo /opt/pulsesecure/bin/setup_cef.sh uninstall
sudo /opt/pulsesecure/bin/setup_cef.sh install
```

**Anything else**  
Feel free to contact me / raise an issue. When Linux doesn't get official support, we must do it ~~ourselves~~ together :).  
The Arch Wiki [also has an article](https://wiki.archlinux.org/title/Pulse_Connect_Secure) that could be of use.

---

## License

The PKGBUILD and install scripts are provided under the same terms as the upstream AUR package.
The Ivanti Secure Access Client itself is proprietary software - by using it you agree to their EULA.


## Disclaimer
This repository contains no copy of the Ivanti Secure Access Client software. The RPM installer must be downloaded separately from HSLU's software portal, which requires a valid university account. This packaging simply makes it easier to install a copy you are already entitled to receive.
