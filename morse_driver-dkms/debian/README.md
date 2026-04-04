# morse_driver

Debian packaging for [Gateworks/morse_driver](https://github.com/Gateworks/morse_driver) (fork of [MorseMicro/morse_driver](https://github.com/MorseMicro/morse_driver)).

## How it works

This directory contains only the `debian/` packaging files. The upstream
source (`morse-driver_<version>.orig.tar.gz`) must be created with
`get-orig-source` (because upstream uses git submodules that GitHub tarballs
omit), then unpacked into the working tree with `origtargz --unpack`.

## Building

```bash
# Create the orig tarball (clones repo with submodules)
make -f debian/rules get-orig-source

# Unpack upstream source into the working tree
origtargz --unpack=yes

# Build the package
dpkg-buildpackage -us -uc
```

## Package details

- **Source package**: morse-driver
- **Binary package**: morse-driver-dkms
- **Upstream**: Gateworks fork with kernel 6.14/6.15/6.16 compatibility fixes
- **Bus transports enabled**: SDIO, SPI, USB
- **Features enabled**: user access, vendor commands, monitor mode, debugfs

The package uses DKMS to automatically build the `morse` and `dot11ah` kernel
modules for each installed kernel version. Firmware files from the
`morse-firmware` package are required for operation.
