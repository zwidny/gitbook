# OpenSSL

## 快速生成key和证书
```shell
openssl genrsa -out foobar.key 2048
openssl req -new -key foobar.key -out foobar.csr
openssl x509 -req -days 365 -in foobar.csr -signkey foobar.key -out foobar.crt
```