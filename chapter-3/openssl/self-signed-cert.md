# 为IP地址生成签名SSL/TLS证书

## Step 1. 创建证书配置文件
```text ssl.cnf
[req]
default_bits = 4096
default_md = sha256
distinguished_name = req_distinguished_name
x509_extensions = v3_req
prompt = no
[req_distinguished_name]
C = CN
ST = Beijing
L = Beijing
O = Company
OU = Division
CN = 10.1.3.15
[v3_req]
keyUsage = keyEncipherment, dataEncipherment
extendedKeyUsage = serverAuth
subjectAltName = @alt_names
[alt_names]
IP.1 = 10.1.3.15
```
## Step 2. 使用生成证书和私钥
```shell
openssl req -new -nodes -x509 -days 365 -keyout certs/domain.key  -out certs/domain.crt -config ssl.cnf
```
## Step 3. 验证证书包含 IP SAN
```shell
openssl x509 -in certs/domain.crt -noout -text
```
正确的话会包含如下信息
```text
X509v3 Subject Alternative Name:
    IP Address:10.1.3.15
```
## Step 4. 安装证书到服务器