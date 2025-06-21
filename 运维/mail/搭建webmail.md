



```
sudo yum install postfix dovecot dovecot-mysql cyrus-sasl opendkim -y
```

解析域名

```
A记录    mail.xxxx.cn 		 → 服务器IP
MX记录   @                     → mail.xxxx.cn (优先级10)
TXT记录  @                     → "v=spf1 mx ~all"
TXT记录  _dmarc                → "v=DMARC1; p=none; rua=mailto:admin@xxxx.cn"
```

证书

```
sudo certbot certonly --standalone -d mail.xxxx.com -d xxxx.com
```

/etc/postfix/main.cf配置

```
# 基本域名配置
myhostname = mail.XXXX.cn  # 邮件服务器完整主机名（需有DNS A记录）
mydomain = XXXX.cn         # 主域名

# 接收邮件配置
inet_interfaces = all               # 监听所有网络接口（生产环境建议指定IP）
mydestination = $myhostname, localhost.$mydomain, localhost, $mydomain

# 发件人域名标识
myorigin = $mydomain               # 外发邮件显示为 user@XXXX.cn

# TLS加密配置
smtpd_tls_cert_file = /www/server/panel/vhost/cert/mail.xxx.cn/fullchain.pem
smtpd_tls_key_file = /www/server/panel/vhost/cert/mail.xxx.cn/privkey.pem
smtpd_use_tls = yes
smtpd_tls_session_cache_database = btree:${data_directory}/smtpd_scache
smtp_tls_session_cache_database = btree:${data_directory}/smtp_scache
```

查看运行的端口

```
sudo netstat -tuln | grep :25
```

/etc/postfix/master.cf放开注释

```
smtps     inet  n       -       n       -       -       smtpd
  -o syslog_name=postfix/smtps
  -o smtpd_tls_wrappermode=yes
  -o smtpd_sasl_auth_enable=yes
  -o smtpd_reject_unlisted_recipient=no
  -o smtpd_client_restrictions=$mua_client_restrictions
  -o smtpd_helo_restrictions=$mua_helo_restrictions
  -o smtpd_sender_restrictions=$mua_sender_restrictions
  -o smtpd_recipient_restrictions=permit_sasl_authenticated,reject
  -o milter_macro_daemon_name=ORIGINATING
```

/etc/dovecot/dovecot.conf