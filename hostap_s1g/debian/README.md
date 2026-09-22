# hostap_s1g

Debian packaging for [MorseMicro/hostap](https://github.com/MorseMicro/hostap).

## How it works

This directory contains only the `debian/` packaging files. The upstream
source (`hostap-s1g_<version>.orig.tar.gz`) is created with
`get-orig-source`, which downloads the `mm8108-<version>` tag from GitHub,
then unpacked into the working tree with `origtargz --unpack`.

## Building

```bash
# Create the orig tarball (downloads the upstream tag)
make -f debian/rules get-orig-source

# Unpack upstream source into the working tree
origtargz --unpack=yes

# Build the package
dpkg-buildpackage -us -uc
```

## Package details

- **Source package**: hostap-s1g
- **Binary packages**: hostapd-s1g, wpa-supplicant-s1g
- **Upstream**: MorseMicro fork with 802.11ah (Wi-Fi HaLow / S1G) support
- **Upstream tags**: `mm8108-<version>` (upstream tags the MM6108 and
  MM8108 series separately; this package follows the MM8108 series and
  strips the `mm8108-` prefix from the Debian upstream version)
- **Build options**: upstream `defconfig` (which already enables the S1G
  features, including TWT, standby mode and keep-alive offload), plus
  CONFIG_IEEE80211AH for hostapd, and 802.11s mesh (CONFIG_MESH) and the
  PEAP, MD5, MSCHAPv2, TLS, TLS 1.3, TTLS, GTC and PWD EAP methods for
  wpa_supplicant

Binaries are installed with `-s1g` suffix to allow coexistence with stock
Debian hostapd and wpasupplicant packages.

## NetworkManager is not supported

`wpa_supplicant_s1g` is built without D-Bus (`CONFIG_CTRL_IFACE_DBUS_NEW`), so
NetworkManager cannot drive a HaLow interface. This is deliberate: both
NetworkManager and the supplicant's `WPAS_DBUS_NEW_SERVICE` hardcode the bus
name `fi.w1.wpa_supplicant1`, so enabling D-Bus here would only make this
package contend with Debian's `wpasupplicant` for that name.

Use the per-interface unit instead, and tell NetworkManager to leave the
interface alone via `unmanaged-devices=interface-name:<iface>` in
`/etc/NetworkManager/conf.d/`:

```bash
# /etc/wpa-supplicant-s1g/wpa_supplicant-<iface>.conf
systemctl enable --now wpa-supplicant-s1g@<iface>.service
```
