```shell
#!/bin/bash

echo ""
echo "============= 开始部署 ============="
echo ""

# 项目存放目录
WEB_PATH='/www/wwwroot/karl.3yconsulting.cn/dorset-activity-admin'

GIT_URL='git@gitee.com:huang-yuxin/dorset-activity-admin.git'

# 分支名称
GIT_BRANCH='master'

# 日志文件路径
LOG_PATH='/tmp/webhook_deploy.log'
date "+%Y-%m-%d %H:%M:%S" >> $LOG_PATH

# 进入项目目录
if [ ! -d $WEB_PATH ]; then
    echo "项目目录不存在，开始克隆仓库..." >> $LOG_PATH
    git clone -b $GIT_BRANCH $GIT_URL $WEB_PATH
    cd $WEB_PATH
else
    cd $WEB_PATH
    echo "拉取最新代码..." >> $LOG_PATH
    git pull origin $GIT_BRANCH
fi


echo "部署完成" >> $LOG_PATH
echo "" >> $LOG_PATH

# 输出日志最后10行，方便宝塔查看
tail -n 10 $LOG_PATH
```

