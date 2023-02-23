# Docker Mount vs Volume
默认容器的数据是保存在容器的可读写层，当容器被删除时其上的数据将会丢失，所以为了实现数据的持久性则需要选择一种数据持久技术来保存数据，当前有以下几种方式：

1. Volumes
2. Bind mounts
3. tmpfs
