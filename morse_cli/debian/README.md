# deb-morse_cli

Debian packaging for [MorseMicro/morse_cli](https://github.com/MorseMicro/morse_cli).

## How it works

This directory contains only the `debian/` packaging files (which become
`.debian.tar.xz` in the source package). The upstream source
(`morse-cli_<version>.orig.tar.gz`) is fetched separately via `uscan` and
merged at build time using `origtargz --unpack`, following the standard
Debian 3.0 (quilt) source format.

`origtargz` removes everything except `debian/` and VCS files (`.git`,
`.gitignore`, etc.), then extracts the orig tarball in-place. The
`.gitignore` uses a whitelist pattern so the unpacked upstream source
stays untracked.

## Building

```bash
# Fetch upstream tarball and unpack it into the working tree
origtargz --unpack=yes

# Build the package
dpkg-buildpackage -us -uc
```

Or as a one-liner:

```bash
origtargz --unpack=yes && dpkg-buildpackage -us -uc
```

## Package details

- **Source package**: morse-cli
- **Binary package**: morse-cli
- **Upstream binary**: morse_cli
- **Transports enabled**: NL80211, UART SLIP, TCP SLIP
