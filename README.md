# 一．Ubuntu 14.04安装Docker
输入如下命令来安装Docker
```
$ sudo apt-get install -y curl
$ curl -k -sSl https://get.docker.com | sudo sh
```
完成安装后使用如下命令来验证是否安装成功：

	$ sudo docker run hello-world
如果出现下面的信息，说明安装成功了：
```
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://cloud.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/engine/userguide/
```
# 二．Docker的基本操作
1.用如下命令启动容器，在容器中执行命令:

	$ docker run IMAGE [COMMAND][ARG...]
例如:
```
$ sudo docker run ubuntu echo 'Hello Docker'
Hello Docker
```
2.启用交互式容器

	$ docker run -i -t IMAGE /bin/bash
注意：
```
-ｉ	--interactive=true|false 默认是false
-t	--tty=true|false 默认是false
```
例如：
```
$ sudo docker run -i -t ubuntu /bin/bash
root@f389c402aa7a:/# ls
bin   dev  home  lib64  mnt  proc  run   srv  tmp  var
boot  etc  lib   media  opt  root  sbin  sys  usr
```
输入exit命令来退出容器:
```
root@f389c402aa7a:/# exit
exit
```
3.查看容器:

	$ docker ps [-a][-l]
例如：
```
$ sudo docker ps -a -l
CONTAINER　ID　IMAGE 　COMMAND 　　　CREATED 　　　　STATUS
PORTS NAMES
f389c402aa7a  ubuntu  "/bin/bash"  4 minutes ago  Exited (0) 2 minutes ago   optimistic_nobel
```
4.自定义容器名:

	$ docker run --name=自定义名 -i -t IMAGE /bin/bash
例如:

	$ sudo docker run --name=jacks -i -t ubuntu /bin/bash

5.重新启动停止的容器:

	$ sudo docker start [-i] 容器名
例如：
```
$ sudo docker start -i jacks
root@c34769ce6d7e:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```
6.删除一个停止运行的容器:

	$ docer remove
例如：
```
$ sudo docker rm jacks
jacks
```
7.查看容器详细信息:

	$ docker inspect 容器名或者ID
例如：
```
$ sudo docker inspect c63ecc37ac7a
[
    {
        "Id": "c63ecc37ac7a0c0d0b1470adbefd9777187272ad81fab424dd71f4755761cf97",
        "Created": "2018-01-09T00:36:20.599458825Z",
        "Path": "/bin/bash",
        "Args": [],
        "State": {

　　　　　　　　　...

            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "a3588560e1340705b7f94d35c4bb84984f7dbb7477f4b27ddebe38860357ec25",
                    "EndpointID": "763a11831e61dc1c01b116c810b9c567aadce8fcf05af538bd89a2ccc53bf9b2",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]
```
# 三.守护式容器
1.以守护形式运行容器:

	$ docker run -i -t IMAGE /bin/bash
然后以Ctrl+P Ctrl+Q退出，这个容器就在后台运行了.
2.重新连接到守护形式容器:

	$ docker attach 容器名或者ID
例如：
```
$ sudo docker attach c63ecc37ac7a
/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```
3.启动守护式容器:

	$ docker run -d 镜像名 [COMMAND][ARG..]
4.查看容器日志:

	$ docker logs [-f][-t][-tail] 容器名
注意:
```
-f	--follow=true|false 默认false
-t	--timestamps=true|false 默认false
--tail= "all"
```
5.查看容器内进程

	$ docker top 容器名
6.在运行中的容器内启动新的进程

	$ docker exec [-d][-i][-t]容器名[COMMAND][ARG...]
7.停止守护式容器:
```
$ docker stop 容器名
$ docker kill 容器名
```
注意：
stop命令发送停止命令给容器，等待容器停止，而kill命令则直接杀死容器．
# 四.在容器中部署静态网站
1.设置容器端口映射:
```
run [-P][-p]
-P --public-all=true|false　默认为false
```
使用这个命令将暴露容器所有端口映射:

```
docker run -P -i -t ubuntu /bin/bash
－p --publish=[]
```
使用这个命令将暴露容器所有指定80号端口:

	docker run -p 80 -i -t ubuntu /bin/bash
2.在容器中安装Nginx，下面演示过程:
```
$ sudo docker run -p 80 --name web -i -t ubuntu /bin/bash
root@8a2532cb8739:/# apt-get update
root@8a2532cb8739:/# apt-get install -y nginx
root@8a2532cb8739:/# apt-get install -y vim
root@8a2532cb8739:/# mkdir -p /var/www/html
root@8a2532cb8739:/# cd /var/www/html/
root@8a2532cb8739:/var/www/html# vim index.html
`
编辑并保存为：
<html>
	<head>
		<title>Hello Nginx</title>
	</head>
	<body>
		<h1>Nginx in Docker !</h1>
	</body>
