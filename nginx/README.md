# nginx with headers-more

See https://github.com/nginxinc/docker-nginx/tree/master/modules

## Build

```bash
docker build --build-arg ENABLED_MODULES="headers-more" -t ghcr.io/cumpsd/nginx:1.25.1 .
```

## Publish

```bash
docker push ghcr.io/cumpsd/nginx:1.25.1
```
