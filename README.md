# HackMD 本身
原文链接：https://hackmd.io/Aohp4vZ0QYGEkNsErb0S2Q

水墨屏必须用浅色背景，可以在编辑器右下角找到画笔🖌图标，进行修改。对于LCD显示屏，直接更改回去即可。

# 周期运行小工具
使用crontab可以在Unix-like的操作系统上周期性运行某些脚本、程序等等，用于进行记录。
https://www.runoob.com/linux/linux-comm-crontab.html

# 服务器配置、ssh配置、IDE配置
## pycharm连接远程服务器并调试代码
> vscode在远程服务器上调试的机制做的是比pycharm好的，使用vscode调试不用将远程服务器和本地的代码同步，直接在远程服务器上就可以直接运行调试。我觉得这样才符合心目中的远程调试的操作。

> PyCharm采用端云联合方式进行远程开发（其实也有专门用于远程的客户端，但好像不太好用），本地和服务端都会有代码，这可能从一定程度上加快了其索引速度，并支持其强大的代码补全和辅助功能，但一个不可避免的弊病就是端云不一致性、缓存映射错乱等各类奇奇怪怪的bug，整体在调试体验上是需要一段时间适应的。另外一个解决方案是直接远程桌面连到服务器，在服务器开PyCharm[滑稽].

> vscode较轻量级占用内存较少，每次打开软件需要的时间少。但是vscode对于新手来说上手比较复杂，且在高亮显示界面、代码补全等功能细节方面较pycharm略差一些（毕竟pycharm是专门针对python开发的插件），最后IDE选择因人而异，要看更适合哪种。

