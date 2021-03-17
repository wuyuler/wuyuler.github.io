# vscode远程连接

vscode远程连接服务器, 查看和在vscode的命令行里运行代码非常方便, 记录一下配置过程。

# ssh

## 安装open ssh

远程连接使用的是ssh连接，我们win10上需要安装open ssh，以比较方便的使用ssh命令。

open ssh的安装非常简单，只要下载对应版本的zip安装包，解压，然后配置环境变量即可。

下载地址：

[Releases · PowerShell/Win32-OpenSSH · GitHub](https://github.com/PowerShell/Win32-OpenSSH/releases)

我安装的是：

![image-20210203223839223](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210203223839.png)

解压内容如下：

![image-20210203222508367](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210203223826.png)

将文件目录位置添加到系统path：

我的电脑右键-》属性-》高级系统设置-》环境变量-》编辑系统变量中的path

![image-20210203224213479](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210203224213.png)

ps: 一个小技巧，按住shift键右键，可以直接复制路径，如下图：

![image-20210203224303928](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210203224303.png)

以上步骤完成后，openssh就算安装完成了，我们在cmd里测试是否成功：

![image-20210203224532916](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210203224532.png)

## 配置免密登录

现在已经可以使用ssh登录远程服务器了，但是每次登录都要输入密码很麻烦，我们配一下免密。

创建公钥，输入以下命令一路按回车

```bash
ssh-keygen -t rsa -C "alice"
```

根据输出提示的.ssh位置, 打开`id_rsa.pub`, 复制其中内容到`authorized_keys`

没有的话就自己创建

```
touch authorized_keys
```

这样就可以免密登陆了

![image-20210203232610331](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210203232610.png)

ps: 我一开始使用想用cmd修改authorized_keys, 在复制粘贴使出了不少问题, 为防止问题其实大家可以先用vscode的远程登陆插件登陆, 用在vscode里修改authorized_keys会减少很多麻烦

# vscode远程登陆插件Remote SSH

在商店中搜索并安装

![image-20210203230635135](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210203230635.png)

出现应用图标

![image-20210203230709309](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210203230709.png)

添加配置, 连续回车即可

![image-20210203230809449](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210203230809.png)

使用该连接打开窗口,

![image-20210203231000591](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210203231000.png)

可以打开自己需要的文件, 即可远程查看和编辑

![image-20210203232024030](https://gitee.com//yyjjtt/picture_bed/raw/master/img/20210203232024.png)