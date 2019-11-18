* [一、准备工作](#%E4%B8%80%E5%87%86%E5%A4%87%E5%B7%A5%E4%BD%9C)
* [二、安装步骤](#%E4%BA%8C%E5%AE%89%E8%A3%85%E6%AD%A5%E9%AA%A4)
* [三、启动EasySwoole](#%E4%B8%89%E5%90%AF%E5%8A%A8EasySwoole)

EasySwoole官方文档: https://www.easyswoole.com/Preface/introduction.html
项目使用3.x版本

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

# 二、安装步骤
## 2.1 GIT同步代码
```sh
cd ~
git clone https://github.com/abc/apollo-build-scripts -o GrowthCloud
```



修改dev.php开发配置信息：
```sh
cd GrowthCloud
vi dev.php
```
dev.php内容：
```php
<?php

return [
    'SERVER_NAME'   => "GrowthCloud",//服务名
    'MAIN_SERVER'   => [
        'LISTEN_ADDRESS' => '0.0.0.0',//监听地址
        'PORT'           => 9501,//监听端口
        'SERVER_TYPE'    => EASYSWOOLE_WEB_SERVER, //可选为 EASYSWOOLE_SERVER  EASYSWOOLE_WEB_SERVER EASYSWOOLE_WEB_SOCKET_SERVER EASYSWOOLE_REDIS_SERVER
        'SOCK_TYPE'      => SWOOLE_TCP,//该配置项当为SERVER_TYPE值为TYPE_SERVER时有效
        'RUN_MODEL'      => SWOOLE_PROCESS,// 默认Server的运行模式
        'SETTING'        => [// Swoole Server的运行配置（ 完整配置可见[Swoole文档](https://wiki.swoole.com/wiki/page/274.html) ）
            'worker_num'       => 8,//运行的  worker进程数量
            'reload_async' => true,//设置异步重启开关。设置为true时，将启用异步安全重启特性，Worker进程会等待异步事件完成后再退出。
            // 'task_enable_coroutine' => true,//开启后自动在onTask回调中创建协程
            'max_wait_time'=>3
        ],
        'TASK'=>[
            'workerNum'=>4,
            'maxRunningNum'=>128,
            'timeout'=>15
        ]
    ],
    'TEMP_DIR'      => null,//临时文件存放的目录
    'LOG_DIR'       => null,//日志文件存放的目录

    'MYSQL' => [
        'host'          => '127.0.0.1',
        'port'          => '3306',
        'user'          => 'root',
        'timeout'       => '5',
        'charset'       => 'utf8mb4',
        'password'      => '111111',
        'database'      => 'chat',
        'POOL_MAX_NUM'  => '20',
        'POOL_TIME_OUT' => '0.1',
    ],
];
```
其中SERVER_NAME及监听端口号修改为自己独立使用的，方便开发调试

## 2.2 Nginx做反向代理

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

## 2.3 数据库操作

Easyswoole提供的一个全新协程安全的ORM封装。

ORM数据库操作文档： https://www.easyswoole.com/Components/Orm/query.html

# 三、启动EasySwoole
## 3.1 确保端口未被占用

```sh
cd /home/{USER}/dev/GrowthCloud/

php easyswoole start d
```
浏览器直接打开 http://172.31.3.232:[port]/api/user/

页面输出： hello user index

表示EasySwoole服务正常运行
