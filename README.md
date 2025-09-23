
- [服务器使用指南](#服务器使用指南)
    - [linux主机远程管理软件下载安装](#linux主机远程管理软件下载安装)
    - [远程ssh登录服务器](#远程ssh登录服务器)
    - [服务器联网](#服务器联网)
    - [安装conda环境](#安装conda环境)
      - [安装miniconda](#安装miniconda)
      - [conda换源](#conda换源)
      - [pip换源](#pip换源)
  - [Pycharm远程调试服务器代码](#pycharm远程调试服务器代码)
    - [Linux常用命令及技巧](#linux常用命令及技巧)

# 服务器使用指南

### 写在前面

此教程基于[此仓库](https://github.com/xindongzetong/use)，主要内容包括连接远程服务器的软件准备、软件配置方法和简单的操作教程，主要面向有一定**深度学习环境配置基础**的同学。对于零基础的同学，建议在本地学习一下conda、cuda、pytorch等关键工具之间的依赖关系和安装方法，以及配置虚拟环境的基本命令。在服务器上安装cuda和pytorch的教程详见[原始仓库](https://github.com/xindongzetong/use)。

### linux主机远程管理软件下载安装

下载链接：https://www.xshell.com/zh/free-for-home-school/

![image-20230804173626502](./assets/image-20230804173626502.png)

填写姓名邮件，选择两者，邮件会收到下载链接，下载安装即可。

**注：Xshell用于ssh连接服务器，Xftp用于与服务器文件进行互相传输。Xmanager用于连接校园网（非免费，需要使用破解补丁）。**

***

### 远程ssh登录服务器


打开安装好的xshell，在弹出的会话中新建，名称随意，主机填写申请到的IP地址。

![image-20230818095252657](./assets/image-20230818095252657.png)

此外，还需要设置代理才能连接到服务器。代理-浏览-添加-填写代理主机IP及端口。随后选择添加的代理服务器，点击连接即可。

<img src="./assets/image-20230818095323610.png" alt="image-20230818095323610" style="zoom: 80%;" /><img src="./assets/image-20230818095327991.png" alt="image-20230818095327991" style="zoom: 80%;" />

首次登录会弹出提示输入用户名和密码，将申请到的用户名和密码填写即可，可以勾选记住用户名和密码，下次登录就无需再次输入了。这样就登录上服务器了。

![image-20230818095605502](./assets/image-20230818095605502.png)



### 服务器联网

***

服务器需要使用xmanager打开校园网登陆界面联网：

**注：xmanager没有学生免费使用的优惠，需要自行破解。若火狐浏览器卡死，在xshell中Ctrl+c即可关闭。**

xmanager的破解补丁链接：https://gitcode.com/open-source-toolkit/e1a5a/?utm_source=tools_gitcode&index=top&type=card&&uuid_tt_dd=10_6637885510-1752204339569-876898&isLogin=1&from_id=142889247&from_link=896d219d7cfba93c6e8b0f390c158fe3


在命令行输入firefox打开火狐浏览器，输入10.80.128.2进入校园网登录界面，输入一卡通号和密码登录即可。

<img src="./assets/屏幕截图 2025-09-23 200738.png" alt="屏幕截图 2025-09-23 200738.png" style="zoom:70%;" />

测试网络状态

```shell
ping www.baidu.com
```

![image-20230818100630452](./assets/image-20230818100630452.png)

出现类似这样的说明已经网络连通了

***

### 安装conda环境

#### 安装miniconda

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

开头是许可协议啥的，回车即可，输入`yes`即可开始安装，后面路径、添加到环境变量（这里输入yes）默认回车即可。

<img src="./assets/image-20241018184509313.png" alt="image-20241018184509313" style="zoom:67%;" />

安装完成后，关闭xshell并重新连接服务器后，发现用户名前出现base，说明已经正确安装了miniconda，并处于base环境下。

<img src="./assets/image-20230925165824586.png" alt="image-20230925165824586" style="zoom:80%;" />

conda常见使用命令：

- `conda create -n env_name python=3.10`：创建一个新的conda环境。
- `conda env list`：列出所有conda环境。
- `conda activate env_name`：激活某个conda环境。
- `conda deactivate`：退出当前conda环境。
- `conda env remove -n  env_name --all`：删除某个conda环境。

#### conda换源

由于默认conda源链接为默认官方链接，使用conda装环境下载速度过慢

```shell
conda config --show
```

<img src="./assets/image-20241018185134878.png" alt="image-20241018185134878" style="zoom:67%;" />

切换到清华镜像源

```shell
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/msys2/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/bioconda/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/menpo/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/pytorch/
conda config --set show_channel_urls yes
```

即可更换成功

<img src="./assets/image-20241018185258104.png" alt="image-20241018185258104" style="zoom:67%;" />

#### pip换源

大部分python包使用pip来安装，默认的pip源位于国外，安装python包比较慢，需要进行换源。

一键更换pip源为清华源

```shell
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

**注：安装python包需要联网！**

***

### Pycharm远程调试服务器代码

**xshell是为了使用命令行配置虚拟环境和运行代码等，xftp是为了上传和下载文件。然而，若每次都在本地修改好代码再使用xftp传输到服务器中，十分费时和麻烦。因此，可以使用pycharm连接服务器，便于代码的查看、编辑和上传。**

使用Pycharm配置ssh解释器必须使用pycharm专业版，社区版不可连接！新版本连接方式略有不同，主要步骤包括“Add Interpreter”-“On SSH”-“Existing”。

右下角选择解释器---添加解释器---ssh解释器---新建ssh并测试连接成功---选择服务器自己目录下miniconda下的对应的python解释器---本地电脑的文件夹路径与服务器的项目文件夹进行对应---取消自动同步---检查是否配置正确---应用即可


![image-20241021102105178](./assets/image-20241021102105178.png)![image-20241021102107573](./assets/image-20241021102107573.png)

![image-20241021102123687](./assets/image-20241021102123687.png)

![image-20241021102204491](./assets/image-20241021102204491.png)

![image-20241021102325565](./assets/image-20241021102325565.png)

![image-20241021102404443](./assets/image-20241021102404443.png)

![image-20241021102427895](./assets/image-20241021102427895.png)

![image-20241021102501890](./assets/image-20241021102501890.png)

![image-20241021102617830](./assets/image-20241021102617830.png)

![image-20241021102816135](./assets/image-20241021102816135.png)

![image-20241021102833734](./assets/image-20241021102833734.png)

![image-20241021102956309](./assets/image-20241021102956309.png)


连接到远程服务器之后，在pycharm中点击Tools-Deployment-Browse Remote Host即可查看和修改远程服务器内的文件。

<img src="./assets/屏幕截图 2025-09-23 165408.png" alt="屏幕截图 2025-09-23 165408" style="zoom:67%;" />

**原理：当选择了远程解释器后，pycharm只是起到一个监视运行结果的作用。当在本地pycharm修改完代码之后，仍需要把修改后的代码上传到远程服务器，这样运行的才是最新代码。所以没必要把数据集和模型文件放在本地电脑，代码运行全是在远程服务器上的。**

<img src="./assets/屏幕截图 2025-09-23 165506.png" alt="屏幕截图 2025-09-23 165506" style="zoom:67%;" />


### Linux常用命令及技巧

通过xshell远程ssh连接服务器后，可以直接在命令行运行程序，然而当关闭`shell`窗口时，前台运行的程序或命令也会随之关闭，之前所做的所有工作可能都会丢失，所做的工作可能都要重做一遍。

`screen` 命令允许用户在一个窗口内使用多个终端会话，可以断开连接，也可以重新连接已断开连接的会话。每个会话都可以恢复连接，这样就算会话断开了，用户也不必担心数据丢失，这正好解决了我们的问题。

```shell
screen -S test # 创建一个叫test的会话
```

这样就会进入到全新的会话，只要服务器没有异常断电或者主动销毁会话这个会话就会一直存在。

当需要暂时离开会话，并保持会话里的程序不会中断，可使用

```
Ctrl+A+D
```

即可分离会话。

如需重新连接会话则使用

```shell
screen -r test # 重新连接test会话
```

如需销毁会话，则在会话中输入命令：

```shell
exit # 销毁test会话
```





















