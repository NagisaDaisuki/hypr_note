# 挂载Win下共享的文件夹到Arch

- 使用 **cifs** 的方案
- 在局域网内，Windows和Linux之间文件共享最通用、兼容性最好的协议是**SMB(Server Message Block)**。

通过**SMB**协议，可以将Windows的文件夹“挂载”(Mount) 到Archlinux的文件系统中。对Archlinux中的音乐播放器和其他文件管理器来说这些文件就像在本地硬盘上一样。

## 第一步：在Windows上设置共享文件夹

1. 开启共享：

- 找到要共享的文件夹。
- 右键点击 -> 属性 -> 共享 选项卡 -> 点击 共享(S)... 按钮。
- 在下拉菜单中选择你的用户（或者 Everyone，如果你不介意局域网其他人访问），点击“添加”。
- 权限级别设置为 “读取” 即可（保护你的文件不被误删）。如有需要设置为读取/写入
- 点击“共享”完成。

2. 获取Windows的IP地址：

- `Win+R`,输入`cmd`在cmd命令行内输入`ipconfig`
- 记下IPv4地址（例如`192.168.1.5`）

3. 确认Windows用户名和密码：

- 微软账户登录需要提供微软账户用户名（C:\Users\用户名）和微软账户密码（不是锁屏密码）
- 本地用户登录提供本地用户名和密码（一般是锁屏密码）

## 第二步：在Arch Linux上安装必要的工具

打开终端，执行：

~~~shell
sudo pacman -S cifs-utils
~~~

`cifs-utils`是Linux下处理SMB协议的核心工具

## 第三步：在ArchLinux上挂载文件夹

1. 创建挂载点（这里以挂载Win上共享的音乐文件夹为例）：

~~~shell
mkdir -p ~/Music/WinMusic
~~~

2. 获取当前用户的**UID**和**GID**(关键)：为了能读取挂载进来的文件需要指定挂载后的文件归属。
在终端输入`id`显示UID和GID。
3. 临时手动挂载（测试用）:

```shell
sudo mount -t cifs //Windows_ip/ShareFolderName ~/Music/WinMusic -o username=Windows用户名,password=微软账号密码/本地账户锁屏密码,uid=1000,gid=1000,iocharset=utf8
```

- `//Windows_ip/ShareFolderName`：Window的IP和共享文件夹名字（ip也可以替换为计算机名，但是Linux默认只通过DNS服务器解析域名，看不懂Windows局域网广播的“计算机名”）如果使用微软账户，用户名字段通常填写完整的邮箱地址。

> 在 Arch 上修改 `/etc/hosts` 文件将计算机名映射到计算机IP地址

~~~shell
# Static table lookup for hostnames.
# See hosts(5) for details.
127.0.0.1        localhost
::1              localhost
127.0.0.1        Kirifuji.localdomain   Kirifuji
# 新添加的host，以后连上不同局域网只要修改这里的ip就可以了
10.73.121.208    AKEBOSHI-HIMARI
~~~

## 第四步：设置开机自动挂载（推荐）

如果不想每次开机都敲命令，可以将其写入`/etc/fstab`

1. 创建一个凭证文件（为了安全不要把密码直接写在fstab里）

~~~shell
vim ~/.smbcredentials
写入：
username=Windows用户名
password=Windows密码
:wq保存退出
设置权限保护该文件：
chmod 600 ~/.smbcredentials
~~~

2. 编辑 `/etc/fstab`

在文件末尾添加一行

~~~shell
# 假如Win的IP是10.73.121.208 分享的文件夹名为CloudMusic arch用户名是Nagisa
//10.73.121.208/CloudMusic /home/Nagisa/Music/WinMusic cifs credentials=/home/Nagisa/.smbcredentials,uid=1000,gid=1000,iocharset=utf8,_netdev,x-systemd.automount 0 0
# 如果在上面设置过hosts那就可以直接写host指定的Win计算机名，那种更方便，不需要在ip变化时修改/etc/fstab只要修改/etc/hosts就可以了
//AKEBOSHI-HIMARI/CloudMusic /home/Nagisa/Music/WinMusic cifs credentials=/home/Nagisa/.smbcredentials,uid=1000,gid=1000,iocharset=utf8,_netdev,x-systemd.automount 0 0
~~~

- `_netdev`：告诉系统这是一个网络设备，要在网络联通后再挂载，防止开机卡死。
- `x-systemd.automount`：这是一个非常实用的参数。它允许系统在开机时不立即挂载，而是当你**第一次访问**该文件夹时自动挂载，对于笔记本或wifi连接不稳定的环境非常有用。
- 这里挂载的时候如果共享的文件夹名之间有空格比如*Cloud Music*需要在/etc/fstab里写成*Cloud\040Music*用占位符补充空格
- 整条配置必须写在同一行，中间不能回车换行。
- 如果遇到挂载报错可以尝试加上`vers=3.0`参数指定SMB协议版本。

> //AKEBOSHI-HIMARI/CloudMusic /home/Nagisa/Music/WinMusic cifs credentials=/home/Nagisa/.smbcredentials,uid=1000,gid=1000,iocharset=utf8,vers=3.0,_netdev,x-systemd.automount 0 0

3. 测试fstab：

执行：

~~~shell
sudo systemctl daemon-reload
sudo mount -a
~~~

如果没有报错说明配置正确，访问那个挂载点文件夹就可以访问Win文件了。

如果报错访问内核日志搜索分析。

~~~shell
sudo dmesg | tail
~~~
