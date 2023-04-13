FROM ubuntu:22.04
LABEL maintainer="Frank Denis"
SHELL ["/bin/sh", "-x", "-c"]
ENV SERIAL 11

ENV CFLAGS=-Ofast
ENV BUILD_DEPS   curl make build-essential git libevent-dev libexpat1-dev autoconf file libssl-dev byacc
ENV RUNTIME_DEPS bash util-linux coreutils findutils grep libssl3 ldnsutils libevent-2.1 expat ca-certificates runit runit-helper jed

RUN apt-get update && apt-get -qy dist-upgrade && apt-get -qy clean && \
    apt-get install -qy --no-install-recommends $RUNTIME_DEPS && \
    rm -fr /tmp/* /var/tmp/* /var/cache/apt/* /var/lib/apt/lists/* /var/log/apt/* /var/log/*.log

RUN update-ca-certificates 2> /dev/null || true

WORKDIR /tmp

ENV RUSTFLAGS "-C link-arg=-s"

RUN apt-get update && apt-get install -qy --no-install-recommends $BUILD_DEPS && \
    curl -sSf https://sh.rustup.rs | bash -s -- -y --default-toolchain stable && \
    export PATH="$HOME/.cargo/bin:$PATH" && \
    echo "Compiling doh-proxy" && \
    cargo install doh-proxy && \
    mkdir -p /opt/doh-proxy/sbin && \
    mv ~/.cargo/bin/doh-proxy /opt/doh-proxy/sbin/ && \
    strip --strip-all /opt/doh-proxy/sbin/doh-proxy && \
    apt-get -qy purge $BUILD_DEPS && apt-get -qy autoremove && \
    rm -fr ~/.cargo ~/.rustup && \
    rm -fr /tmp/* /var/tmp/* /var/cache/apt/* /var/lib/apt/lists/* /var/log/apt/* /var/log/*.log

RUN groupadd _doh-proxy && \
    mkdir -p /opt/doh-proxy/empty && \
    useradd -g _doh-proxy -s /etc -d /opt/doh-proxy/empty _doh-proxy

EXPOSE 3000

ENTRYPOINT ["/opt/doh-proxy/sbin/doh-proxy"]
