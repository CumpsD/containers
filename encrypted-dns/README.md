# encrypted-dns

Forked from [DNSCrypt/dnscrypt-server-docker](https://github.com/DNSCrypt/dnscrypt-server-docker/blob/master/Dockerfile) without `unbound`.

## Build

```bash
docker build . --file Dockerfile --tag ghcr.io/cumpsd/encrypted-server:latest
```

## Publish

```bash
docker push ghcr.io/cumpsd/encrypted-server:latest
```

## Init

```bash
docker run --name=dnscrypt-server -p 443:443/udp -p 443:443/tcp --restart=unless-stopped encrypted-server:latest init -N core.cumps.be -E '10.0.50.50:443' -D '10.0.50.50:53'
```
