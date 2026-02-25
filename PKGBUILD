# Maintainer: David Bohner <davidbohner@protonmail.ch>
# Modified for HSLU compliance compatibility
# PKGBUILD Modified from upstream AUR: https://aur.archlinux.org/packages/pulse-secure

pkgname=pulse-secure
pkgver=22.8r5_b41063
pkgrel=1
pkgdesc='Ivanti Secure Access Client'
arch=(x86_64)
license=(custom)
url='https://www.pulsesecure.net/'
depends=(gcc-libs libgnome-keyring openssl curl dbus libbsd dmidecode)
install=${pkgname}.install
options=(!strip !debug)
optdepends=('psmisc: for pulsesvc -K' 'gtkmm3: for pulseUi' 'webkit2gtk: for pulseUi')
conflicts=(pulse-connect-secure)

source_x86_64=("ps-pulse-linux-22.8r5-b41063-installer.rpm")
md5sums_x86_64=('93a073f8ff0f30d71bb603388dadb417')

package() {
    install -Dm644 EULA.txt "$pkgdir"/usr/share/licenses/$pkgname/EULA.txt

    for f in $(find opt/pulsesecure/bin -type f); do
        install -Dm755 "$f" "$pkgdir"/"$f"
    done
    for f in $(find opt/pulsesecure/lib -type f); do
        install -Dm755 "$f" "$pkgdir"/"$f"
    done
    for f in $(find opt/pulsesecure/resource -type f); do
        install -Dm644 "$f" "$pkgdir"/"$f"
    done

    install -Dm644 usr/share/man/man1/pulse.1.gz "$pkgdir"/usr/share/man/man1/pulse.1.gz

    # systemd unit goes to /usr/lib/systemd/system per pacman convention
    install -Dm644 lib/systemd/system/pulsesecure.service "$pkgdir"/usr/lib/systemd/system/pulsesecure.service

    mkdir -p "$pkgdir"/usr/share/applications/ \
             "$pkgdir"/usr/share/dbus-1/system.d/ \
             "$pkgdir"/opt/pulsesecure/lib/JUNS/interfaces \
             "$pkgdir"/var/lib/pulsesecure/pulse

    ln -s /opt/pulsesecure/resource/pulse.desktop "$pkgdir"/usr/share/applications/pulse.desktop
    ln -s /opt/pulsesecure/lib/JUNS/net.psecure.pulse.conf "$pkgdir"/usr/share/dbus-1/system.d/net.psecure.pulse.conf

    # Map Arch's CA bundle to the RHEL-style path the client expects
    mkdir -p "$pkgdir"/etc/pki/ca-trust/extracted/openssl
    ln -sf /etc/ca-certificates/extracted/ca-bundle.trust.crt "$pkgdir"/etc/pki/ca-trust/extracted/openssl/ca-bundle.trust.crt

    for f in $(find opt/pulsesecure/lib/JUNS/interfaces -type l); do
        ln -s $(readlink "$f") "$pkgdir"/"$f"
    done
}