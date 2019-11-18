* [一、准备工作](#准备工作)
* [二、安装步骤](#安装步骤)
* [三、启动EasySwoole](#启动EasySwoole)

EasySwoole官方文档，项目使用3.x版本

# 一、准备工作
## 1.1 基础运行环境

* 保证 PHP 版本大于等于 7.1
* 保证 Swoole 拓展版本大于等于 4.4.0
* 需要 pcntl 拓展的任意版本
* 使用 Linux / FreeBSD / MacOS 这三类操作系统
* 使用 Composer 作为依赖管理工具


在配置好后，可以通过如下命令检查PHP版本：
```sh
php -v
```

样例输出：
```sh
PHP 7.2.8 (cli) (built: Jul 24 2018 10:15:41) ( NTS )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.2.0, Copyright (c) 1998-2018 Zend Technologies
```

## 1.2 Nginx做反向代理

编辑`NGINX_CONFIG_DIR/nginx.conf`，增加server。
```conf
  server {
        listen 80;
        server_name libing.dev.growthcloud.info;
        root /home/{USER}/dev/GrowthCloud/Public;

        location / {
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header Host $host;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header Connection "keep-alive";
            proxy_pass http://127.0.0.1:[port];
        }
  }
```
端口号[port]与项目dev.php配置保持一致
