- [ ] 下载安装vagrant

  1. 访问Vagrant官网 https://www.vagrantup.com/
  2. 点击Download， Windows，MacOS，Linux等
  3. 选择对应的版本
  4. 瓜式安装
  5. 命令行输入vagrant，测试是否安装成功

- [ ] 下载安装virtual box

  1. 访问VirtualBox官网 https://www.virtualbox.org/
  2. 选择左侧的“Downloads”
  3. 选择对应的操作系统版本
  4. 瓜式安装
  5. [win10中若出现]安装virtualbox快完成时立即回滚，并提示安装出现严重错误
         (1)打开服务
         (2)找到Device Install Service和Device Setup Manager，然后启动
         (3)再次尝试安装

- [ ] 安装centos7

  1. 创建centos7文件夹，并进入其中[目录全路径不要有中文字符]

  2. 在此目录下打开cmd，运行vagrant init centos/7

     [^此时会在当前目录下生成Vagrantfile，同时指定使用的镜像为centos/7，关键是这个镜像在哪里，我已经提前准备好了，名称是virtualbox.box文件]: 

     

  3. 将virtualbox.box文件添加到vagrant管理的镜像中
     	(1)下载网盘中的virtualbox.box文件
         (2)保存到磁盘的某个目录，比如D:\virtualbox.box
         (3)添加镜像并起名叫centos/7：vagrant box add centos/7 D:\virtualbox.box
         (4)vagrant box list  查看本地的box[这时候可以看到centos/7]

  4. centos/7镜像有了，根据Vagrantfile文件启动创建虚拟机。	来到centos7文件夹，在此目录打开cmd窗口，执行vagrant up[打开virtual box观察，可以发现centos7创建成功]

  5. 以后操作虚拟机，还是要在centos文件夹打开cmd窗口操作
     	vagrant halt   优雅关闭
     	vagrant up     正常启动

  6. vagrant常用命令sys
     	(1)vagrant ssh    
         	进入刚才创建的centos7中
         (2)vagrant status
         	查看centos7的状态
         (3)vagrant halt
         	停止/关闭centos7
         (4)vagrant destroy
         	删除centos7
         (5)vagrant status
         	查看当前vagrant创建的虚拟机
         (6)Vagrantfile中也可以写脚本命令，使得centos7更加丰富
         	但是要注意，修改了Vagrantfile，要想使正常运行的centos7生效，必须使用vagrant reload

     > 至此，使用vagrant+virtualbox搭建centos7完成，后面可以修改Vagrantfile对虚拟机进行相应配置

- [ ] ssh连接centos7

  1. ​	使用centos7的默认账号连接
     ​	在centos文件夹下执行vagrant ssh-config
     ​	关注:Hostname  Port  IdentityFile
     ​	IP:127.0.0.1
     ​	port:2222
     ​	用户名:vagrant
     ​	密码:vagrant
     ​	文件:Identityfile指向的文件private-key	
  2.    使用root账户登录
     	vagrant ssh   进入到虚拟机中
     	sudo -i
     	vi /etc/ssh/sshd_config
     	修改PasswordAuthentication yes
     	passwd修改密码，比如abc123
     	passwd
     	systemctl restart sshd
     	使用账号root，密码abc123进行登录

- [ ] Vagrantfile通用写法

  ```ruby
  # -*- mode: ruby -*-
  # vi: set ft=ruby :
  
  # All Vagrant configuration is done below. The "2" in Vagrant.configure
  # configures the configuration version (we support older styles for
  # backwards compatibility). Please don't change it unless you know what
  # you're doing.
  Vagrant.configure("2") do |config|
    # The most common configuration options are documented and commented below.
    # For a complete reference, please see the online documentation at
    # https://docs.vagrantup.com.
  
    # Every Vagrant development environment requires a box. You can search for
    # boxes at https://vagrantcloud.com/search.
    config.vm.box = "centos/7"
  
    # Disable automatic box update checking. If you disable this, then
    # boxes will only be checked for updates when the user runs
    # `vagrant box outdated`. This is not recommended.
    # config.vm.box_check_update = false
  
    # Create a forwarded port mapping which allows access to a specific port
    # within the machine from a port on the host machine. In the example below,
    # accessing "localhost:8080" will access port 80 on the guest machine.
    # NOTE: This will enable public access to the opened port
    # config.vm.network "forwarded_port", guest: 80, host: 8080
  
    # Create a forwarded port mapping which allows access to a specific port
    # within the machine from a port on the host machine and only allow access
    # via 127.0.0.1 to disable public access
    # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"
  
    # Create a private network, which allows host-only access to the machine
    # using a specific IP.
    # config.vm.network "private_network", ip: "192.168.33.10"
  
    # Create a public network, which generally matched to bridged network.
    # Bridged networks make the machine appear as another physical device on
    # your network.
    config.vm.network "public_network"
  
    # Share an additional folder to the guest VM. The first argument is
    # the path on the host to the actual folder. The second argument is
    # the path on the guest to mount the folder. And the optional third
    # argument is a set of non-required options.
    # config.vm.synced_folder "../data", "/vagrant_data"
  
    # Provider-specific configuration so you can fine-tune various
    # backing providers for Vagrant. These expose provider-specific options.
    # Example for VirtualBox:
    #
    # config.vm.provider "virtualbox" do |vb|
    #   # Display the VirtualBox GUI when booting the machine
    #   vb.gui = true
    #
    #   # Customize the amount of memory on the VM:
    #   vb.memory = "1024"
    # end
      config.vm.provider "virtualbox" do |vb|
          vb.memory = "4000"
          vb.name= "jack-centos7"
          vb.cpus= 2
      end
    #
    # View the documentation for the provider you are using for more
    # information on available options.
  
    # Enable provisioning with a shell script. Additional provisioners such as
    # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
    # documentation for more information about their specific syntax and use.
    # config.vm.provision "shell", inline: <<-SHELL
    #   apt-get update
    #   apt-get install -y apache2
    # SHELL
  end
  ```

  

- [ ] box的打包分发

  1. 退出虚拟机
     	vagrant halt
  2. 打包
     	vagrant package --output first-docker-centos7.box
  3. 得到first-docker-centos7.box
  4. 将first-docker-centos7.box添加到其他的vagrant环境中
     	vagrant box add first-docker-centos7 first-docker-centos7.box
  5. 得到Vagrantfile
     	vagrant init first-docker-centos7
  6. 根据Vagrantfile启动虚拟机
     	vagrant up [此时可以得到和之前一模一样的环境，但是网络要重新配置]

