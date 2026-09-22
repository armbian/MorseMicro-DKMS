# mm8108-driver-dkms

Debian packaging for [MorseMicro/morse_driver](https://github.com/MorseMicro/morse_driver).

## How it works

This directory contains only the `debian/` packaging files. The upstream
source (`mm8108-driver_<version>.orig.tar.gz`) must be created with
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

- **Source package**: mm8108-driver
- **Binary package**: mm8108-driver-dkms
- **Upstream**: MorseMicro official driver repository
- **Upstream tags**: `mm8108-<version>` (upstream tags the MM6108 and
  MM8108 series separately; this package follows the MM8108 series and
  strips the `mm8108-` prefix from the Debian upstream version)
- **Bus transports enabled**: SDIO, SPI, USB
- **Features enabled**: user access, vendor commands, monitor mode, debugfs
- **Build-time defaults**: 4-byte SDIO bulk alignment, country `US`, power
  save disabled (override the latter two at load time with the `country` and
  `enable_ps` module parameters)

The package uses DKMS to automatically build the `morse` and `dot11ah` kernel
modules for each installed kernel version. Firmware files from the
`mm8108-firmware` package are required for operation.
