- [ ] 安装docker

  > https://docs.docker.com/install/linux/docker-ce/centos/

  1. 进入centos7
     	vagrant ssh

  2. 卸载之前的docker
     	sudo yum remove docker \
                       docker-client \
                       docker-client-latest \
                       docker-common \
                       docker-latest \
                       docker-latest-logrotate \
                       docker-logrotate \
                       docker-engine

  3. 安装必要的依赖
     	sudo yum install -y yum-utils \
         device-mapper-persistent-data \
         lvm2
         

  4. 设置docker仓库  [设置阿里云镜像仓库可以先自行百度，后面课程也会有自己的docker hub讲解]	
     	sudo yum-config-manager \
           --add-repo \
           https://download.docker.com/linux/centos/docker-ce.repo

  5. 安装docker
     	sudo yum install -y docker-ce docker-ce-cli containerd.io

  6. 启动docker
     	sudo systemctl start docker

  7. 测试docker安装是否成功
     	sudo docker run hello-world

  8. ​      

         [访问这个地址，使用自己的阿里云账号登录，查看菜单栏左下角，发现有一个镜像加速器:https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors]
         
         sudo mkdir -p /etc/docker
         sudo tee /etc/docker/daemon.json <<-'EOF'
         {
           "registry-mirrors": ["https://30j7bs93.mirror.aliyuncs.com"]
         }
         EOF
         sudo systemctl daemon-reload
         sudo systemctl restart docker
         

  

- [ ] docker基本体验

  1. 创建tomcat容器
     	docker pull tomcat
     	docker run -d --name my-tomcat -p 9090:8080 tomcat
  2. 创建mysql容器
     	docker run -d --name my-mysql -p 3301:3306 -e MYSQL_ROOT_PASSWORD=jack123 --privileged mysql
  3. 进入到容器里面
     	docker exec -it containerid /bin/bash

