# <center> Docker 制作 tomcat 镜像 笔记

环境搭建  

> win 7/10操作系统
> virtualBox
> centos7.6 虚拟机
> tomcat 8.5
> Java jdk 1.8

## 下载资源
[tomcat8.5下载，提取码:w5c1](https://pan.baidu.com/s/1OdxJH4JSulT_uPE-Q2w4Cw)
[Java jdk 1.8下载，提取码:mry6](https://pan.baidu.com/s/1K0MPqRaDx-xtsfT8UDXqjg)
1. centos7.6系统中新建文件夹
    `mkdir /home/data/tomcat/webapps/`
    `mkdir /home/data/software`

2. 将下载好的软件上传到/home/software/ 文件中,可以使用xftp
    [root@localhost software]# pwd
    /home/software
    [root@localhost software]# ll
    total 196712
    -rw-r--r--. 1 root root   9672485 Apr 12 23:29 apache-tomcat-8.5.39.tar.gz
    -rw-r--r--. 1 root root 191757099 Apr 12 23:56 jdk-8u192-linux-x64.tar.gz


## 开始搭建环境
1. 安装docker
`yum install docker`
`systemctl start docker`
2. 中国镜像设置，pull/push进行的时候速度快点<br>
    `vi /etc/docker/daemon.json`

    ```
        { 
            "registry-mirrors":["https://registry.docker-cn.com"] 
        }
    ```
3. pull centos:7镜像<br>
`docker pull centos:7`
4. 启动centos:7进行配置<br>
`docker run --privileged=true -it -v /home/software/:/mnt/software/ centos:7 /bin/bash`

    ```
    [root@localhost software]# docker run -it -v /home/software/:/mnt/software/ centos:7 /bin/bash
    [root@72c0213110c7 /]# pwd
    /
    ```
    - ***java jdk1.8***安装
         - `cd /mnt/software/`   该文件中的内容就是宿主机中挂载的文件内容
         - `tar -zxf jdk-8u192-linux-x64.tar.gz`解压jdk程序包
         - `mv jdk1.8.0_192 /opt/jdk/`移动jdk目录
    - ***tomcat 8.5***安装
         - `tar -zxf apache-tomcat-8.5.39.tar.gz`
         - `mv apache-tomcat-8.5.3/ /opt/tomcat/`
5. 编写运行脚本 <br>
    `touch /root/run.sh`
    `vi /root/run.sh`

    ```
        #!/bin/bash
        export JAVA_HOME=/opt/jdk/
        export PATH=$JAVA_HOME/bin:$PATH
        sh /opt/tomcat/bin/catalina.sh run
    ```
    
    `chmod u+x /root/run.sh`  为脚本添加权限
    
6. 退出容器<br>
    `exit`

## 制作镜像

1. `docker ps -a`  查看容器id
2. `docker commit 刚刚制作的容器id tomcat8.5`
3. `docker images` 查看制作成功的镜像


## 使用tomcat8.5创建tomcat容器
1. 从tomcat的webapps目录下面的ROOT文件放入/home/test/ 中
2. 创建容器端口映射为8000,挂在目录为/home/test/，**--privileged=true** 这个运行参数必须要有<br>
 `docker run -p 8000:8080 -d --privileged=true -v /home/test/:/opt/tomcat/webapps/ bigzhouyq/tomcat8.5 /root/run.sh`
3. 浏览器访问http://192.168.56.2:8000/ 访问成功

-------
镜像已经上传值[dockerHub](https://cloud.docker.com/repository/docker/bigzhouyq/tomcat8.5)









