# morse-firmware

Debian packaging for [MorseMicro/morse-firmware](https://github.com/MorseMicro/morse-firmware).

## How it works

This directory contains only the `debian/` packaging files. The upstream
source (`morse-firmware_<version>.orig.tar.gz`) must be created with
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

- **Source package**: morse-firmware
- **Binary package**: morse-firmware
- **Architecture**: all (arch-independent firmware blobs)
- **Chips supported**: MM6108, MM8108
- **BCF vendors included**: Morse Micro, AzureWave, NetPrisma, Quectel

Firmware and Board Configuration Files (BCFs) are installed to
`/lib/firmware/morse/` for use by the `morse` kernel driver.
