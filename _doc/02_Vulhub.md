
**使用Vulhub一键搭建漏洞测试靶场**

### 1. docker安装
```
https://docs.docker.com/engine/install/centos/

# yum install -y yum-utils device-mapper-persistent-data lvm2
# yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
# yum install docker-ce
# systemctl enable docker
# systemctl start docker
# yum list docker-ce --showduplicates

https://docs.docker.com/compose/

# url -L "https://github.com/docker/compose/releases/download/1.27.0/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
# chmod +x /usr/local/bin/docker-compose 
# docker-compose --version
docker-compose version 1.27.0, build 980ec85b

Ubuntu下
# 修改docker国内镜像源
/etc/docker/daemon.json
{
"registry-mirrors":["https://cr.console.aliyun.com"]
}
# apt-get install docker*
# service docker start
# apt-get install git
# apt-get install net-tools
```
### 2. 安装Git

```
# mkdir github
# cd github/
# yum install -y git
# git version
git version 1.8.3.1
```

### 3.下载Vulhub靶机
```
https://vulhub.org/
# git clone https://github.com/vulhub/vulhub.git  
正克隆到 'vulhub'...
remote: Enumerating objects: 78, done.
remote: Counting objects: 100% (78/78), done.
remote: Compressing objects: 100% (53/53), done.
remote: Total 9648 (delta 28), reused 55 (delta 15), pack-reused 9570
接收对象中: 100% (9648/9648), 129.47 MiB | 35.00 KiB/s, done.
处理 delta 中: 100% (3791/3791), done.

或着 
wget https://github.com/vulhub/vulhub/archive/master.zip -O vulhub-master.zip
# pwd
/github/vulhub
# pwd
/github/vulhub/weblogic/CVE-2018-2628
# docker-compose build
weblogic uses an image, skipping
# docker-compose up -d
Creating network "cve-2018-2628_default" with the default driver
Pulling weblogic (vulhub/weblogic:)...
latest: Pulling from vulhub/weblogic
6599cadaf950: Pull complete
23eda618d451: Pull complete
f0be3084efe9: Pull complete
52de432f084b: Pull complete
a3ed95caeb02: Pull complete
a2318f26c625: Pull complete
1aa642dd8cc1: Pull complete
b307208f8bf5: Pull complete
1dfbbdcc497d: Pull complete
a53e674a7606: Pull complete
5f06bb51fa3c: Pull complete
ff0ff72567f2: Pull complete
684862046025: Pull complete
abbf8d475455: Pull complete
848eb11ef744: Pull complete
2f3438f2b83b: Pull complete
8e5871e15571: Pull complete
Digest: sha256:275ec19477cfda389dc1c42158033e7e8c650dd4cba9f090ca0ba673902b73c9
Status: Downloaded newer image for vulhub/weblogic:latest
Creating cve-2018-2628_weblogic_1 ... done
# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
vulhub/weblogic     latest              7d35c6cd3bcd        3 years ago         2.46GB
# docker ps -a
CONTAINER ID        IMAGE               COMMAND              CREATED             STATUS              PORTS                              NAMES
bb165ea737df        vulhub/weblogic     "startWebLogic.sh"   9 minutes ago       Up 9 minutes        5556/tcp, 0.0.0.0:7001->7001/tcp   cve-2018-2628_weblogic_1

访问 http://192.168.11.226:7001/console
```
- docker start $(docker ps -qa) #启动所有镜像
- docker stop $(docker ps -qa) #停止所有镜像
- docker rmi $(docker ps -qa) #删除所以镜像
- docker exec -it id /bin/bash #进入到一个docker shell下
- docker export -o test.tar 54dc157b2982 # 镜像打包 test.tar  
- cat test.tar | docker import - test_123 # 导入镜像包到本地
- docker images #查看test_123是否导入成功
- run -it -v /:/mnt --entrypoint /bin/bash ubuntu # 映射宿主机根目录到虚拟机/mnt下
- docker push zhang0908/fudan-consumer1:v1 #将本地镜像上传Docker Hub（或阿里云Hub）
- docker pull hang0908/fudan-consumer1 #从Docker Hub（或阿里云Hub）、下载镜像到本地