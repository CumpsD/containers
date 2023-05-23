# asterisk

## Build

```bash
docker build . --file Dockerfile --tag ghcr.io/cumpsd/asterisk:20.2.1 --build-arg VERSION=20.2.1
```

## Publish

```bash
docker tag ghcr.io/cumpsd/asterisk:20.2.1 ghcr.io/cumpsd/asterisk:latest
docker push ghcr.io/cumpsd/asterisk:latest
```

## Test

```bash
docker run --rm -it ghcr.io/cumpsd/asterisk:latest -V

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
