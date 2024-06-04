实验室共享指南，节省同学们奔走相告搜集信息的时间。主要内容包括**服务器配置方法和使用指引**、**科研生产力工具**推荐等。

如果你有更多内容希望添加到这里，欢迎联系叶语霄或方辰获取编辑权限~其他主要贡献者：戴子彭，王昊，赵一诺

原文链接：https://hackmd.io/Aohp4vZ0QYGEkNsErb0S2Q

# HackMD 本身

水墨屏必须用浅色背景，可以在编辑器右下角找到画笔🖌图标，进行修改。对于LCD显示屏，直接更改回去即可。

# 周期运行小工具
使用crontab可以在Unix-like的操作系统上周期性运行某些脚本、程序等等，用于进行记录。
https://www.runoob.com/linux/linux-comm-crontab.html

附：这也是病毒自动重启的方法[Doge]

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

## Conda
### Configure envs

conda最佳的设置是所有机器只有一个conda主程序，然后其他用户分别设置自己的环境。如果转移了登录用户，而原始的环境存在user目录底下，那么可以通过修改`CONDA_ENVS_PATH`实现定位。将PATH添加到`bashrc`或`zshrc`中实现永久链接。

```
export CONDA_ENVS_PATH=<old_path>
```
如果主磁盘空间不够，需要移动安装包路径，则会复杂一些，需要参照
https://stackoverflow.com/questions/67610133/how-to-move-conda-from-one-folder-to-another-at-the-moment-of-creating-the-envi
### Installation
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

设置自动挂载（未实操，有待检验）：
edit /etc/fstab
通过`df -h`和`blkid`收集磁盘的UUID和Type，然后按照格式填写到/etc/fstab中
```
UUID=uuid-of-sda1  /data1  filesystem-type-sda1  defaults  0  2
UUID=uuid-of-sda2  /data2  filesystem-type-sda2  defaults  0  2
```
重启前使用`sudo mount -a`检验正确性。

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

wandb自带有“Report”功能，可生成直接展示实验数据窗口的动态报告，方便直观，非常适合持久化记录某一组对比实验。

要注意Report功能bug比较多:
- 在实验选择页面不能使用剪切快捷键，否则会把整个实验Panel剪切走，再次粘贴时机会崩溃。
- 偶尔展开带有实验数据表的标题会出现崩溃。

好在这些bug除了造成小幅度时间损耗以外，没有其他副作用，Report有自动保存，每次崩溃后找到Reports，光标滑过显示Draft，继续编辑即可。

