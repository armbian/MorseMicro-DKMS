# mm6108-firmware

Debian packaging for [MorseMicro/morse-firmware](https://github.com/MorseMicro/morse-firmware).

## How it works

This directory contains only the `debian/` packaging files. The upstream
source (`mm6108-firmware_<version>.orig.tar.gz`) must be created with
`get-orig-source` (which downloads the pinned upstream release commit),
then unpacked into the working tree with `origtargz --unpack`.

## Building

```bash
# Create the orig tarball (downloads the pinned upstream commit)
make -f debian/rules get-orig-source

# Unpack upstream source into the working tree
origtargz --unpack=yes

# Build the package
dpkg-buildpackage -us -uc
```

## Package details

- **Source package**: mm6108-firmware
- **Binary package**: mm6108-firmware
- **Architecture**: all (arch-independent firmware blobs)
- **Chips supported**: MM6108
- **Upstream tags**: `mm6108-<version>` (upstream tags the MM6108 and
  MM8108 series separately, both pointing at the same firmware release
  commit). The 2.1.x tags were never pushed, so `debian/rules` pins the
  release commit; switch `UPSTREAM_COMMIT` back to a tag once upstream
  publishes one.
- **BCF vendors included**: Morse Micro, AzureWave, NetPrisma, Quectel

Firmware and Board Configuration Files (BCFs) are installed to
`/lib/firmware/morse/` for use by the `morse` kernel driver.