step1. [下载professional版本pycharm](https://mp.weixin.qq.com/s/RH0oCJWD00QFXpuCYwB8oA)(community版本不能连服务器)：

step2. [pycharm连接远程服务器](https://www.jb51.net/article/166790.htm)

step3. [配置ssh interpreter](https://blog.csdn.net/weixin_40592798/article/details/125150344)


### FAQ

Q 多个项目同时使用一个服务器上的解释器时，经常出现用了一会A项目后发现B项目的deployment莫名其妙被改的情况
A 勾选Visible only for this project就可以避免这个问题，为每个项目配各自的deployment即可。

<img src="https://cdn.jsdelivr.net/gh/1candoallthings/figure-bed@main/img/202302051924222.png" alt="image-20230205192444051" style="zoom: 25%;" />

Q 莫名其妙地出现`Can‘t get remote credentials for deployment server`的bug
A 参见[博客](https://blog.csdn.net/qq_26108393/article/details/123994555)，我的解决方法是：

![image-20230205194444912](https://cdn.jsdelivr.net/gh/1candoallthings/figure-bed@main/img/202302051944067.png)

Q 一个本地项目同时上传到两个server时，automatic upload可能不好使
A 重启一下Automatic Upload，此时pycharm可能会让你选自动同步到哪个服务器，之前应该是选择了另一个
![image-20230211105211277](https://cdn.jsdelivr.net/gh/1candoallthings/figure-bed@main/img/202302111052309.png)



## pycharm连接远程服务器的jupyter

step1. [在远程服务器的conda环境中安装jupyter](https://blog.csdn.net/qq_18256855/article/details/125439096)



step2.[使用pycharm连接远程jupyter](https://www.jb51.net/article/247326.htm)

在Configured Server中，填写`http://[服务器ip]:[jupyter端口号]/`，如下图

![](https://cdn.jsdelivr.net/gh/1candoallthings/figure-bed@main/img/202307211156685.png)

尝试run cell后，会进行身份验证，如下图

<img src="https://cdn.jsdelivr.net/gh/1candoallthings/figure-bed@main/img/202305221725513.png" alt="image-20230522172522477" style="zoom:25%;" />

输入后即可运行。

step3.(如果step2失败) [使用ssh连接远程jupyter](https://blog.csdn.net/qq_18256855/article/details/125439096)


```
ssh -L [本地端口]:localhost:[远程端口] [远程用户名]@[远程IP] -p [ssh连接端口]
```

注意：这里的远程端口是上面配置的端口号（例如我配置的是8890，这里就需要写8890），本地端口随便写一个就可以，我这里写的是8888。

```sh
ssh -L 8888:localhost:8890 liuchi@10.1.114.77
```



## ssh配置免密登录

注意：SSH是客户端，SSHD是服务端。

以本地A连接服务器B为例，参见[博客](http://t.zoukankan.com/kuangke-p-12390453.html）

1. 登录A机器
2. 命令行输入`ssh-keygen -t rsa`，将会生成密钥文件和私钥文件`id_rsa`和`id_rsa.pub`。以A机器=实验室电脑为例，将保存在`C:\Users\Administrator\.ssh`路径下。同一台机器生成一次即可。
3. 将 .pub 文件复制到B机器的 .ssh 目录， 并 cat id_rsa.pub >> ~/.ssh/authorized_keys
```
# 可用命令行代替(Linux)
ssh-copy-id -i ~/.ssh/id_rsa.pub root@192.168.20.60

# Windows (untested)
type  public_id | plink.exe -pw <password> username@hostname "umask 077; test -d .ssh || mkdir .ssh ; cat >> .ssh/authorized_keys"
```


5. 大功告成，从A机器登录B机器的目标账户，不再需要密码了；（直接运行 **ssh 192.168.20.60** ）

### MobaXterm免密登录

图中私钥也即本机的.ssh路径下的id_rsa文件。
如果编辑已有session不work，重新创建session并配置即可。

<img src="https://cdn.jsdelivr.net/gh/1candoallthings/figure-bed@main/img/202211112346142.png" alt="image-20221111234649983" style="zoom:33%;" />

### FAQ

Q 配置后仍需密码，原因是56的PubkeyAuthentication设置为no，如下图

![image-20221103145315467](https://cdn.jsdelivr.net/gh/1candoallthings/figure-bed@main/img/202211031453499.png)

A 参见[博客](https://blog.csdn.net/sun2019720/article/details/125725328)解决

## Pip vs Conda
> Pip installs Python packages whereas conda installs packages which may contain software written in any language. For example, before using pip, a Python interpreter must be installed via a system package manager or by downloading and running an installer. Conda on the other hand can install Python packages as well as the Python interpreter directly.

大白话=安装软件可先用Conda，再试pip。Conda能解决的依赖库更广。

pip下载速度慢的问题-国内index解决：
https://www.runoob.com/w3cnote/pip-cn-mirror.html
```shell!
pip3 install numpy -i https://pypi.tuna.tsinghua.edu.cn/simple
```

### Conda Install
https://docs.anaconda.com/free/anaconda/install/linux/
Update according to latest version:https://repo.anaconda.com/archive/
```
sudo apt-get install libgl1-mesa-glx libegl1-mesa libxrandr2 libxrandr2 libxss1 libxcursor1 libxcomposite1 libasound2 libxi6 libxtst6

curl -O https://repo.anaconda.com/archive/Anaconda3-2024.02-1-Linux-x86_64.sh
```



## 75服务器重新挂载/data1和/data2
75服务器重启后，需要重新挂载硬盘
首先，使用omnisky用户登陆75
使用命令 sudo fdisk -l 查看分区
使用命令 sudo mount /dev/sdb1 /data1 挂载第一块硬盘
使用命令 sudo mount /dev/sdc1 /data2 挂载第二块硬盘

设置自动挂载：
【待补充】


## 服务器连接外网（校园网）


[下载BITSrun的github并上传到服务器](https://github.com/coffeehat/BIT-srun-login-script)

例如在77中：
```shell
cd /data1/yyx/resources/BIT-srun-login-script-master/
nohup python always_online.py & 
```
ping百度试一下，成功了即可愉快的使用wandb等需要连外网的应用。

# Doing Experiments

## Tensorboard

如果要使用tensorboard进行数据实时监控，可以在服务器上运行
```shell
tensorboard --logdir XXX --port YYY --bind_all
```
其中`XXX`是tensorboard的登记路径，`YYY`是绑定的端口（建议选择动态端口，即49152-65535)
必须使用--bind_all才能从本机访问。
接下来，在本机浏览器中输入：`server_ip:YYY`，即可访问tensorboard.

## Wandb
wandb是更加便捷的记录方式，最常用的几个功能：
1. wandb.log，记录各类参数
2. wandb.define_metric，可以在实验概要中显示某个重要指标的最大值、最小值和平均值。

wandb自带有“Report”功能，可生成直接展示实验数据窗口的动态报告，方便直观，非常适合持久化记录某一组对比实验。但要注意Report功能bug比较多，最严重的是在实验选择页面不能使用剪切快捷键，否则会把整个实验Panel剪切走，再次粘贴时机会崩溃。好在有自动保存，每次崩溃后找到Reports，光标滑过自己的bug显示Draft，继续编辑即可。
![Screen Shot 2024-02-23 at 22.56.01](https://hackmd.io/_uploads/H1yGfNIna.png)


# linux常用命令

> 所有linux命令都可以问gpt！
## SSH服务
```
# restart SSH
service sshd restart
# get SSH Config Profile
vim /etc/ssh/sshd_config
```
## 系统管理操作
修改用户名正确操作：https://www.networkworld.com/article/969308/how-to-modify-user-account-settings-with-usermod.html
```
# logout user from root
pkill -u username
# force logout, used when pkill -u is not successful.
pkill -KILL -u {username}
#### WARNING, DO NOT DO THIS ####
pkill -KILL -u root
# change user name and home directory
sudo usermod -l new_username old_username
sudo usermod -d /home/new_username -m new_username
# or in single command line (not tested)
sudo usermod -l new_username -d /home/new_username -m old_username
# check existing users
getent passwd [UID]
```

## 重启服务器

```
sudo reboot
```

如果liuchi用户有root权限，可以直接在liuchi用户下进行root操作，没必要以omnisky用户登录。（再通过exit返回普通用户）

```
sudo -i
```


## 资源

- **查看GPU使用情况**

```
nvidia-smi
```

- **查看CPU使用情况**

```
top
htop
```

htop中可以看到以下三项：

![image-20221014155159054](https://cdn.jsdelivr.net/gh/1candoallthings/figure-bed@main/img/202210141551083.png)

Tasks代表进程，running是正在运行的进程数量，不是所有进程都正在被运行。

thr是线程数，392个线程被分解成1344个线程。

## 进程

- **查看含有关键字foo的进程信息**

```shell
ps -ef | grep foo
```

- **查看含有关键字foo的进程数量**

```shell
ps -ef | grep -c foo
```

- **进程信息按创建时间顺序排列**

```shell
ps -ef --sort=start_time
```

单行进程信息太长，屏幕宽度放不下，让单行信息换行：（[亲测ww可行](https://blog.csdn.net/wzy9854/article/details/77885732)，是很无语的设计）

```shell
ps -efww 
```

- **挂起进程**

ctrl z是挂起，GPU仍然被该进程占用，ctrl c才是中断进程！


- **按进程号杀死进程，-9表示强制杀死**

```shell
# force shutdown
kill -9 <pid>
kill -SIGKILL <pid>
# more graceful, but may not work.
kill -15 <pid>
kill -SIGTERM <pid>
kill -2 <pid>
kill -SIGINT <pid>
```

- **批量杀死含有关键字"foo"的进程**

```shell
ps -ef | grep foo | grep -v grep | cut -c 9-15 | xargs kill -9 
```

上述命令的含义可以参见[博客1](https://www.cnblogs.com/lichkingct/archive/2010/08/27/1810463.html)和[博客2](https://www.cnblogs.com/miracle-luna/p/15426103.html)。

用王昊这个也行：

```shell
ps -ef | grep foo | grep -v grep | awk '{print $2}' | xargs kill -9 
```


## 存储

【du】

- **查看当前路径大小**

```
du -sh  # du意为disk usage
```

- **查看当前路径下各子路径的大小**，比较繁琐

```
du -h
du -h foo.txt  # 仅查看foo.txt文件的大小
du -ah  # 不仅给出子路径，也给出文件
```

- **查看各一级子路径的大小**，很常用

```
du -h --max-depth=1
# sort according to size
du -h . --max-depth=1 | sort -h
```

1. **-s**：对每个Names参数只给出占用的数据块总数。

2. **-a**：递归地显示指定目录中各文件及子目录中各文件占用的数据块数。若既不指定-s，也不指定-a，则只显示Names中的每一个目录及其中的各子目录所占的磁盘块数。

3. **-b**：以字节为单位列出磁盘空间使用情况（系统默认以k字节为单位）。

4. **-k**：以1024字节为单位列出磁盘空间使用情况。

5. **-c**：最后再加上一个总计（系统默认设置）。

6. **-l**：计算所有的文件大小，对硬链接文件，则计算多次。

7. **-x**：跳过在不同文件系统上的目录不予统计。

8. **-h**：以K，M，G为单位，提高信息的可读性。

   

【df】

以磁盘分区为单位查看文件系统，可以获取硬盘被占用了多少空间，目前还剩下多少空间等信息。

- **查看各个分区的使用情况**

```
df -h
# home disk
df -h /home
```

显示内容参数说明：

1. **Filesystem**：文件系统
2. **Size**： 分区大小
3. **Used**： 已使用容量
4. **Avail**： 还可以使用的容量
5. **Use%**： 已用百分比
6. **Mounted on**： 挂载点



## 路径

- **查看当前路径**

```
echo $(pwd)
# or just pwd is fine
```

- **剪切文件夹（重命名）**

```
mv SRC_DIR DST_DIR
```

- **复制文件夹**

```
cp -r SRC_DIR DST_DIR
```

不同于mv，cp必须用`-r`指明复制对象为文件夹。若不指定会报错。

- **复制路径时排除某些子路径**

[参考博客](https://www.cnblogs.com/xiaoyou2018/p/16443637.html)

排除当前路径下的file1和dir2，将其他文件复制到/data。若想复制文件夹而不是文件，加-r

```
cp !(file1|dir2) /data/
```

**- 删除路径（谨慎使用，使用不当可以自毁电脑）**

```
rm -rf DIR
```

-r:删除对象为路径。-f:直接删除，无需逐一确认。

若想排除某路径，删除其他路径，和cp的用法相同。

- **两个服务器之间文件夹传输**
### scp
```
scp [参数] <源地址（用户名@IP地址或主机名）>:<文件路径> <目的地址（用户名 @IP 地址或主机名）>:<文件路径>
```
文件夹传输参数使用-r
### rsync
有断点续传，"-avuzPr"是一个复合选项，可压缩传出路径，并输出传输进度。
```
# download
rsync -e 'ssh -p <port>' -avuzPr <user_name>@<ip>:<source_dir> <dest_dir>
# upload
rsync -e 'ssh -p <port>' -avuzPr <source_dir> <user_name>@<ip>:<dest_dir>
```

- **查找路径下文件、文件夹**

[博客](https://blog.csdn.net/wuyiqinag/article/details/127249949)里说的更全，例如正则匹配路径下文件数

```
wfind -name XXX
```




# 生产力小工具

[~~在windows使用everything+powertoys实现类似mac的聚焦搜索~~](https://www.bilibili.com/read/cv15627874) [2024年1月更新：Listary一键下载配置，更好用！](https://www.listary.com/)


[tmux终端复用器，即使终端窗口关闭实验仍能继续跑](http://www.ruanyifeng.com/blog/2019/10/tmux.html)

[screen，类似tmux但是一次只能开一个session，同样能做到后台运行，也可作为简化版使用](https://www.howtogeek.com/662422/how-to-use-linuxs-screen-command/)


# 文献检索

## [查某年某会议（如AAAI2022）全部文章列表]((https://zhuanlan.zhihu.com/p/473360171))

直接在dblp.org搜索框搜AAAI，在Venue search results中选择

![](https://cdn.jsdelivr.net/gh/1candoallthings/figure-bed@main/img/202307211203992.png)

点进去后，能看到AAAI'22的主会议，点contents

![](https://cdn.jsdelivr.net/gh/1candoallthings/figure-bed@main/img/202307211203893.png)

ctrl+f搜索track on，能看到按track分类的所有文章。不分页！ctrl+f可以搜全

![](https://cdn.jsdelivr.net/gh/1candoallthings/figure-bed@main/img/202307211203849.png)

## CCF参考表网址

[CCF推荐会议/期刊PDF](https://www.ccf.org.cn/Academic_Evaluation/By_category/)

## 其他常用期刊SCI指标参考
注意，引用时尽量用通讯三大顶刊：JSAC、TON、TMC，以下期刊供次级参考。
- SCI一区

    - IEEE Transactions on Intelligent Transportation Systems （TITS，CCF-B）
    - IEEE Transactions on Vehicular Technology（TVT）
    - IEEE Transactions on Network Science and Engineering （TNSE，CCF-B，吴大鹏老师创始，刘老师是副主编）
    - Future Generation Computer Systems
    -  Journal of Network and Computer Applications
-   - IEEE Internet of Things Journal（CCF-C，但刘老师评价还可以，可根据文章质量决定）
    -  （弱Q1）Engineering Applications of Artificial Intelligence
    -  （弱Q1）Ad Hoc Networks
- SCI二区
    - (强Q2）IEEE Transactions on Green Communications and Networking
    - （强Q2）IEEE Transactions on Automation Science and Engineering
    - IEEE Access
    - Wireless Networks
    - Sensors (化学和仪器领域为Q1，但计算机领域影响力弱一些)


## 引用指数参考
打开链接：[JSAC IEEE](https://ieeexplore.ieee.org/xpl/RecentIssue.jsp?punumber=49)
可以拿到顶刊的影响因子、引用分数等，除了参考CCF列表外，也可以参照这个比对期刊的影响力

[ACM SIGCOMM](https://dl.acm.org/conference/comm)
ACM的内容会稍微少一点

# 文献编写
## Latex
[LatexLive](https://www.latexlive.com)，可用于实验各种公式，测试无误后粘贴到Overleaf中。

# 代码优化

在PyCharm的Debugger中可以一键输入：
```python=
import timeit;
a=timeit.timeit("your code here", globals=globals(), number=100);
a;
```
实时检查不同代码的运行时间，从而完成就地代码优化。
通过`number`变量控制运行次数，以便次数越多计算结果越准确。

# Docker
## 正确安装
首先，有很多地方要sudo，绝大部分docker操作，否则无权限修改对应文件。

其次处理错误：
```shell
docker: Error response from daemon: could not select device driver "" with capabilities: [[gpu]].

```

https://www.cnblogs.com/booturbo/p/16318627.html
装好nvidia-docker2, nvidia-container-runtime等
```
sudo apt-get update
sudo apt-get install -y nvidia-docker2
sudo systemctl restart docker
```
Docker的控制可以映射容器的22端口到服务器的一个用户自定义端口，这样就和其他服务器一样连接了。
## Docker基本控制
```shell
sudo docker start <container_name>
sudo docker stop <container_name>
sudo docker exec -it <container_name> /bin/zsh(/bin/bash)
```
## commit/pull/push
commit就像任何git操作一样，把进度保存；
push可以把docker image推到remote；
pull可以实现跨机器几乎零成本部署。

# 用户名管理


https://www.scaler.com/topics/how-to-change-username-in-linux/

# CUDA相关
使用`nvidia-smi`查看信息：
https://www.microway.com/hpc-tech-tips/nvidia-smi_control-your-gpus/

Driver搜索(选择正确的显卡型号、对应版本并下载，上传到服务器上)：https://www.nvidia.com/Download/Find.aspx?lang=en-us
CUDA兼容和驱动的关系：https://docs.nvidia.com/deploy/cuda-compatibility/


升级前建议通过`ubuntu-drivers devices`检查设备版本兼容性。
[Reference:命令行方式安装nvidia显卡驱动](https://blog.csdn.net/u011119817/article/details/100520669)

一般通过[Reference:常规升级流程](https://www.cyberciti.biz/faq/ubuntu-linux-install-nvidia-driver-latest-proprietary-driver/)可以升级，即最简单的apt-get install。

如果遇到报错，或者出现nvidia-smi无法正常工作，进入下面流程：

[部分报错教程(在66上测试成功)](https://blog.csdn.net/sdnuwjw/article/details/110290280)
注意：重启后SSH需要2~3分钟才能恢复，不要紧张。

通过另一台主机的终端工具 ssh 登录 Ubuntu 系统，依次执行如下两条命令（设置系统默认进入终端命令模式，然后重启系统）
```shell
sudo systemctl set-default multi-user.target
sudo reboot 0
```
待系统重启后，通过其他主机的终端工具 ssh 登录 Ubuntu 系统，依次执行如下三条命令，卸载已安装的 NVIDIA 驱动后重启
```shell
sudo apt-get purge nvidia*
sudo apt-get autoremove
sudo reboot
```
待系统重启后，通过其他主机的终端工具 ssh 登录 Ubuntu 系统，执行如下两条命令（先进入 NVIDIA 驱动安装文件所在的目录，再安装驱动）
```
cd NVIDIA驱动安装文件所在的目录
sudo sh ./NVIDIA驱动安装文件.run
```
等待 NVIDIA 驱动安装完成并测试显卡正常识别和运行后，再在终端执行如下两条命令（设置系统默认进入图形化界面模式，重启系统）
```
sudo systemctl set-default graphical.target
sudo reboot 0
```
# 论文撰写
[将Excel表格直接转化为latex表格](https://tableconvert.com/excel-to-latex)

# 实验室杂事
## 出差报销
原则上所有出差学生都应该坐高铁出行，但如果高铁时间太长，可以选择比较便宜的飞机（不超过高铁二等座价格）。

若进行机票报销，发票需要开北京理工大学抬头，行程单开个人。

学校发票信息：
单位名称: 北京理工大学
纳税人识别号: 12100000400009127B
单位开户行: 中国工商银行北京紫竹院支行
单位银行账号: 0200007609014435495
开户行联行号：102100000763
预算代码: 124202
上级部门: 工业和信息化部
单位地址: 北京市海淀区中关村南大街5号
单位电话: 010-68914618