
## Install php on Mac
```shell

OPENSSL_CFLAGS="-I/usr/local/opt/openssl@1.1/include" \
OPENSSL_LIBS="-L/usr/local/opt/openssl@1.1/lib -lcrypto -lssl"  \
phpbrew install -j $(nproc)   7.4.32 +gd +mysql +sqlite +bcmath +json +gettext +pdo +curl +zlib=$(brew --prefix zlib) +openssl +mbstring
```