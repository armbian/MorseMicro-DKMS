# mm6108-driver-dkms

Debian packaging for [MorseMicro/morse_driver](https://github.com/MorseMicro/morse_driver).

## How it works

This directory contains only the `debian/` packaging files. The upstream
source (`mm6108-driver_<version>.orig.tar.gz`) must be created with
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

- **Source package**: mm6108-driver
- **Binary package**: mm6108-driver-dkms
- **Upstream**: MorseMicro official driver repository
- **Upstream tags**: `mm6108-<version>` (upstream tags the MM6108 and
  MM8108 series separately; this package follows the MM6108 series and
  strips the `mm6108-` prefix from the Debian upstream version)
- **Bus transports enabled**: SDIO, SPI, USB
- **Features enabled**: user access, vendor commands, monitor mode, debugfs

The package uses DKMS to automatically build the `morse` and `dot11ah` kernel
modules for each installed kernel version. Firmware files from the
`mm6108-firmware` package are required for operation.
