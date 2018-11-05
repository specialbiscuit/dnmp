基于 CentOS 的 Docker LNMP 集成开发环境
======

### 前言

制作 Docker 选择 CentOS   

为什么 [Docker中选择CentOS还是Ubuntu/Debian][2]，在知乎上有文章介绍;而[CentOS][1] 更加适合商业服务器，原因如下：  

CentOS（可以说）更稳定以及更安全。由于 CentOS 的更新频率较低，这意味着软件测试的时间更长，并且只有真正稳定的版本才会得到发布。如果你使用 CentOS，你不会因新的有 bug 的应用程序版本而遇到任何稳定性问题，因为你不会得到那个新的有 bug 的版本。

说明：
	使用Docker构建的开发环境：Nginx、MySQL、Redis、PHP5.6、PHP7.1、PHP7.2、memcached、支持xdebug、Composer

### 快速部署 Docker CentOS LNMP 


#### 部署 LNMP  
开发环境的结构，如下图：
![Demo Image](./dnmp.png)

说明：
	使用Docker构建的开发环境：Nginx、MySQL、Redis、PHP5.6、PHP7.1、PHP7.2、Memcached 支持xdebug、Composer


### 快速使用 

* 根据系统Linux,MacOS 安装 `docker` and `docker-compose`;
* [镜像加速器][1] 使用阿里云

Docker默认从DockerHub仓库下载镜像，更换途径，使用阿里云的加速Docker仓库,注册一个阿里云账号，然后访问阿里云的Docker镜像仓库，能找到加速器地址。

https://cr.console.aliyun.com/cn-beijing/mirrors

根据镜像加速器 选项卡配置 加速器 
例如我的配置如下：

1. 加速器
	使用加速器可以提升获取Docker官方镜像的速度

	加速器地址：https://i37cj3ld.mirror.aliyuncs.com
2. 操作文档 Mac 版本
	
	 * 安装／升级 Docker 客户端
		对于10.10.3以下的用户 推荐使用 Docker Toolbox

		Mac安装文件：http://mirrors.aliyun.com/docker-toolbox/mac/docker-toolbox/

		对于10.10.3以上的用户 推荐使用 Docker for Mac

		Mac 安装文件：http://mirrors.aliyun.com/docker-toolbox/mac/docker-for-mac/

	* 配置镜像加速器  
		针对安装了 Docker Toolbox 的用户，您可以参考以下配置步骤：
		创建一台安装有 Docker 环境的 Linux 虚拟机，指定机器名称为 default ，同时配置 Docker 加速器地址。

		docker-machine create --engine-registry-mirror=https://i37cj3ld.mirror.aliyuncs.com -d virtualbox default
		查看机器的环境配置，并配置到本地，并通过 Docker 客户端访问 Docker 服务。

			docker-machine env default
			eval "$(docker-machine env default)"
			docker info
		
		针对安装了 Docker for Mac 的用户，您可以参考以下配置步骤：
		右键点击桌面顶栏的 docker 图标，选择 Preferences ，在 Daemon 标签（Docker 17.03 之前版本为 Advanced 标签）下的 Registry mirrors 列表中将

		https://i37cj3ld.mirror.aliyuncs.com 加到 "registry-mirrors" 的数组里，点击 Apply & Restart 按钮，等待 Docker 重启并应用配置的镜像加速器。
	





    
```
$docker load -i dnmp.tar 
$dcoker images 
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
dnmp_nginx          latest              9e119ed7687e        4 hours ago         17.7MB
dnmp_php-7.2        latest              ba8db3d065ec        4 hours ago         806MB
dnmp_php-7.1        latest              b1a222905b4c        5 hours ago         890MB
dnmp_php-5.6        latest              2b10917e52b8        5 hours ago         905MB
dnmp_memcached      latest              5fa4dd1e161e        5 hours ago         15.6MB
dnmp_redis          latest              5100ff157ed1        5 hours ago         94.9MB
dnmp_mysql          latest              e3f31f4131c8        5 hours ago         372MB

```

可以看到多个image文件已经load进入Docker

* 启动docker容器:

