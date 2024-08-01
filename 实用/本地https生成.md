# 本地生成https证书

```bash
#生成秘钥key,两次密码
openssl genrsa -des3 -out server.key 2048
#去除输入密码
openssl rsa -in server.key -out server.key
#创建服务器证书的申请文件
openssl req -new -key server.key -out server.csr
#创建CA证书
openssl req -new -x509 -key server.key -out ca.crt -days 3650
#创建自当前日期起有效期为期十年的服务器证书server.crt：
openssl x509 -req -days 3650 -in server.csr -CA ca.crt -CAkey server.key -CAcreateserial -out server.crt
```

windows需要安装openssl\
[http://slproweb.com/products/Win32OpenSSL.html](http://slproweb.com/products/Win32OpenSSL.html)
