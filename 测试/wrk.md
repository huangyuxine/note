# 介绍

wrk一款现代 HTTP 基准测试工具

# 安装

MacOS安装

```
brew install wrk
```

# 使用

运行 30 秒的基准测试，使用 12 个线程，并保持 400 个 HTTP 连接打开

```
wrk -t12 -c400 -d30s http://127.0.0.1:8080/index.html
```