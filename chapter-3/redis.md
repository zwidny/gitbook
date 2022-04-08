# 在Docker模式下， 为redis增加password授权


```shell
docker run --name some-redis -d -e REDIS_PASSWORD=foobared redis sh -c 'exec redis-server --requirepass "$REDIS_PASSWORD"'
```