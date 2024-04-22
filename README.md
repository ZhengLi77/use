# 服务器使用指南-by时

***

### linux主机远程管理软件下载安装

下载链接：https://www.xshell.com/zh/free-for-home-school/

![image-20230804173626502](./assets/image-20230804173626502.png)

填写姓名邮件，选择两者，邮件会收到下载链接，下载安装即可。

***

### 远程ssh登录服务器

打开安装好的xshell，在弹出的会话中新建，名称随意，主机填写申请到的IP地址。

![image-20230818095252657](./assets/image-20230818095252657.png)

此外，还需要设置代理才能连接到服务器，具体原因可能学院为了增强安全性。代理-浏览-添加-按照微信群公告填写代理主机IP及端口。（用户名和密码可不填）。随后选择添加的代理服务器，点击连接即可。

<img src="./assets/image-20230818095323610.png" alt="image-20230818095323610" style="zoom: 80%;" /><img src="./assets/image-20230818095327991.png" alt="image-20230818095327991" style="zoom: 80%;" />

首次登录会弹出提示输入用户名和密码，将申请到的用户名和密码填写即可，可以勾选记住用户名和密码，下次登录就无需再次输入了。这样就登录上服务器了。

![image-20230818095605502](./assets/image-20230818095605502.png)

### 服务器联网

***

服务器只对接了njupt校园网，不属于校园卡电信或移动。所以请提前在南京邮电大学小程序一卡通网费进行充值（价格：0.2元/小时）。

在命令行输入：

```shell
curl -d "DDDDD=学号&upass=密码&0MKKey=%B5%C7%A1%A1%C2%BC&v6ip=" "192.168.168.168"
```

测试网络状态

```shell
ping www.baidu.com
```

![image-20230818100630452](./assets/image-20230818100630452.png)

出现类似这样的说明已经网络连通了

可写成一个shell脚本，方便使用

命令行输入：

```shell
echo 'curl -d "DDDDD=学号&upass=密码&0MKKey=%B5%C7%A1%A1%C2%BC&v6ip=" "192.168.168.168"' > login.sh
```

登陆时即可用：

```shell
bash login.sh
```

登录

同样，如果服务器登陆了自己的账号，使用完了想退出登录，可输入命令：

```shell
curl "192.168.168.168/F.htm"
```

即可退出登录，可以用ping命令进行验证

**注：可提前使用ping命令先测试，如果网络不通再去登录，节省网费。因为服务器网卡是共享的，只要有一个人连接了网络，所有人都可以使用网络。**

***

### 安装miniconda

Miniconda和Anaconda的区别：

![image-20230918170915265](./assets/image-20230918170915265.png)

由于我们每个项目需要不同的环境，并不需要anaconda自带的环境，故选择安装miniconda即可。

<img src="./assets/image-20230918170954809.png" alt="image-20230918170954809" style="zoom: 80%;" />

服务器为linux系统，且为64位操作系统，下载最新版`miniconda`

Miniconda官网：https://docs.conda.io/en/latest/miniconda.html

下载链接：https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh

下载完成后利用xftp将安装包放在服务器自己的路径下。

<img src="./assets/image-20230918171524719.png" alt="image-20230918171524719" style="zoom:80%;" />

也可在保证服务器联网时使用`wget`命令直接下载到服务器中：

```shell
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
```

<img src="./assets/image-20230918171730847.png" alt="image-20230918171730847" style="zoom:80%;" />

请保证下载到100%，否则安装程序无法正常运行。

赋予安装程序执行权限：

```shell
chmod +x Miniconda3-latest-Linux-x86_64.sh
```

运行安装程序：

```shell
./Miniconda3-latest-Linux-x86_64.sh
```

开头是许可协议啥的，回车即可，输入`yes`即可开始安装，后面路径、添加到环境变量默认回车即可。

安装完成后，关闭xshell并重新连接服务器后，发现用户名前出现base，说明已经正确安装了miniconda，并处于base环境下。

<img src="./assets/image-20230925165824586.png" alt="image-20230925165824586" style="zoom:80%;" />

conda常见使用命令：

- `conda create -n env_name python=3.10`：创建一个新的conda环境。
- `conda env list`：列出所有conda环境。
- `conda activate env_name`：激活某个conda环境。
- `conda deactivate`：退出当前conda环境。
- `conda env remove -n  env_name --all`：删除某个conda环境。

***

### 跑通Yolov5做迁移训练

首先使用`conda`创建虚拟环境并激活环境：

```shell
conda create -n yolov5 python=3.8
conda activate yolov5
git clone --branch v5.0 https://github.com/ultralytics/yolov5.git # 将yolov5官方项目克隆到服务器上，可使用git来克隆项目
cd yolov5
```

使用xftp打开yolov5文件夹下的requirements.txt发现

<img src="./assets/image-20231007160007039.png" alt="image-20231007160007039" style="zoom:80%;" />

需要使用到torch和torchvision这两个包的，且需要满足版本要求。由于torch和torchvision版本有对应关系，而且torch和cuda版本也有版本对应关系，而cuda版本取决于显卡和显卡驱动，所以在安装torch和torchvision包之前应先确认显卡及显卡驱动。安装好cuda之后再去安装torch和torchvision包。

```shell
nvidia-smi # 查看显卡驱动支持的cuda版本
```

<img src="./assets/image-20231007160608232.png" alt="image-20231007160608232" style="zoom:80%;" />

**注：显卡的算力也决定了cuda的版本，比如RTX3090的算力为8.6，就不支持cuda11.0及以下的版本，所以在安装cuda之前可以先调查以下服务器的显卡型号，以及支持的cuda版本**

可以看到当前服务器显卡支持最大cuda版本为10.2。所以我们可以安装版本低于10.2的cuda。安装cuda有多种方式：

1、本地安装

当你需要使用cuda来编译程序的话，则需要使用本地安装，否则建议在虚拟环境中安装。

非root本地安装cuda可以参考这篇文章：https://zhuanlan.zhihu.com/p/476313656?utm_id=0



2、conda安装

激活环境并查询可安装的cudatoolkit版本：

![image-20231030151250577](./assets/image-20231030151250577.png)

















### Linux常用命令及技巧

#### 环境变量







#### 通过会话后台跑程序

通过xshell远程ssh连接服务器后，可以直接在命令行运行程序，然而当关闭`shell`窗口时，前台运行的程序或命令也会随之关闭。那么你之前所做的所有工作可能都会丢失，所做的工作可能都要重做一遍，这会浪费我们许多的时间，非常影响我们的工作。

`screen` 命令允许用户在一个窗口内使用多个终端会话，可以断开连接，也可以重新连接已断开连接的会话。每个会话都可以恢复连接，这样就算会话断开了，用户也不必担心数据丢失，这正好解决了我们的问题。

```shell
screen -S test # 创建一个叫test的会话
```

这样就会进入到全新的会话

```

```



