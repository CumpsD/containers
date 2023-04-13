# doh-proxy

## Build

```bash
docker build . --file Dockerfile --tag ghcr.io/cumpsd/doh-proxy:latest
```

## Publish

```bash
docker push ghcr.io/cumpsd/doh-proxy:latest
```

## Test

```bash
docker run --rm -it ghcr.io/cumpsd/doh-proxy:latest -V
docker run --rm -it ghcr.io/cumpsd/doh-proxy:latest -h
```
