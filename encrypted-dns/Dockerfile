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
    echo "Compiling encrypted-dns" && \
    cargo install encrypted-dns && \
    mkdir -p /opt/encrypted-dns/sbin && \
    mv ~/.cargo/bin/encrypted-dns /opt/encrypted-dns/sbin/ && \
    strip --strip-all /opt/encrypted-dns/sbin/encrypted-dns && \
    apt-get -qy purge $BUILD_DEPS && apt-get -qy autoremove && \
    rm -fr ~/.cargo ~/.rustup && \
    rm -fr /tmp/* /var/tmp/* /var/cache/apt/* /var/lib/apt/lists/* /var/log/apt/* /var/log/*.log

RUN groupadd _encrypted-dns && \
    mkdir -p /opt/encrypted-dns/empty && \
    useradd -g _encrypted-dns -s /etc -d /opt/encrypted-dns/empty _encrypted-dns && \
    mkdir -m 700 -p /opt/encrypted-dns/etc/keys && \
    mkdir -m 700 -p /opt/encrypted-dns/etc/lists && \
    chown _encrypted-dns:_encrypted-dns /opt/encrypted-dns/etc/keys

RUN mkdir -p \
    /var/svc/encrypted-dns \
    /var/svc/watchdog

COPY encrypted-dns.toml.in /opt/encrypted-dns/etc/
COPY undelegated.txt /opt/encrypted-dns/etc/

COPY entrypoint.sh /

COPY encrypted-dns.sh /var/svc/encrypted-dns/run

COPY watchdog.sh /var/svc/watchdog/run

RUN ln -sf /opt/encrypted-dns/etc/keys/encrypted-dns.toml /opt/encrypted-dns/etc/encrypted-dns.toml

VOLUME ["/opt/encrypted-dns/etc/keys"]

EXPOSE 443/udp 443/tcp 9100/tcp

CMD ["/entrypoint.sh", "start"]

ENTRYPOINT ["/entrypoint.sh"]
