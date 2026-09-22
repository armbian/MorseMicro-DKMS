# Deb-MorseMicro
Debian packaging for MorseMicro

## Building

Use the devcontainer, and follow the instructions for each package.

## Packages

- [`hostapd-s1g`](hostap_s1g/debian/): `hostapd` with support for 802.11ah (S1G) features.
- [`wpa-supplicant-s1g`](hostap_s1g/debian/): `wpa_supplicant` with support for 802.11ah (S1G) features.
- [`morse-cli`](morse_cli/debian/): Command-line utility for interacting with Morse Micro devices.
- [`mm6108-driver-dkms`](mm6108-driver-dkms/debian/): DKMS package for the Morse Micro MM6108 driver.
- [`mm6108-firmware`](mm6108-firmware/debian/): Firmware files for Morse Micro MM6108 devices.
- [`mm8108-driver-dkms`](mm8108-driver-dkms/debian/): DKMS package for the Morse Micro MM8108 driver.
- [`mm8108-firmware`](mm8108-firmware/debian/): Firmware files for Morse Micro MM8108 devices.

## Connecting to a HaLow network

Station mode, using `wpa-supplicant-s1g`. Use `hostapd-s1g` to run an AP
instead.

**1. Find the interface.** udev names it after its MAC:

```bash
ip -br link | grep ^wlx
```

**2. NetworkManager is already kept off it.** The driver package ships a udev
rule marking `morse_*` interfaces `NM_UNMANAGED`, because NetworkManager cannot
drive a HaLow interface at all — see
[`hostap_s1g/debian/README.md`](hostap_s1g/debian/README.md) for why. To hand a
device to it anyway: `nmcli device set <iface> managed yes`.

**3. Write `/etc/wpa-supplicant-s1g/wpa_supplicant-<iface>.conf`**, mode
`0600`. Most HaLow APs use WPA3-SAE:

```
ctrl_interface=DIR=/run/wpa_supplicant_s1g GROUP=netdev
country=US
sae_pwe=2

network={
	ssid="my-halow-ap"
	key_mgmt=SAE
	ieee80211w=2
	sae_password="your-passphrase"
}
```

- `country` must match the AP's regulatory domain, and is separate from the
  driver's `country` module parameter.
- `ieee80211w=2` (PMF required) is mandatory for SAE.
- `sae_pwe` defaults to `0`, hunt-and-peck only, which will not associate with
  an H2E-only AP. `2` accepts both, `1` requires H2E.
- For WPA2-PSK use `key_mgmt=WPA-PSK` and `psk="..."`.

**4. Start it.** The template unit reads the config named after the interface:

```bash
sudo systemctl enable --now wpa-supplicant-s1g@wlx0cbf740028b3.service
```

**5. Check the link.** `netdev` members need no `sudo`; `-p` is required or
`wpa_cli_s1g` talks to the stock supplicant:

```bash
wpa_cli_s1g -p /run/wpa_supplicant_s1g -i wlx0cbf740028b3 status
```

`wpa_state=COMPLETED` means associated. `freq`, `WIDTH` and `LINKSPEED` are
fictional — `dot11ah` maps S1G channels onto 5 GHz ones so stock `mac80211`,
`iw` and `wpa_supplicant` work. `freq=5825 / WIDTH=40 MHz / LINKSPEED=150` is
really a 2 MHz channel near 925 MHz at roughly 7.5 Mbps.

**6. Get an address.** Both flags matter: `-G` keeps the HaLow lease from
replacing your default route, `--nohook resolv.conf` keeps it from replacing
`/etc/resolv.conf` with the AP's nameserver, which on an isolated network may
be unreachable and will take down DNS host-wide.

```bash
sudo dhcpcd -G --nohook resolv.conf wlx0cbf740028b3
```

To make that permanent, in `/etc/dhcpcd.conf`:

```
interface wlx0cbf740028b3
    nogateway
    nohook resolv.conf
```

## Driver settings

List the `morse` module parameters and their current values:

```bash
grep . /sys/module/morse/parameters/* 2>/dev/null
```

**Writing to those files does nothing.** They are mode `0644` so the write
succeeds, but no parameter registers a setter callback, and the interesting
ones are read once at probe. Set them in `/etc/modprobe.d/morse.conf` and
reload:

```
options morse country=US enable_ps=2
```

```bash
sudo modprobe -r morse && sudo modprobe morse
```

| Parameter | Default here | Notes |
|---|---|---|
| `country` | `US` | Must match the AP's regulatory domain. Upstream defaults to `AU`. |
| `enable_ps` | `0` | `0` off, `1` protocol only, `2` full. Upstream defaults to `2`. See below. |
| `tx_max_power_mbm` | `0` | Max TX power in mBm; cannot exceed the chip maximum. |
| `enable_twt` | `Y` | Target Wake Time. |
| `debug_mask` | `8` | Bits 0–3: debug, info, warn, error. Raise when diagnosing association failures. |
| `enable_wiphy` | `N` | Selects FullMAC. Leave it — these packages are SoftMAC and pair with the `-rl` firmware and `hostap_s1g`. |