![Screen Shot 2024-02-23 at 22.56.01](https://hackmd.io/_uploads/H1yGfNIna.png)
## 查看实验轨迹
如果轨迹图可以渲染为html文件，一个很好的可视化方法是将服务器变为一个HTTP Server（并且自带压缩传输，性能更佳）:
```
# install gzip server if you don't have one
pip install httpcompressionserver 
# run this directly in the terminal
python3 -m httpcompressionserver --bind 0.0.0.0 
```
# Windows常用工具

磁盘清理可以处理临时文件、驱动程序的清理。Windows搜索处直接敲“磁盘清理”或者“Disk Cleanup”。

# linux常用命令

> 所有linux命令都可以问gpt！

## 文件权限
```
# change the owner of all files under current directory recursively.
chown -R <uid>:<gid> *
```

## SSH服务
```
# restart SSH
service sshd restart
# get SSH Config Profile
vim /etc/ssh/sshd_config
```
## 系统管理操作
### 修改用户名正确操作
https://www.networkworld.com/article/969308/how-to-modify-user-account-settings-with-usermod.html
```
# logout user from root
pkill -u username
# force logout, used when pkill -u is not successful.
pkill -KILL -u {username}
#### WARNING, DO NOT DO THIS!!! ####
pkill -KILL -u root
# change user name and home directory
sudo usermod -l new_username old_username
sudo usermod -d /home/new_username -m new_username
# or in single command line (not tested)
sudo usermod -l new_username -d /home/new_username -m old_username
# check existing users
getent passwd [UID]
```

### 重启服务器

```
sudo reboot
```

如果admin用户有root权限，可以直接在liuchi用户下进行root操作，没必要以omnisky用户登录。（再通过exit返回普通用户）
注意，只有管理有可以做到这一点，其他用户没有进入root用户的权限。目前所有普通用户是无权重启的（随意重启可能打乱其他人的实验进度），如有重启需求，请联系管理员。

```
sudo -i
```
### (警告)sudoer修改
请**务必**通过visudo修改，这是基本安全操作，禁止一切通过不带任何命令来修改`/etc/sudoer`（比如`sed`命令）一旦出现下面两种情况：
- 有语法错误
- 全部文件内容被删除

sudo权限将被死锁，无法使用，没有任何用户可以绕开（root也不行），只能进入恢复模式重新编辑文件。

#### 基本语法（GPT生成）

The `/etc/sudoers` file is the configuration file for the `sudo` command on Unix-like systems. It controls who can run what commands as what users on which machines, and with what privileges. Here's a brief overview of its syntax:

1. **User Specification**: Each line typically consists of a user specification, specifying who is allowed to execute commands. It has the following format:
   ```
   user host=(runas) commands
   ```

   - `user`: The user or group allowed to run the commands.
   - `host`: The hostname or IP address of the system where the commands can be run. Use `ALL` to allow on any host.
   - `runas`: The user the commands can be run as. Use `ALL` to allow running as any user.
   - `commands`: The commands that the user can run. Use `ALL` to allow running any command.

2. **Special Tokens**:
   - `ALL`: Represents all possible values for users, hosts, or commands.
   - `NOPASSWD`: Specifies that the user does not need to enter a password to execute the specified commands.
   - `!`: Indicates negation.

3. **Comments**: Lines starting with `#` are treated as comments and are ignored.

4. **Defaults**: The `Defaults` directive allows you to set default options for `sudo`. For example:
   ```
   Defaults        env_reset
   ```

   This sets the `env_reset` option, which resets the environment to a default set of variables before executing commands.

5. **Aliases**: You can define aliases for users, hosts, commands, and more using the `User_Alias`, `Host_Alias`, `Cmnd_Alias`, and other directives.

6. **Include Directive**: The `#includedir` directive allows including additional configuration files from a directory. This is useful for modularizing the sudoers configuration.

## 资源

- **查看GPU使用情况**

```
nvidia-smi
```

- **查看CPU使用情况**

```
top
# check top by user
top -u <username>
# press 'C'(Command) to show detailed command line argument.
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

8. **-h**：human-readable，以KB，MB，GB为单位，提高信息的可读性。

   

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
# LINC 仿真系统
## Unreal Engine
./Setup.sh需要加--force才能在Linux上使用。

# LINC PPT制作美学
## 主要成果
写主要成果的时候，注意一定要“简练”
1. 除非某个数学公式极其重要，一般不写，写出来的数学公式要讲清楚每个字母的含义，整个公式的物理意义。
2. 写清楚业界SOTA是什么(方法名称，发表在哪)，写清楚他们有什么问题，写清楚你的方法到底“核心”创新在哪，改进了哪里
3. 要展示效果，最多一张图，挑最有代表性的一张，对比方法应该是前面你自己写的SOTA，故事有头有尾。

**WARNING: 以下是错误示范**
![402351711026417_.pic](https://hackmd.io/_uploads/BkLE7nFA6.png)
1. 左侧公式含义不清
2. 业界SOTA没有指明存在问题；自己的方法没有指出核心创新
3. 展示图像过多

**正确示范**

# 如何汇报

- 多画图，善用腾讯会议中的画板。
- 老师容易忘记过去汇报的内容，请在本次汇报时简单回顾自己过去提到的内容。
- 一篇可以达到老师标准的文章一般有以下特点（按优先级排序）：
    - 主方法效果足够好
    - 有能在学术界留下痕迹的东西（包括首个做某问题/场景的；定义新System Model或Metrics的；构建一个Framework的；证明Bound的）
    - 有说得过去的仿真系统
# LINC差旅会务
## 鉴定会/启动会标准流程

### 会议前
！请比老师提前到达会场。
- 桌椅排布：大屏幕两侧分别排两组长桌，一侧为评审专家，另一侧为项目组成员。可视桌椅情况摆放“回”字型。
- 摆放物品：每组长桌上摆放矿泉水、茶水、会议材料、酒店记录纸张以及桌签，三个话筒。
    - 桌签应数量对等，按照“领导C位，左右排开”原则摆放。专家组名单的第一位即C位，C位的左边是第二位，右边是第三位，以此类推。注意，“左”是坐在领导桌侧时的左。
    - 桌上材料若太少（如只有2份），应尽可能打印其他材料。
    - 所有物品应紧凑排布，保证一人材料是一堆。
    - 三个话筒，一个话筒给专家组，两个话筒给项目组。
- 横幅一般悬挂在专家评审长桌一侧，应提前找人站位测量横幅高度是否合适。
- 若有副屏，应摆放在大屏幕旁边，展示一张背景深色（如暗红色）且带有项目支持机构、项目名称的静态PPT。
- 若有讲台，两种选择：艺术型讲台，或带有项目牌子的普通讲台。如果无法满足，不要摆放。
- 电脑一般采用Windows，需要良好PPT播放功能，支持打印机无线或有线连接，以供打印评审意见；请在开始会议之前关闭所有社交软件、提醒事项和闹钟等。


### 会议中
- 建议开会的时候开上手机录音，方便（用AI）整理成会议纪要，后续项目验收和老师问起也有材料可说。
- 编写鉴定会材料时，请将Word文字充满屏幕，放到最大。

### 会议后
- 会后可能会要求写新闻稿，具体范例可参照repo中上传PDF文件。一般的新闻稿分为开头、项目简介、与会嘉宾、领导发言、项目咨询六个部分。
- 在与会嘉宾中，主要包含出席领导+咨询专家具体职务和姓名，以及主持人的职务和姓名。其他人员一般带过。
- 一般全文只有一张图片时，紧靠着标题放。否则可以图文并茂排版。
- 在领导发言中，几个关键领导的职责主要是：
    - 科发院领导，一般是学校项目管理的相关政策、进展汇报等
    - 项目主管单位领导，一般是项目管理政策的宣读、提醒
    - 上级单位领导，如教育部领导、科技部领导、学院书记/院长，一般是对项目启动表达祝贺，激励号召等

## 项目运行

### 项目Leader职责
应熟悉的材料：合同/任务书，答辩PPT，考核指标（几篇论文、几个软著、几个专利，有没有仿真系统），关键交付物和时间，人员分工。所有这些材料应由Leader自行找单独文件夹收纳。

项目不同板块的研究内容要明确是谁负责，后续找人要材料。

## 报销和保障

### 旅行报销
报销一共有两种途径，正常发票报销和劳务费报销。劳务费报销一般处理不可直接报销的材料（出租、餐饮、会员费等），不需要发票。正常发票报销则需要发票、支付截图全齐才可以报销。

发票需要开北京理工大学抬头，开票开**普通票**即可，不用增值税。

所有纸质发票、火车票报销凭证需要粘贴到[发票贴纸单](xxx)后交给老师，电子发票统一打印，同时还要附上支付证明。

学校发票信息：
```
单位名称: 北京理工大学
纳税人识别号: 12100000400009127B
单位开户行: 中国工商银行北京紫竹院支行
单位银行账号: 0200007609014435495
开户行联行号：102100000763
预算代码: 124202
上级部门: 工业和信息化部
单位地址: 北京市海淀区中关村南大街5号
单位电话: 010-68914618
```

#### 劳务费报销
出租车、餐饮费一般直接报总数即可，由老师直接转账给负责人。刘老师也有可能会要一个车票、酒店、出租、餐饮的总价。

#### 交通报销
原则上所有出差学生都应该坐高铁出行，但如果高铁时间太长，可以选择比较便宜的飞机（不超过高铁二等座价格）。机票需要电子行程单，火车票需要报销凭证（车票）。请务必在出发地或目的地机场/车站打印报销凭证，未安检之前就需要取。

如果忘记打印，可以在中教一层的机器刷身份证取出（机器经常坏..(；′⌒`)，所以建议在车站完成）

#### 酒店报销
通过酒店下单可到酒店开具发票；如果是携程下单，酒店前台不一定能直接开票，因此离开之前至少一天一定要在酒店前台问一下。

#### 会议报销
所有会议的注册费报销都需要邀请函，一般可通过官网、现场工作人员索要等渠道拿到。

如果是国际会议，需要参考[北京理工大学国际交流处](https://international.bit.edu.cn/ygcg/index.htm)，具体细节可联系该页面左下角徐海涛老师。

**注意：务必在出发前至少14天前开始走校内流程，必须在出发前拿到国际交流处的因公出国批件，否则费用无法报销。**

### 文章发表报销

专利；

发表付费...