</html>
:wq
`
root@8a2532cb8739:/var/www/html# vi /etc/nginx/sites-enabled/default
`
编辑并保存：
root /var/www/html;
:wq
`
root@8a2532cb8739:/var/www/html# cd /
root@8a2532cb8739:/# nginx
root@8a2532cb8739:/# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 03:07 pts/0    00:00:00 /bin/bash
root       868     1  0 03:31 ?        00:00:00 nginx: master process nginx
www-data   869   868  0 03:31 ?        00:00:00 nginx: worker process
www-data   870   868  0 03:31 ?        00:00:00 nginx: worker process
www-data   871   868  0 03:31 ?        00:00:00 nginx: worker process
www-data   872   868  0 03:31 ?        00:00:00 nginx: worker process
root       873     1  0 03:31 pts/0    00:00:00 ps -ef

`ctrl+p ctrl +q 退出，让容器后台运行｀
jack@jack-X455LD:~$ sudo docker port web
80/tcp -> 0.0.0.0:32769
jack@jack-X455LD:~$ curl http://127.0.0.1:32769
<html>
	<head>
		<title>Hello Nginx</title>
	</head>
	<body>
		<h1>Nginx in Docker !</h1>
	</body>
</html>
｀
使用docker inspect web查看web容器信息中
"IPAddress": "172.17.0.2"
｀
jack@jack-X455LD:~$ curl http://172.17.0.2
<html>
	<head>
		<title>Hello Nginx</title>
	</head>
	<body>
		<h1>Nginx in Docker !</h1>
	</body>
</html>
```
3.停止一个容器，然后再次启动进入:
```
jack@jack-X455LD:~$ sudo docker stop web
web
jack@jack-X455LD:~$ sudo docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
jack@jack-X455LD:~$ sudo docker start -i web
root@8a2532cb8739:/# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 03:40 pts/0    00:00:00 /bin/bash
root        11     1  0 03:40 pts/0    00:00:00 ps -ef

```
# 五.Docker镜像与仓库（一）
1.查看和删除镜像:
```
$ docker images [OPTIONS] [REPOSITORY]
-a	--all=false
-f	--filter=[]
--no-trunc=false
-q	--quiet=false
```
例如：
```
jack@jack-X455LD:~$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              latest              00fd29ccc6f1        3 weeks ago         111MB
hello-world         latest              f2a91732366c        7 weeks ago         1.85kB
```

```
jack@jack-Ubuntu:~$ sudo docker images --no-trunc
REPOSITORY          TAG                 IMAGE ID                                                                  CREATED             SIZE
ubuntu              latest              sha256:f975c50357489439eb9145dbfa16bb7cd06c02c31aa4df45c77de4d2baa4e232   4 weeks ago         112MB
hello-world         latest              sha256:f2a91732366c0332ccd7afd2a5c4ff2b9af81f549370f7a19acd460f87686bc7   4 months ago        1.85kB

