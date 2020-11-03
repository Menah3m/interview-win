## CentOS环境中安装K8s集群



#### 0.安装环境

CentOS7虚拟机两台

+ 主节点     （主机名：master    IP地址：192.168.13.100）
+ 工作节点 （主机名：slave   IP地址：192.168.13.101）



#### 1.虚拟机准备

+ 更新hosts文件

  ```shell
  # 使用vi工具编辑hosts文件
  vi /etc/hosts
  
  # 添加以下内容到文件末尾
  192.168.13.100 master
  192.168.13.101 slave
  199.232.68.133 raw.githubusercontent.com
  ```

  

+ 关闭并禁用防火墙，SELinux，swap

  ```shell
  # 关闭防火墙（临时生效，重启虚拟机后会失效）
  systemctl stop firewalld
  # 关闭防火墙的开机自启（防止重启后防火墙又被打开）
  systemctl disable firewalld
  
  # 关闭SELinux（临时生效）
  setenforce 0
  # 关闭SELinux的开机自启
  sed -i 's/SELINUX=.*/SELINUX=disabled/g' /etc/selinux/config
  
  # 关闭swap
  swapoff -a
  # 禁用swap
  sed -i.bak '/swap/s/^/#/' /etc/fstab
  ```



+ 设置master免密登录到slave(**只在master上执行**)

  ```shell
  ssh-keygen -t rsa -P ""
  ssh-copy-id -i /root/.ssh/id_rsa.pub root@slave
  ```



#### 2.安装Docker

```shell
# 安装yum相关插件
yum install -y yum-utils

# 设置docker源
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

# 安装docker ce
yum -y install docker-ce-19.03.9 docker-ce-cli-19.03.9 containerd.io

# 启动docker 并设置 开机自启
systemctl start docker
systemctl enable docker

# 测试安装是否成功
docker --version
docker run hello-world

```



#### 3.master安装

+ 修改内核参数

  ```shell
  # 打开k8s.conf文件
  vi /etc/sysctl.d/k8s.conf
  # 添加以下内容到最后
  net.bridge.bridge-nf-call-ip6tables=1
  net.bridge.bridge-nf-call-iptables=1
  # 重新载入使设置生效
  sysctl -p /etc/sysctl.d/k8s.conf
  ```

+ 设置kubernetes源

  ```shell
  # 打开设置文件
  vi /etc/yum.repos.d/kubernetes.repo
  # 添加内容
  [kubernetes]
  name=Kubernetes
  baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7x86_64/
  enabled=1
  gpgcheck=1
  repo_gpgcheck=1
  gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm.package-key.gpg
  
  # 更新yum缓存
  yum clean all
  yum -y makecache
  ```

  

+ 安装kubernetes （版本：1.17）

  ```shell
  # 安装
  yum -y install kubelet-1.17.4 kubeadm-1.17.4 kubectl-1.17.4 --setopt=obsoletes=0
  
  # 启动kubelet并设置开机自启
  systemctl start kubelet
  systemctl enable kubelet
  ```

  

+ 下载k8s要用到的镜像

  ```shell
  # 编辑下载用的脚本
  vi image.sh
  
  # 写入以下内容
  #!/bin/bash
  url=registry.cn-hangzhou.aliyuncs.com/google_containers
  version=v1.17.4
  images=(`kubeadm config images list --kubernetes-version=$version|awk -F '/' '{print $2}'`)
  for imagename in ${images[@]} ; do
  	docker pull $url/$imagename
  	docker tag $url/$imagename k8s.gcr.io/$imagename
  	docker rmi -f $url/$imagename
  done
  
  # 给脚本添加运行权限
  chmod u+x image.sh
  
  # 运行脚本
  ./image.sh
  
  # 查看下载结果
  docker images
  ```

  

#### 4.master配置

```shell
# 初始化master
kubeadm init --apiserver-advertise-address 192.168.13.100 --pod-network-cidr=10.202.0.0/16

# 根据初始化之后的提示执行以下命令
mkdir -p $HOME/.kube
cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
chown $(id -u):$(id -g) $HOME/.kube/config

# 加载环境变量
source .bash_profile

# 安装pod网络
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

# 查看令牌
kubeadm token list

# 如果令牌为空，则创建新令牌
kubeadm token create

# 生成加密串
openssl x509 -pubkey -in /etc/kubernetes/pki/ca.crt | openssl rsa -pubin -outform der 2>/dev/null | openssl dgst -sha256 -hex | sed 's/^.* //'

```



#### slave安装和配置

+ 设置kubernetes源

  ```shell
  # 打开设置文件
  vi /etc/yum.repos.d/kubernetes.repo
  # 添加内容
  [kubernetes]
  name=Kubernetes
  baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7x86_64/
  enabled=1
  gpgcheck=1
  repo_gpgcheck=1
  gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm.package-key.gpg
  
  # 更新yum缓存
  yum clean all
  yum -y makecache
  ```

  

+ 安装kubernetes （版本：1.17）

  ```shell
  # 安装
  yum -y install kubelet-1.17.4 kubeadm-1.17.4 kubectl-1.17.4 --setopt=obsoletes=0
  
  # 启动kubelet并设置开机自启
  systemctl start kubelet
  systemctl enable kubelet
  ```



+ 加入集群

   ```shell
  # 加入master创建的集群中
  kubeadm join 192.168.13.100:6443 --token xxxxxxxx(master节点的令牌) --discovery-token-ca-cert-hash sha256:xxxxxx(master节点产生的加密串)
  # 在master节点 验证结果
  kubectl get nodes
   ```

