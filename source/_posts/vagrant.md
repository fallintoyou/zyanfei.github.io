---
title: vagrant里搭建centos7
date: 2018-11-28 10:29:17
tags:
---
>vagrant是简便虚拟机操作的一个软件，而使用虚拟机有几个好处：
>1、为了开发环境与生产环境一致（很多开发环境为windows而生产环境为linux），不至于出现在开发环境正常而移步到正式生产环境时出现各种问题，而vagrant通过共享文件，可以实现在主机（windows）下的IDE编写代码操作，直接在虚拟机（linux）中运行展示出效果。
>2、在vagrant中只需要搭配一次开发环境，然后就可以将搭配好的环境系统镜像打包发送给其他的同事用了，其他同事只需要下载vagrant和virtualBox，然后配置下共享目录后就可以开发了，再也不需要关心配置环境的问题了。
>3、目前好多扩展如swoole、redis等对linux支持更好，甚至有些只支持linux，所以采用虚拟机，再也不需要为学习新技术找借口了。
>相关环境机软件版本：主机：win10；虚拟机：CentOS 7 x86_64；vagrant：1.9.5；virtualBox：5.2
##### 一、安装步骤如下：
1、下载virtualBox，地址点[这里](https://www.virtualbox.org/)，安装
2、下载vagrant，地址点[这里](https://www.vagrantup.com/downloads.html)，安装
3、下载镜像，http://www.vagrantbox.es/ 和 https://app.vagrantup.com/boxes/search 都可以 我找了个centos/7
4、将下载的镜像加载，顺便说下，第3步可以不用，vagrant支持在线安装镜像，但由于墙的原因，所以最好通过其它方法将镜像下载下来,再在本地加载,，打开cmd，输入以下命令：
```bash
命令是这样的，title为自己起的名字，url为box地址，可以在线也可以本地
vagrant box add {title} {url}
实际安装命令，本地下载下来的
vagrant box add CentOs7 E:\boxes\centos-7.0-x86_64.box
```

5、找一个/新建一个目录，例如vagrant_project，然后初始化环境
```bash
#新建目录
mkdir vagrant_project
#打开此目录cd vagrant_project
#初始化，CentOs7为你上方设置的title
vagrant init CentOs7
```
初始化成功后，本目录下会出现Vagrantfile这样一个文件，这个是vagrant的配置文件，后续的配置在这里修改

6、先都使用默认的配置，来启动下：
```bash
vagrant up
```
会显示一系列启动信息：
```bash
==> default: Clearing any previously set forwarded ports...
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
    default: Adapter 2: hostonly
==> default: Forwarding ports...
    default: 22 (guest) =&gt; 2222 (host) (adapter 1)
==> default: Running 'pre-boot' VM customizations...
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222//注意这里
    default: SSH username: vagrant//这里
    default: SSH auth method: private key
    .......
```
如果不报错的话就算是启动成功了，如果出现错误则谷歌或百度，我这边主要遇到的错误为无限卡在 default: SSH auth method: private key这个地方，很大可能的原因是本机没有开启虚拟化技术，重启电脑，按f2或其它的键进入bios设置，找到virtual相关字样的开启即可。

7、远程连接linux，此时windows下可以用putty或者是xshell来连接，host地址如上方标识的注意： 127.0.0.1:2222，用户名密码都为：vagrant

此时，默认的共享文件夹的对于关系是windows下的vagrant_project文件夹对应linux下的/vagrant文件夹，可以随便在双方建立几个文件测试下是否两方是同步的

8、关闭等命令，由于还有好多需要配置，暂时可以关闭，等配置完成后再启动
```bash
#关闭
vagrant halt
#重启
vagrant reload
```

##### 二、网络配置、共享目录等相关配置
以上仅仅是展示了安装和开启的简单用法，但真正能用于开发还需要额外的配置，打开上方提到的Vagrantfile配置文件来进行配置，好多都是已注释掉的，选择去掉即可

1、网络配置，vagrant有三种配置方法：官网文档点[这里](https://www.vagrantup.com/docs/networking/)查看
　　a、端口映射，意思是将虚拟机的端口映射到主机的端口上，主机局域网内可以通过访问这个端口来访问到你虚拟机上的东西
```bash
config.vm.forwarded_port 80, 8080
```
　　上述作用是将虚拟机的80端口映射到主机的8080端口上，那我直接访问主机的8080端口就相当于访问虚拟机的80端口

　　b、私有网络，只有主机才能访问虚拟机，主句局域网内成员不能访问的虚拟机的内容
  ```bash
config.vm.network "private_network", ip: "192.168.33.10"
```
   上述设置完成后，在本机可以通过192.168.33.10来访问虚拟机，需要注意192.168.33.10虽然可以随便设定，但如果主机的ip为192.168.1.xxx字段，那就不要这个再用这个区段，可以2.xxx或3.xxx或其它，以免造成冲突。
　　c、共有网络，主机局域网内成员可以访问虚拟机中的内容，虚拟机相当于局域网内的一员
  ```bash
config.vm.network "public_network", ip: "192.168.1.120"
```
　　以上配置一般来说开发环境也不需要局域网内成员访问，所以大部分情况下是选择用b、私有网络来开发，这个可根据实际情况来选择。

<font color=#FF0000>
如果虚拟机里安装了nginx，有时候会出现能ping通ip，ssh也能登录。但是输入ip地址访问不了网站的问题，这时候尝试关闭centos7防火墙。
</font>

```bash
systemctl stop firewalld.service
systemctl disable firewalld.service #防止开机启动
```

2、共享目录，用户可以自定义共享目录，在Vagrantfile配置文件中配置：
#禁用原有的默认的共享目录
```bash
config.vm.synced_folder '.', '/vagrant', disabled: true
```
#增加新的共享目录，第二个参数以当前文件夹为基准
```bash
config.vm.synced_folder "abc", "/www/web/abc",
```
上方相当与windows下的vagrant_project/abc文件夹对应linux下的/www/web/abc文件夹
除了可以自定义共享文件外，还可以选择共享的方式，vagrant有四种共享的方式，官网文档请点这里查看
　　a、NFS共享方式，windows平台无法使用此配置，且用此方式时不能修改文件的所有者和所有组
　　b、RSync共享方式，所有平台都可以使用，但这种方式貌似有个缺点就是主机的改动可以同步到虚拟机中，但虚拟机中的改动无法同步到主机中，如果要实现双向同步可能还需做其它设置，由于没有用过，所以不太清楚。
　　c、SMB共享方式，只有windows平台可以使用，曾有找一些资料来配置，但一直没有成功，所以具体的并不太清楚，但貌似效率是挺不错的。
　　d、VirtualBox共享方式，这是VirtualBox提供的也是vagrant默认的共享方式。当文件数量小时用着不错，当文件数量大的时候打开网页会卡到超时。
总之以上，如果主机是mac，则推荐用NFS共享方式，如果是windows则推荐用smb或者rsync方式，如果文件很少，那用默认的就可以了。
但除了上方说的，windows下其实还有方式可以加快网站访问速度，那就是用Vagrant WinNFSd这款插件，插件地址点这里，这种方式是windows下提高速度最简单的方式了：
```bash
vagrant plugin install vagrant-winnfsd
```
安装完成后配置type为nfs，虽然官网上说nfs不能应用于windows，但用这个插件后就可以了。
```bash
config.vm.synced_folder "abc", "/www/web/abc",type:"nfs"
```

测试了下，此插件还是非常有效的，原先打开yii2框架的文件会超时，或10、20多秒，但用了这个后响应在2s以内，还是可以接受的。另提供一个参考资料：让Vagrant在Windwos下支持使用NFS/SMB共享文件夹从而解决目录共享IO缓慢的问题

 三、域名访问及打包分发1、域名访问，主要是虚拟的本地域名配置问题
这里还是安装了lanmp_v3.1，具体的安装方法可以看以前的文章：Linux下安装LANMP环境或者去下载官网上去找安装说明

新建一个站点，指定目录到共享的目录，随便取个虚拟的域名：www.abc.com测试需要在主机中修改host，如下：（至于虚拟机中/etc/hosts文件中，测试修改与不修改都不影响访问）
```bash
192.168.33.10    www.abc.com
192.168.33.10    abc.com
```
然后直接在浏览器中用http://www.abc.com既可显示虚拟机中的内容了。而直接在windows中的的IDE修改文件，会实时通过这个网址展示出来。
2、打包分发
```bash
vagrant package
```
等待一段时间，会发现在目录生成一个package.box的文件，这个就是已经包含安装环境的镜像了，将此镜像发给同事，让其安装上方所属的步骤加载镜像vagrant box add {title} {url}命令，然后配置自己的网络及共享文件夹就可以直接开发了，远离重新配置环境的烦恼。
以上就是vagrant常用的一些操作，挺不错的，试试吧！

* * *
vagrant 默认登录账号:vagrant 密码vagrant，root账号:root 密码vagrant

一些常用的命令

vagrant init  # 初始化

vagrant up  # 启动虚拟机

vagrant halt  # 关闭虚拟机

vagrant reload  # 重启虚拟机

vagrant ssh  # SSH 至虚拟机

vagrant status  # 查看虚拟机运行状态

vagrant destroy  # 销毁当前虚拟机

vagrant box remove [box-name]#移除某个虚拟机