```

2.查看镜像
```
$doker inspect [OPTIONS] CONTAINER|IMAGE[CONTAINER|IMAGE...]
-f,--format=""
```
例如：
```
jack@jack-Ubuntu:~$ sudo docker inspect ubuntu
[
    {
        "Id": "sha256:f975c50357489439eb9145dbfa16bb7cd06c02c31aa4df45c77de4d2baa4e232",
        "RepoTags": [
            "ubuntu:latest"
        ],
        "RepoDigests": [
            "ubuntu@sha256:e348fbbea0e0a0e73ab0370de151e7800684445c509d46195aef73e090a49bd6"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2018-03-06T22:17:26.531075062Z",
        "Container": "6e8eb576ec0f7564a85c0fbd39824e0e91c031aa0019c56c5f992449e88d1142",
        "ContainerConfig": {
            "Hostname": "6e8eb576ec0f",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],

...
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:a94e0d5a7c404d0e6fa15d8cd4010e69663bd8813b5117fbad71365a73656df9",
                "sha256:88888b9b1b5b7bce5db41267e669e6da63ee95736cb904485f96f29be648bfda",
                "sha256:52f389ea437ebf419d1c9754d0184b57edb45c951666ee86951d9f6afd26035e",
                "sha256:52a7ea2bb533dc2a91614795760a67fb807561e8a588204c4858a300074c082b",
                "sha256:db584c622b50c3b8f9b8b94c270cc5fe235e5f23ec4aacea8ce67a8c16e0fbad"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]
```
3.删除镜像
```
$docker rmi [OPTIONS]IMAGE[IMAGE...]
-f,--force=false  Force removal of the image
--no-prune=false  Do not delete untagged parents
```
先查看有哪些镜像:
```
jack@jack-Ubuntu:~$ sudo docker images 
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              latest              f975c5035748        4 weeks ago         112MB
hello-world         latest              f2a91732366c        4 months ago        1.85kB
```

然后选择删除

4.查找镜像
方法一：Docker Hub
```
https://registry.hub.docker.com
```

方法二：
```
$docker search [OPTIONS] TERM
--automated=false  Only show automated builds
--no-trunc=false   Don't truncate output
-s,--stars=0       Only displays with at least x starsApril 7, 2018 4:18 PM
```
例如：
```
jack@jack-Ubuntu:~$ sudo docker search ubuntu
NAME                                                      DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
ubuntu                                                    Ubuntu is a Debian-based Linux operating sys…   7467                [OK]                
dorowu/ubuntu-desktop-lxde-vnc                            Ubuntu with openssh-server and NoVNC            174                                     [OK]
rastasheep/ubuntu-sshd                                    Dockerized SSH service, built on top of offi…   138                                     [OK]
ansible/ubuntu14.04-ansible                               Ubuntu 14.04 LTS with ansible                   91                                      [OK]
ubuntu-upstart                                            Upstart is an event-based replacement for th…   85                  [OK]                
neurodebian                                               NeuroDebian provides neuroscience research s…   46                  [OK]                
ubuntu-debootstrap                                        debootstrap --variant=minbase --components=m…   37                  [OK]                
1and1internet/ubuntu-16-nginx-php-phpmyadmin-mysql-5      ubuntu-16-nginx-php-phpmyadmin-mysql-5          33                                      [OK]
nuagebec/ubuntu                                           Simple always updated Ubuntu docker images w…   22                                      [OK]
tutum/ubuntu                                              Simple Ubuntu docker images with SSH access     18                                      
ppc64le/ubuntu                                            Ubuntu is a Debian-based Linux operating sys…   12                                      
i386/ubuntu                                               Ubuntu is a Debian-based Linux operating sys…   10                                      
1and1internet/ubuntu-16-apache-php-7.0                    ubuntu-16-apache-php-7.0                        9                                       [OK]
eclipse/ubuntu_jdk8                                       Ubuntu, JDK8, Maven 3, git, curl, nmap, mc, …   5                                       [OK]
1and1internet/ubuntu-16-nginx-php-phpmyadmin-mariadb-10   ubuntu-16-nginx-php-phpmyadmin-mariadb-10       4                                       [OK]
darksheer/ubuntu                                          Base Ubuntu Image -- Updated hourly             3                                       [OK]
codenvy/ubuntu_jdk8                                       Ubuntu, JDK8, Maven 3, git, curl, nmap, mc, …   3                                       [OK]
1and1internet/ubuntu-16-nginx-php-5.6-wordpress-4         ubuntu-16-nginx-php-5.6-wordpress-4             3                                       [OK]
pivotaldata/ubuntu                                        A quick freshening-up of the base Ubuntu doc…   1                                       
ossobv/ubuntu                                             Custom ubuntu image from scratch (based on o…   0                                       
1and1internet/ubuntu-16-sshd                              ubuntu-16-sshd                                  0                                       [OK]
1and1internet/ubuntu-16-healthcheck                       ubuntu-16-healthcheck                           0                                       [OK]
pivotaldata/ubuntu-gpdb-dev                               Ubuntu images for GPDB development              0                                       
smartentry/ubuntu                                         ubuntu with smartentry                          0                                       [OK]
thatsamguy/ubuntu-build-image                             Docker webapp build images based on Ubuntu      0                                       

```

```
jack@jack-Ubuntu:~$ sudo docker search -s 3 ubuntu
Flag --stars has been deprecated, use --filter=stars=3 instead
NAME                                                      DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
ubuntu                                                    Ubuntu is a Debian-based Linux operating sys…   7467                [OK]                
dorowu/ubuntu-desktop-lxde-vnc                            Ubuntu with openssh-server and NoVNC            174                                     [OK]
rastasheep/ubuntu-sshd                                    Dockerized SSH service, built on top of offi…   138                                     [OK]
ansible/ubuntu14.04-ansible                               Ubuntu 14.04 LTS with ansible                   91                                      [OK]
ubuntu-upstart                                            Upstart is an event-based replacement for th…   85                  [OK]                
neurodebian                                               NeuroDebian provides neuroscience research s…   46                  [OK]                
ubuntu-debootstrap                                        debootstrap --variant=minbase --components=m…   37                  [OK]                
1and1internet/ubuntu-16-nginx-php-phpmyadmin-mysql-5      ubuntu-16-nginx-php-phpmyadmin-mysql-5          33                                      [OK]
nuagebec/ubuntu                                           Simple always updated Ubuntu docker images w…   22                                      [OK]
tutum/ubuntu                                              Simple Ubuntu docker images with SSH access     18                                      
ppc64le/ubuntu                                            Ubuntu is a Debian-based Linux operating sys…   12                                      
i386/ubuntu                                               Ubuntu is a Debian-based Linux operating sys…   10                                      
1and1internet/ubuntu-16-apache-php-7.0                    ubuntu-16-apache-php-7.0                        9                                       [OK]
eclipse/ubuntu_jdk8                                       Ubuntu, JDK8, Maven 3, git, curl, nmap, mc, …   5                                       [OK]
1and1internet/ubuntu-16-nginx-php-phpmyadmin-mariadb-10   ubuntu-16-nginx-php-phpmyadmin-mariadb-10       4                                       [OK]
darksheer/ubuntu                                          Base Ubuntu Image -- Updated hourly             3                                       [OK]
1and1internet/ubuntu-16-nginx-php-5.6-wordpress-4         ubuntu-16-nginx-php-5.6-wordpress-4             3                                       [OK]
codenvy/ubuntu_jdk8                                       Ubuntu, JDK8, Maven 3, git, curl, nmap, mc, …   3                                       [OK]
```

6.拉取镜像
```
$docker pull [OPTIONS] NAME [:TAG]
-a,--all-tags=false Download all tagged images in the repository
```
例如：
```
jack@jack-Ubuntu:~$ sudo docker images -a
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              latest              f975c5035748        4 weeks ago         112MB
hello-world         latest              f2a91732366c        4 months ago        1.85kB
jack@jack-Ubuntu:~$ sudo docker pull ubuntu:14.04
14.04: Pulling from library/ubuntu
99ad4e3ced4d: Pull complete 
ec5a723f4e2a: Pull complete 
2a175e11567c: Pull complete 
8d26426e95e0: Pull complete 
46e451596b7c: Pull complete 
Digest: sha256:7676e23855de0b95bb3e025561ac272a8ba530c78f54915cb7952051cff753e6
Status: Downloaded newer image for ubuntu:14.04
jack@jack-Ubuntu:~$ sudo docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              latest              f975c5035748        4 weeks ago         112MB
ubuntu              14.04               a35e70164dfb        4 weeks ago         222MB
hello-world         latest              f2a91732366c        4 months ago        1.85kB
```
当然，这样获取镜像比较慢，可以使用如下--registry-mirror选项来加速下载:
```
1.修改：/etc/default/docker
2.添加:DOCKER_OPTS = "--registry-mirror=http://MIRROR-ADDR"
```
例如：
```
DOCKER_OPTS="$DOCKER_OPTS --registry-mirror=http://f2d6cb40.m.daocloud.io"
```
然后重启docker:
```
sudo service docker restart
```
7.推送镜像
```
$docker push NAME[:TAG]
```
8.构建镜像
```
第一种方法:$docker commit 通过容器构建
第一种方法:$docker build 通过Dockerfile文件构建
```
使用commit构建镜像
```
$docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
-a,--author="" Author
			e.g.,"John Hannibal Smith hannibal@a-team.com"
-m,--message="" Commit message
-p,--pause=true Pause container during commit
```

例如:
先启动一个容器bash：
```
jack@jack-Ubuntu:~$ sudo docker run -it -p 80 --name commit_test ubuntu /bin/bash
```
然后在容器中安装tree软件:
```
root@95b48d3cec6e:/# apt-get update
root@95b48d3cec6e:/# apt-get tree
```
最后提交:
```
jack@jack-Ubuntu:~$ sudo docker ps -l
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
95b48d3cec6e        ubuntu              "/bin/bash"         7 minutes ago       Exited (2) 2 minutes ago                       commit_test
jack@jack-Ubuntu:~$ docker commit -a 'User information' -m 'tree' commit_test Username/commit_test1
```

使用Dockerfile构建镜像
```
jack@jack-Ubuntu:~$ mkdir -p dockerfile/df_test1
jack@jack-Ubuntu:~$ cd dockerfile/df_test1/
jack@jack-Ubuntu:~/dockerfile/df_test1$ vi Dockerfile
```
Dockerfile内容如下:
```
#First Dockerfile
FROM ubuntu:14.04
MAINTAINER dormancypress "dormancypress@outlook.com"
RUN apt-get update
RUN apt-get install tree
```
接下来编译运行Dockerfile脚本,Dockerfil脚本格式如下:
```
$ docker build [OPTIONS] PATH | URL | -
--force-rm=false
--no-cache=false
-q,--quiet=false
--rm=true
-t,--tag=""
```
例如：
docker build -t='dormancypress/df_test1' .