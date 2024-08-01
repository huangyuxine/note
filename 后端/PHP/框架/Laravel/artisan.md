## 基本命令

### 显示应用程序基本信息

`--json`以json格式显示

```
php artisan about
```

### 清除缓存

```
php artisan optimize:clear
```

* `php artisan clear-compiled` bootstrap/cache/services.php bootstrap/cache/packages.php
* `php artisan config:clear` 引导程序/缓存/config.php
* `php artisan route:clear` bootstrap/cache/routes.php
* `php artisan view:clear` 存储/框架/视图
* `php artisan cache:clear` 存储/框架/缓存/数据

### 添加缓存

`php artisan optimize`将执行以下命令。

```
php artisan config:cache
php artisan route:cache
```

以下不会被执行，所以单独运行。

```
php artisan view:cache
```

### 关闭程序

```
php artisan down
```

### 开启程序

```
php artisan up
```

### 查看当前环境

```
php artisan env
```

### 鼓舞人心的话

```
php artisan inspire
```

### 在PHP开发服务器上提供应用程序

```
php artisan serve
```

### 运行测试

```
php artisan test
```

### 与应用程序交互

```
php artisan tinker
```

## 控制器

资源型

```
php artisan make:controller IdeaController --resource
```

api型

```
php artisan make:controller IdeaController --api
```

## 数据填充

文件生成

```
php artisan make:seed UsersTableSeeder
```

执行

```
php artisan db:seed
```

清空默认数据并填充

```
php artisan migrate:refresh --seed
```

## 数据迁移

创建迁移文件

```
php artisan make:migration create_ideas_table
```

执行

```
php artisan migrate
```

重建

```
php artisan migrate:refresh
```

执行最后两个迁移操作

```
php artisan migrate:refresh --step=2
```

## 模型

创建模型、控制器和迁移

```
php artisan make:model Comment -m -c
```

## 用户策略

```
php artisan make:policy UserPolicy --model=User
```

## 表单验证

```
php artisan make:request UserRequest
```

## 创建任务

```
php artisan make:job TranslateSlug
```
