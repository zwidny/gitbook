# 配置docker 使其可以远程访问

## 镜像加速

编辑dockerd配置文件
vi /etc/docker/daemon.conf
```json
{
  "runtimes": {
    "nvidia": {
      "path": "nvidia-container-runtime",
      "runtimeArgs": []
    }
  },
  "registry-mirrors":["https://22e5a8dbd7d44606bc21701144789d02.mirror.swr.myhuaweicloud.com", "https://almtd3fa.mirror.aliyuncs.com"]
}
```

## 暴露端口

编辑systemd service, 修改ExecStart
vi /lib/systemd/system/docker.service
```text

[Service]
Type=notify
# the default is not to use systemd for cgroups because the delegate issues still
# exists and systemd currently does not support the cgroup feature set required
# for containers run by docker
ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2375 -H fd:// --containerd=/run/containerd/containerd.sock
```