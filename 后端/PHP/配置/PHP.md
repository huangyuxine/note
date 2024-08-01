# 配置

php.ini

```ini
;决定 PHP 是否标示它装在服务器上, 需要关闭
expose_php = Off
;脚本的最大执行时间
max_execution_time = 30
;一个脚本最大可使用的内存总量
memory_limit = 128M
;所有错误级别 E_ALL，但排除了 E_NOTICE 和 E_STRICT
error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT
;显示错误关闭
display_errors = Off
;是否显示在 PHP 初始化过程中发生的错误。这些错误与脚本执行期间发生的错误不同，它们是在 PHP 启动时加载和解析配置文件时产生的
display_startup_errors = Off
;错误日志
log_errors = On;
;PHP 将接受的 POST 数据最大大小。
post_max_size = 8M ;
;HTTP方式文件上传
file_uploads = On
;上传的最大许可
upload_max_filesize = 2M ;
;为了安全的原因让特定函数失效
disable_functions = 
[opcache]
;启用OPcache，加速代码执行。
opcache.enable = 1
opcache.enable_cli = 1
;用于存储预留字符串的内存大小
opcache.interned_strings_buffer = 8
;可用的共享内存大小
opcache.memory_consumption = 128
```

