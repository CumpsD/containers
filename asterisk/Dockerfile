FROM debian:bullseye-slim

LABEL maintainer="David Cumps <david@cumps.be>"

ENV ASTERISK_VERSION 20-current
ENV OPUS_CODEC       asterisk-20.0/x86-64/codec_opus-20.0_current-x86_64

RUN useradd --system asterisk

RUN DEBIAN_FRONTEND=noninteractive \
  apt-get update -qq

RUN DEBIAN_FRONTEND=noninteractive \
  apt-get install --yes --no-install-recommends --no-install-suggests \
    autoconf \
    binutils-dev \
    build-essential \
    ca-certificates \
    curl \
    dos2unix \
    file \
    gzip \
    libcurl4-openssl-dev \
    libedit-dev \
    libgsm1-dev \
    libical-dev \
    libiksemel-dev \
    libjansson-dev \
    libjansson4 \
    libneon27-dev \
    libogg-dev \
    libpopt-dev \
    libresample1-dev \
    libspandsp-dev \
    libspeex-dev \
    libspeexdsp-dev \
    libsqlite3-dev \
    libsrtp2-dev \
    libssl-dev \
    libvorbis-dev \
    libxml2-dev \
    libxslt1-dev \
    msmtp \
    msmtp-mta \
    ntp \
    odbc-postgresql \
    odbcinst \
    portaudio19-dev \
    procps \
    unixodbc \
    unixodbc-dev \
    uuid \
    uuid-dev \
    xmlstarlet

RUN DEBIAN_FRONTEND=noninteractive \
  apt-get purge --yes --auto-remove

RUN rm -rf /var/lib/apt/lists/*

RUN mkdir -p /usr/src/asterisk && \
  cd /usr/src/asterisk && \
  # Download asterisk source
  curl -vsL http://downloads.asterisk.org/pub/telephony/asterisk/releases/asterisk-${ASTERISK_VERSION}.tar.gz | tar --strip-components 1 -xz || \
  curl -vsL http://downloads.asterisk.org/pub/telephony/asterisk/asterisk-${ASTERISK_VERSION}.tar.gz | tar --strip-components 1 -xz || \
  curl -vsL http://downloads.asterisk.org/pub/telephony/asterisk/old-releases/asterisk-${ASTERISK_VERSION}.tar.gz | tar --strip-components 1 -xz && \
  # Configure asterisk
  ./configure \
    --with-resample \
    --with-pjproject-bundled \
    --with-jansson-bundled && \
  # Prepare menuselect
  make menuselect/menuselect menuselect-tree menuselect.makeopts && \
  # Tweak asterisk
  menuselect/menuselect \
    --disable BUILD_NATIVE \
    --disable-category MENUSELECT_CORE_SOUNDS \
    --disable-category MENUSELECT_MOH \
    --disable-category MENUSELECT_EXTRA_SOUNDS \
    --enable BETTER_BACKTRACES \
    --enable chan_ooh323  \
    --enable chan_motif \
    --enable format_ogg_speex \
    --enable format_vox \
    --enable res_calendar \
    --enable res_calendar_caldav \
    --enable res_calendar_icalendar \
    --enable res_xmpp menuselect.makeopts && \
  # Compile asterisk
  make all && \
  make install && \
  make samples && \
  make clean

 # set runuser and rungroup
RUN sed -i -E 's/^;(run)(user|group)/\1\2/' /etc/asterisk/asterisk.conf

COPY codec_silk-20.0_1.0.3-x86_64/codec_silk.so               /usr/lib/asterisk/modules/
COPY codec_opus-20.0_1.3.0-x86_64/codec_opus.so               /usr/lib/asterisk/modules/
COPY codec_opus-20.0_1.3.0-x86_64/format_ogg_opus.so          /usr/lib/asterisk/modules/
COPY codec_opus-20.0_1.3.0-x86_64/codec_opus_config-en_US.xml /var/lib/asterisk/documentation/thirdparty

RUN mkdir -p /etc/asterisk/ /var/spool/asterisk/fax

RUN chown -R asterisk:asterisk /etc/asterisk /var/*/asterisk /usr/*/asterisk
RUN chmod -R 750 /var/spool/asterisk

RUN cd / && rm -rf /usr/src/asterisk /usr/src/codecs

EXPOSE 5060/udp 5060/tcp

VOLUME /etc/asterisk \
       /usr/lib/asterisk \
       /var/lib/asterisk \
       /var/lib/asterisk/sounds \
       /var/lib/asterisk/keys \
       /var/lib/asterisk/phoneprov \
       /var/spool/asterisk \
       /var/log/asterisk

COPY docker-entrypoint.sh /
RUN dos2unix /docker-entrypoint.sh

ENTRYPOINT ["/docker-entrypoint.sh"]

CMD ["/usr/sbin/asterisk", "-vvvdddf", "-T", "-W", "-U", "asterisk", "-p"]