```
 $docker-compose up -d
 $docker ps 
    
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                                      NAMES
04f0bf45a8dc        dnmp_nginx          "nginx -g 'daemon ofΒ   2 minutes ago       Up 3 minutes        0.0.0.0:80->80/tcp, 0.0.0.0:443->443/tcp   dnmp_nginx_1
f504799df2a7        dnmp_php-5.6        "/usr/bin/supervisord"   2 minutes ago       Up 3 minutes        9000/tcp                                   dnmp_php-5.6_1
754cf783cd67        dnmp_php-7.2        "/usr/bin/supervisord"   2 minutes ago       Up 3 minutes        9000/tcp                                   dnmp_php-7.2_1
fa3b3ab309d4        dnmp_php-7.1        "/usr/bin/supervisord"   2 minutes ago       Up 3 minutes        9000/tcp                                   dnmp_php-7.1_1
a73eab5efff0        dnmp_memcached      "docker-entrypoint.sΒ   2 minutes ago       Up 3 minutes        0.0.0.0:11211->11211/tcp                   dnmp_memcached_1
b0795867d8c9        dnmp_mysql          "docker-entrypoint.sΒ   2 minutes ago       Up 3 minutes        0.0.0.0:3306->3306/tcp, 33060/tcp          dnmp_mysql_1
6ff1bbfd6591        dnmp_redis          "docker-entrypoint.sΒ   2 minutes ago       Up 3 minutes        0.0.0.0:6379->6379/tcp                     dnmp_redis_1
```   

可以看到容器已经启动了。
    
* 在浏览其中localhost:

![Demo Image](./snapshot.png)

* 目录结构
 
 目录结构如下：

```
.
├── docker-compose.yml          容器启动配置文件
├── conf                        配置目录
│   ├── mysql                   MySQL 配置文件目录
│   │   └── my.cnf              MySQL 配置文件
│   ├── redis                   Rids 配置文件目录
│   │   └── redis.conf          Rids 配置文件
│   ├── nginx                   Nginx配置文件目录
│   │   ├── conf.d              站点配置文件目录
│   │   │   ├── certs           SSL认证文件、密钥和加密文件目录
│   │   │   │   └── site2       站点2的认证文件目录
│   │   │   ├── www.site1.com.conf  站点1 Nginx配置文件
│   │   │   └── www.site2.com.conf  站点2 Nginx配置文件
│   │   └── nginx.conf          Nginx 通用配置文件
│   └── php-5.6                 PHP 配置目录
│   │   ├── php-fpm.d           PHP-FPM 配置目录
│   │   │   └── www.conf        PHP-FPM 配置文件
│   │   └── php.ini             PHP 配置文件
│   └── php-7.1                 PHP 配置目录
│   │   ├── php-fpm.d           PHP-FPM 配置目录
│   │   │   └── www.conf        PHP-FPM 配置文件
│   │   └── php.ini             PHP 配置文件
│   └── php-7.2                 PHP配置目录
│       ├── php-fpm.d           PHP-FPM配置目录
│       │   └── www.conf        PHP-FPM配置文件
│       └── php.ini             PHP配置文件
├── log                         日志目录
│   ├── mysql                   MySQL 日志目录
│   ├── nginx                   Nginx 日志目录
│   └── php-fpm                 PHP-FPM 日志目录
├── mysql                       MySQL 数据文件目录
└── www                         站点根目录
│   ├── www.sit1.com            站点1根目录
│   └── www.sit2.com            站点2根目录
│ 
└── php                         PHP Dockfiles
│ 
└── other                       Nginx、Redis、MySQL、Memcached Dockfiles
```

* 站点部署

默认加了两个站点：www.site1.com（同 localhos t）和www.site2.com, 在www站点目录中，在 Nginx conf 目录也有相应的配置文件

要在本地访问这两个域名，需要修改本机hosts文件，添加以下两行：

```
127.0.0.1 www.site1.com
127.0.0.1 www.site2.com
```

其中，www.site2.com为支持SSL/https和HTTP/2的示例站点。

因为站点2的SSL采用自签名方式，所以浏览器有安全提示，继续访问就可以了，自己的站点用第三方SSL认证证书替换即可。

如果只用到站点1，把站点2相关的目录和配置文件删除：

```
./conf/nginx/conf.d/certs/site2/
./conf/nginx/conf.d/site2.conf
./www/site2/

```

重启容器内的Nginx生效：

```
$docker-compose  restart nginx 

```
nginx 为docker-compose.yml 规定的服务名（service）


* 切换不同的PHP版本 

找到nginx虚机配置文件
例如： www.site1.com.conf 修改nginx配置文件：

```
location ~ \.php$ {
    fastcgi_pass   fpm-5.6:9000;
    fastcgi_index  index.php;
    include        fastcgi_params;
    fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
}

```
仅需把fastcgi_pass改成：fpm-7.1:9000 ; PHP-FPM的侦听主机改成：Nginx links PHP-FPM service的别名，在docker-compose.yml文件里面我们设置为service别名，例如 php-7.2 为fpm-7.2、php-7.1为fpm-7.1。

具体查看 docker-compose.yml文件 

```
links:
    - php-7.2:fpm-7.2
    - php-7.1:fpm-7.1
    - php-5.6:fpm-5.6
 
```

