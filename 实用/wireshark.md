---
description: mac电脑
---

# wireshark抓包

## iphone抓包

1. 电脑上**必须**安装xcode、wireshark
2. 电脑进入安全模式，关机-长按电源进入安全模式-安全工具-设置低安全模式
3. 手机插在mac上，打开mac自带音乐软件，找到uuid并复制
4. 打开终端，执行以下命令

创建虚拟网卡

```
rvictl -s your uuid
```

移除

```
rvictl -x your uuid
```

5. 打开wireshark，选择rvi0接口，即可监听流量

```
sudo /Applications/Wireshark.app/Contents/MacOS/Wireshark
```

## 数据包过滤

过滤ip地址和协议

```
ip.addr == 192.168.1.4 && http
```

过滤源地址和目的地址

```
ip.src == 192.168.1.4 && ip.dst == 120.232.73.99
```

过滤源地址和目的域名

```
ip.src == 192.168.1.4 && http.host == www.yundaex.com
```
