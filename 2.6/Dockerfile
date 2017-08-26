#
# This is a multi-stage build.
# Actual build is at the very end.
#

FROM library/ubuntu:xenial AS build

ENV DEBIAN_FRONTEND noninteractive
ENV LANG C.UTF-8

RUN apt-get update && \
    apt-get install -y \
        python-software-properties \
        software-properties-common \
        apt-utils

RUN mkdir -p /build/image
WORKDIR /build
RUN apt-get download \
        mongodb \
        mongodb-server \
        mongodb-clients \
        libboost-filesystem1.58.0 \
        libboost-program-options1.58.0 \
        libboost-system1.58.0 \
        libboost-thread1.58.0 \
        libgoogle-perftools4 \
        libpcap0.8 \
        libpcre3 \
        libpcrecpp0v5 \
        libsnappy1v5 \
        libssl1.0.0 \
        libstdc++6 \
        libv8-3.14.5 \
        libyaml-cpp0.5v5 \
        libtcmalloc-minimal4 \
        libunwind8 \
        liblzma5
RUN for file in *.deb; do dpkg-deb -x ${file} image/; done

WORKDIR /build/image
RUN mkdir -p \
        var/log/mongodb \
        var/lib/mongodb && \
    ln -s /dev/stdout var/log/mongodb/mongodb.log && \
    rm -rf \
        etc/init \
        etc/init.d \
        etc/logrotate.d \
        lib/systemd \
        usr/share && \
    sed -i -r \
        's,^ *bind_ip *=.*,bind_ip = 0.0.0.0,g' \
        etc/mongodb.conf

FROM clover/base

WORKDIR /
COPY --from=build /build/image /

VOLUME ["/var/lib/mongodb"]

CMD ["mongod", "-f", "/etc/mongodb.conf"]

EXPOSE 27017
