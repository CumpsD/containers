# asterisk

## Build

```bash
docker build . --file Dockerfile --tag ghcr.io/cumpsd/asterisk:20.3.0 --build-arg ASTERISK_VERSION=20.3.0
```

## Publish

```bash
docker tag ghcr.io/cumpsd/asterisk:20.3.0 ghcr.io/cumpsd/asterisk:latest
docker push ghcr.io/cumpsd/asterisk:latest
```

## Test

```bash
docker run --rm -it ghcr.io/cumpsd/asterisk:latest /usr/sbin/asterisk -V

docker run --rm -it --net=host --name asterisk \
  -v (pwd)/docker/cdr:/var/log/asterisk/cdr-custom/ \
  -v (pwd)/docker/etc/asterisk:/etc/asterisk/ \
  -v (pwd)/docker/lib/moh:/var/lib/asterisk/moh/ \
  -v (pwd)/docker/lib/sounds:/var/lib/asterisk/sounds/ \
  -v (pwd)/docker/log:/var/log/asterisk/ \
  -v (pwd)/docker/spool:/var/spool/asterisk/ \
  -v (pwd)/docker/etc/msmtp/msmtprc:/etc/msmtprc \
  ghcr.io/cumpsd/asterisk:latest
```
