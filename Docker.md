### 重要概念

- 镜像
- 容器
- 仓库
- Dockerfile
- tar文件

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c8bab34d-8337-4e91-a837-c775431e4f8e/Docker.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c8bab34d-8337-4e91-a837-c775431e4f8e/Docker.png)

### Docker核心技术

- namespace
- CGroup
- UnionFS

### Docker架构

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e78f713b-2726-4368-bf95-69e48b85026a/Untitled.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e78f713b-2726-4368-bf95-69e48b85026a/Untitled.png)

### 安装

参考Docker帮助文档

```bash
# 安装需要内核版本在3.10以上
unamr -r

# 1.卸载旧版本
yum remove docker \\
                  docker-client \\
                  docker-client-latest \\
                  docker-common \\
                  docker-latest \\
                  docker-latest-logrotate \\
                  docker-logrotate \\
                  docker-engine

# 2.安装需要的安装包
yum install -y yum-utils

# 3.设置镜像仓库
yum-config-manager \\
    --add-repo \\
    <https://download.docker.com/linux/centos/docker-ce.repo> # 默认是国外源

yum-config-manager \\
    --add-repo \\
    <http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo> # 国内阿里云镜像仓库地址

# 4.安装docker相关
yum install docker-ce docker-ce-cli containerd.io

# 5.启动docker
systemctl start docker

# 6.测试是否安装成功
docker version

# 7.测试hello-world镜像
docker run hello-world

# 8.查看下载的hello-world镜像
docker images
```

### 卸载

```bash
# 1.卸载依赖
yum remove docker-ce docker-ce-cli containerd.io
# 2.删除资源
rm -rf /var/lib/docker    # /var/lib/docker是docker默认目录
```

### Docker的常用命令

------

### 帮助

```bash
docker version       # 显示docker版本信息
docker info          # 显示docker详细 系统信息
docker 命令 --help   # 万能帮助命令
```

帮助文档地址：https://docs.docker.com/engine/reference/commandline/

### 镜像命令

**docker images  查看所有images**

```bash
[root@centos-master ~]# docker images
REPOSITORY                           TAG                 IMAGE ID            CREATED             SIZE
k8s.gcr.io/kube-proxy                v1.18.6             c3d62d6fe412        7 weeks ago         117MB
k8s.gcr.io/kube-apiserver            v1.18.6             56acd67ea15a        7 weeks ago         173MB
k8s.gcr.io/kube-controller-manager   v1.18.6             ffce5e64d915        7 weeks ago         162MB
k8s.gcr.io/kube-scheduler            v1.18.6             0e0972b2b5d1        7 weeks ago         95.3MB
alpine                               latest              a24bb4013296        3 months ago        5.57MB
quay.io/coreos/flannel               v0.12.0-amd64       4e9f801d2217        6 months ago        52.8MB
k8s.gcr.io/kube-proxy                v1.17.4             6dec7cfde1e5        6 months ago        116MB
k8s.gcr.io/kube-controller-manager   v1.17.4             7f997fcf3e94        6 months ago        161MB
k8s.gcr.io/kube-apiserver            v1.17.4             2e1ba57fe95a        6 months ago        171MB
k8s.gcr.io/kube-scheduler            v1.17.4             5db16c1c7aff        6 months ago        94.4MB
k8s.gcr.io/pause                     3.2                 80d28bedfe5d        6 months ago        683kB
k8s.gcr.io/coredns                   1.6.7               67da37a9a360        7 months ago        43.8MB
hello-world                          latest              bf756fb1ae65        8 months ago        13.3kB
k8s.gcr.io/etcd                      3.4.3-0             303ce5db0e90        10 months ago       288MB

# 解释
REPOSITORY                           
TAG                 
IMAGE ID            
CREATED             
SIZE

# 可选项
  -a, --all             # 显示所有镜像
  -q, --quiet           # 只显示id
```

**docker search**   搜索镜像

```bash
[root@centos-master ~]# docker search mysql
NAME                              DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
mysql                             MySQL is a widely used, open-source relation…   9945                [OK]                
mariadb                           MariaDB is a community-developed fork of MyS…   3638                [OK]                
mysql/mysql-server                Optimized MySQL Server Docker images. Create…   725                                     [OK]
percona                           Percona Server is a fork of the MySQL relati…   508                 [OK]                
centos/mysql-57-centos7           MySQL 5.7 SQL database server                   83                                      
mysql/mysql-cluster               Experimental MySQL Cluster Docker images. Cr…   75

# 可选项
-f, --filter filter   Filter output based on conditions provided
    --filter=STARS=3000  #搜索3000星以上的镜像
```

**docker  pull**  下载镜像

```bash
# docker pull 镜像名[:tag]
[root@centos-master ~]# docker pull mysql
Using default tag: latest      # 如果不写tag，则默认选择最新版本
latest: Pulling from library/mysql
bf5952930446: Pull complete    # 分层下载
8254623a9871: Pull complete 
938e3e06dac4: Pull complete 
ea28ebf28884: Pull complete 
f3cef38785c2: Pull complete 
894f9792565a: Pull complete 
1d8a57523420: Pull complete 
6c676912929f: Pull complete 
3cdd8ff735c9: Pull complete 
4c70cbe51682: Pull complete 
e21cf0cb4dc3: Pull complete 
28c36cd3abcc: Pull complete 
Digest: sha256:6ded54eb1e5d048d8310321ba7b92587e9eadc83b519165b70bbe47e4046e76a #签名
Status: Downloaded newer image for mysql:latest 
docker.io/library/mysql:latest   # 真实下载地址

# 等价
docker pull mysql
docker pull docker.io/library/mysql:latest

# 指定版本下载镜像
[root@centos-master ~]# docker pull mysql:5.7
5.7: Pulling from library/mysql
bf5952930446: Already exists      
8254623a9871: Already exists 
938e3e06dac4: Already exists 
ea28ebf28884: Already exists 
f3cef38785c2: Already exists 
894f9792565a: Already exists 
1d8a57523420: Already exists 
5f09bf1d31c1: Pull complete 
1591b28581e8: Pull complete 
96ef942f7603: Pull complete 
2e009731422e: Pull complete 
Digest: sha256:1a83136153b238b659b0887ceb4e08275473af1eab2e67de4c22b37c5f4130cd
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7
```

**docker images** 删除镜像

```bash
[root@centos-master ~]# docker rmi -f d58    #根据image id 删除
Untagged: mysql:5.7
Untagged: mysql@sha256:1a83136153b238b659b0887ceb4e08275473af1eab2e67de4c22b37c5f4130cd
Deleted: sha256:d589ea3123e06ae2cfb548f3a697fc82986878afb1ef7b4fe1d8a3981d420620
Deleted: sha256:a014b34bfc08ca4a4fd6a3ddbc9882b4dc3b192d0a0b1e864662cd1d23ea7567
Deleted: sha256:abf85fe645d8297b56e0363050e6df5e1ccc0649d4a71dc2c2f978a1de8ab1da
Deleted: sha256:4299450e41032ab45b35e5d929bfebfa6e3d06528b23b02dc1223fcff7fb0667
Deleted: sha256:165805124136fdee738ed19021a522bb53de75c2ca9b6ca87076f51c27385fd7

[root@centos-master ~]# docker rmi -f $(docker images -qa)  #删除全部镜像
```