- [ ] docker 基本命令

  ```
  查看所有的容器命令如下：
  $ docker ps -a
  使用 docker start 启动一个已停止得容器：
  $ docker start b750bbbcfd88 
  在大部分的场景下，我们希望 docker 的服务是在后台运行的，我们可以过 -d 指定容器的运行模式。
  $ docker run -itd --name ubuntu-test ubuntu /bin/bash
  停止容器的命令
  $ docker stop <容器 ID>
  停止的容器可以通过 docker restart 重启：
  $ docker restart <容器 ID>
  进入容器
  $ docker exec -it 243c32535da7 /bin/bash
  不推荐使用$ docker attach 1e560fca3906  注意： 如果从这个容器退出，会导致容器的停止。
  删除容器
  $ docker rm -f 1e560fca3906
  docker logs [ID或者名字] 可以查看容器内部的标准输出。
  $ docker logs -f bf08b7f2cd89
  使用 docker inspect 来查看 Docker 的底层信息。它会返回一个 JSON 文件记录着 Docker 容器的配置和状态信息。
  $ docker inspect wizardly_chandrasekhar
  
  
  
  
  列出镜像列表
  $ docker images      
  各个选项说明:
  		REPOSITORY：表示镜像的仓库源
  		TAG：镜像的标签
  		IMAGE ID：镜像ID
  		CREATED：镜像创建时间
  		SIZE：镜像大小
  		
  runoob@runoob:~$ docker run -t -i ubuntu:15.10 /bin/bash 
  root@d77ccb2e5cca:/#
  参数说明：
          -i: 交互式操作。
          -t: 终端。
          ubuntu:15.10: 这是指用 ubuntu 15.10 版本镜像为基础来启动容器。
          /bin/bash：放在镜像名后的是命令，这里我们希望有个交互式 Shell，因此用的是 /bin/bash。
          
  获取一个新的镜像
  $ docker pull ubuntu:13.10
  查找镜像
  $  docker search httpd
  删除镜像
  $ docker rmi hello-world
  
  创建镜像
  当我们从 docker 镜像仓库中下载的镜像不能满足我们的需求时，我们可以通过以下两种方式对镜像进行更改。
  	1、从已经创建的容器中更新镜像，并且提交这个镜像
  	2、使用 Dockerfile 指令来创建一个新的镜像
  	
  	更新镜像
  	更新镜像之前，我们需要使用镜像来创建一个容器。
      runoob@runoob:~$ docker run -t -i ubuntu:15.10 /bin/bash
      root@e218edb10161:/# 
      在运行的容器内使用 apt-get update 命令进行更新。
      在完成操作之后，输入 exit 命令来退出这个容器。
      此时 ID 为 e218edb10161 的容器，是按我们的需求更改的容器。我们可以通过命令 docker commit 来提交容器副本。
      runoob@runoob:~$ docker commit -m="has update" -a="runoob" e218edb10161 runoob/ubuntu:v2
  sha256:70bf1840fd7c0d2d8ef0a42a817eb29f854c1af8f7c59fc03ac7bdee9545aff8
      各个参数说明：
      -m: 提交的描述信息
      -a: 指定镜像作者
      e218edb10161：容器 ID
      runoob/ubuntu:v2: 指定要创建的目标镜像名
      我们可以使用 docker images 命令来查看我们的新镜像 runoob/ubuntu:v2：
      
      构建镜像
      我们使用命令 docker build ， 从零开始来创建一个新的镜像。为此，我们需要创建一个 Dockerfile 文件，其中包含一组指令来告诉 Docker 如何构建我们的镜像。
      runoob@runoob:~$ cat Dockerfile 
      FROM    centos:6.7
      MAINTAINER      Fisher "fisher@sudops.com"
  
      RUN     /bin/echo 'root:123456' |chpasswd
      RUN     useradd runoob
      RUN     /bin/echo 'runoob:123456' |chpasswd
      RUN     /bin/echo -e "LANG=\"en_US.UTF-8\"" >/etc/default/local
      EXPOSE  22
      EXPOSE  80
      CMD     /usr/sbin/sshd -D
      
      每一个指令都会在镜像上创建一个新的层，每一个指令的前缀都必须是大写的。
      第一条FROM，指定使用哪个镜像源
      RUN 指令告诉docker 在镜像内执行命令，安装了什么。。。
      然后，我们使用 Dockerfile 文件，通过 docker build 命令来构建一个镜像。
      runoob@runoob:~$ docker build -t runoob/centos:6.7 .
      Sending build context to Docker daemon 17.92 kB
      Step 1 : FROM centos:6.7
       ---&gt; d95b5ca17cc3
      Step 2 : MAINTAINER Fisher "fisher@sudops.com"
       ---&gt; Using cache
       ---&gt; 0c92299c6f03
      Step 3 : RUN /bin/echo 'root:123456' |chpasswd
       ---&gt; Using cache
       ---&gt; 0397ce2fbd0a
      Step 4 : RUN useradd runoob
      ......
      
      参数说明：
      -t ：指定要创建的目标镜像名
      . ：Dockerfile 文件所在目录，可以指定Dockerfile 的绝对路径
      使用docker images 查看创建的镜像已经在列表中存在,镜像ID为860c279d2fec
      
   设置镜像标签
   runoob@runoob:~$ docker tag 860c279d2fec runoob/centos:dev
  ```



- [ ] 可能有的疑惑

  1. docker pull在哪拉取的镜像？

     ​	默认是在hub.docker.com

  2. docker pull tomcat拉取的版本是？

     ​	默认是最新的版本，可以在后面指定版本":"

  3. 简单先说一下命令咯

     ```
     docker pull        拉取镜像到本地
     docker run         根据某个镜像创建容器
     -d                 让容器在后台运行，其实就是一个进程
     --name             给容器指定一个名字
     -p                 将容器的端口映射到宿主机的端口
     docker exec -it    进入到某个容器中并交互式运行
     ```

     