Nginx容器启动的时候，就会自动修改nginx容器的/etc/hosts，让fpm-* 指向php-* 容器的IP。

修改之后，重启容器中的nginx：

```
$docker-compose  restart nginx 

```

* 使用MySQL数据库

docker-compose.yml文件中，指定了MySQL数据库root用户的密码为123456。 
在主机终端中输入密码，就可以进入MySQL命令行。

```
$ mysql -h 127.0.0.1 -u root -p

```
也可以通过数据库管理软件 navicat 连接MySQL数据库。

在PHP代码中的使用方式与在主机中使用稍有不同，如下：


```
$pdo = new PDO('mysql:host=mysql;dbname=****', 'root', '123456');

```

其中，host的值就是在docker-compose.yml里面指定的MySQL服务的别名。

这是因为PHP代码是在PHP-FPM容器中，PHP-FPM容器启动时会自动在/etc/hosts中加上,可能在你的机器上 IP 有差别：

		172.17.0.2 mysql 11e55f91c4c3 dnmp_mysql_1

就是说，mysql 自动指向了 MySQL 容器动态生成的 IP。

* 使用Redis

Redis使用和MySQL类似。在主机和容器内部都通过地址 127.0.0.1，端口 6379 访问。PHP 则是跨容器访问，host是参数用redis（links指定的名称），端口用6379。

### 构建集成开发环境

1. 从Github上下载
github仓库地址：https://github.com/heguangzhi/dnmp.git 


		#git clone  https://github.com/heguangzhi/dnmp.git

2. 构建 LNMP

		#cd dnmp  
		#docker-compose  build 
		
   需要等待一段时间。
   
	#docker images
	REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
	dnmp_nginx          latest              a1c7de60a1bc        13 seconds ago      17.7MB
	dnmp_php-7.2        latest              e4520299e968        33 seconds ago      806MB
	dnmp_php-7.1        latest              4b0bd4563162        3 hours ago         890MB
	dnmp_php-5.6        latest              a6d260ec1ba8        3 hours ago         905MB
	dnmp_memcached      latest              c1eb398ad98c        3 hours ago         15.6MB
	dnmp_redis          latest              8e1de2836763        3 hours ago         94.9MB
	dnmp_mysql          latest              57b9ce70d5ba        3 hours ago         372MB
	mysql               5.7                 702fb0b7837f        11 days ago         372MB
	redis               latest              1babb1dde7e1        2 weeks ago         94.9MB
	memcached           1.5.11-alpine       01e7979fa175        3 weeks ago         8.97MB
	centos              7.2.1511            4cbf48630b46        3 weeks ago         195MB
	nginx               alpine              aae476eee77d        4 weeks ago         17.7M
   
		
3. 启用 LNMP 


```
#doker-compose  up  -d
	
Creating dnmp_mysql_1     ... done
Creating dnmp_memcached_1 ... done
Creating dnmp_redis_1     ... done
Creating dnmp_php-7.1_1   ... done
Creating dnmp_php-5.6_1   ... done
Creating dnmp_php-7.2_1   ... done
Creating dnmp_nginx_1     ... done

#docker ps 

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                                      NAMES
13798e2af9aa        dnmp_nginx          "nginx -g 'daemon ofΒ   14 seconds ago      Up 13 seconds       0.0.0.0:80->80/tcp, 0.0.0.0:443->443/tcp   dnmp_nginx_1
62a205e13f60        dnmp_php-5.6        "/usr/bin/supervisord"   15 seconds ago      Up 14 seconds       9000/tcp                                   dnmp_php-5.6_1
684309c55220        dnmp_php-7.1        "/usr/bin/supervisord"   15 seconds ago      Up 14 seconds       9000/tcp                                   dnmp_php-7.1_1
ee4b54a4ed8f        dnmp_php-7.2        "/usr/bin/supervisord"   15 seconds ago      Up 14 seconds       9000/tcp                                   dnmp_php-7.2_1
bff009fbf384        dnmp_memcached      "docker-entrypoint.sΒ   16 seconds ago      Up 15 seconds       0.0.0.0:11211->11211/tcp                   dnmp_memcached_1
d42fed007ac6        dnmp_mysql          "docker-entrypoint.sΒ   16 seconds ago      Up 15 seconds       0.0.0.0:3306->3306/tcp, 33060/tcp          dnmp_mysql_1
933c7e41e335        dnmp_redis          "docker-entrypoint.sΒ   16 seconds ago      Up 15 seconds       0.0.0.0:6379->6379/tcp                     dnmp_redis_1

```

[1]:https://cr.console.aliyun.com/cn-beijing/mirrors  "镜像加速器"