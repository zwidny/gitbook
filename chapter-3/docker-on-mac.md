# Enable Docker Remote API

## 默认情况

默认情况下可以修改`/etc/docker/daemon.json`文件, (增加/修改)hosts, 之后重启dockerd
```
{
  ...
  "hosts": ["tcp://0.0.0.0:2375", "unix:///var/run/docker.sock"]
  ...
}
```
## Mac
在Mac安装Docker时，由于Mac安全限制， 默认时无法开发TCP端口的， 所以即便在daemon.conf设置
了host依然不会生效

### 解决方法
#### socat

Socat 是 Linux 下的一个多功能的网络工具. Socat 的主要特点就是在两个数据流之间建立通道，且支持众多协议和链接方式。

具体操作如下
```shell
socat TCP-LISTEN:2375,reuseaddr,fork UNIX-CONNECT:/var/run/docker.sock &
```

