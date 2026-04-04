# hostap_s1g

Debian packaging for [MorseMicro/hostap](https://github.com/MorseMicro/hostap).

## How it works

This directory contains only the `debian/` packaging files. The upstream
source (`hostap-s1g_<version>.orig.tar.gz`) must be created with
`get-orig-source` (because upstream uses branch names instead of tags),
then unpacked into the working tree with `origtargz --unpack`.

## Building

```bash
# Create the orig tarball (downloads from the upstream branch)
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
- **S1G build options**: CONFIG_IEEE80211AH, CONFIG_S1G_TWT, CONFIG_MORSE_STANDBY_MODE, CONFIG_MORSE_KEEP_ALIVE_OFFLOAD

Binaries are installed with `-s1g` suffix to allow coexistence with stock
Debian hostapd and wpasupplicant packages.
