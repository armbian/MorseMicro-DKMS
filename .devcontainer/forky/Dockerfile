FROM debian:forky

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        build-essential \
        git \
        debhelper \
        devscripts \
        dh-dkms \
        fakeroot \
        lintian \
        quilt \
        wget \
        libdbus-1-dev \
        libnl-3-dev \
        libnl-genl-3-dev \
        libnl-route-3-dev \
        libssl-dev \
        libusb-1.0-0-dev \
        pkg-config \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*
