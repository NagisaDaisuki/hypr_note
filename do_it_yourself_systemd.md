# 设置用户systemd启动服务和定时任务

## 使用 Systemd 用户服务来管理 Aria2 和定时任务

前提：下载Aria2c程序，准备aria2.conf配置文件和tracker自动更新文件

**下载Aria2c程序**

~~~shell
sudo pacman -S aria2
~~~

**aria2.conf配置文件（已经准备好tracker）**

~~~shell
## 文件保存设置 ##


# 默认下载路径
dir=/home/NagiChan/Downloads
# 启用磁盘缓存, 0为禁用, 针对机械硬盘可以设大一点(如 32M)
disk-cache=32M
# 文件预分配方式, 建议 falloc (NTFS建议用 prealloc)
file-allocation=falloc
# 断点续传
continue=true

## 进度保存设置 ##

# 读取会话
input-file=/home/NagiChan/.config/aria2/aria2.session
# 保存会话
save-session=/home/NagiChan/.config/aria2/aria2.session
# 定时保存会话(秒)
save-session-interval=60

## RPC 设置 (与浏览器交互的关键) ##

# 启用 RPC 服务
enable-rpc=true
# 允许所有来源, web界面跨域需开启
rpc-allow-origin-all=true
# 允许非外部访问
rpc-listen-all=true
# RPC 监听端口, 默认 6800
rpc-listen-port=6800
# RPC 密钥 (重要！请修改这个密码，浏览器插件里要填)
rpc-secret=AkeboshiHimariDaisuki

## BT/PT 下载设置 ##

# 自动做种时间(0为一直做种, 建议设为0或者适量时间回馈社区)
seed-time=0
# 启用本地节点查找
bt-enable-lpd=true
# 强制加密 (防运营商封锁)
bt-require-crypto=true
# 单个种子最大连接数
bt-max-open-files=100
# 全局最大下载速度 (0为不限制)
max-overall-download-limit=0
# 单任务最大下载速度
max-download-limit=0
# 开启 DHT (非 PT 下载必须开启)
enable-dht=true
# 开启 IPv6 DHT
enable-dht6=true

# 伪装成Chrome
user-agent=Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36

## Tracker 设置 (稍后通过脚本自动填充，这里留空即可) ##
bt-tracker=http://1337.abcvg.info:80/announce,http://aboutbeautifulgallopinghorsesinthegreenpasture.online:80/announce,http://bt.okmp3.ru:2710/announce,http://bvarf.tracker.sh:2086/announce,http://echostar.ddnsfree.com:8080/announce,http://ipv4.rer.lol:2710/announce,http://ipv6.rer.lol:6969/announce,http://lucke.fenesisu.moe:6969/announce,http://nyaa.tracker.wf:7777/announce,http://open.tracker.cl:1337/announce,http://tk.greedland.net:80/announce,http://torrentsmd.com:8080/announce,http://tr.highstar.shop:80/announce,http://tracker.beeimg.com:6969/announce,http://tracker.dhitechnical.com:6969/announce,http://tracker.mywaifu.best:6969/announce,http://tracker.renfei.net:8080/announce,http://www.all4nothin.net:80/announce.php,http://www.wareztorrent.com:80/announce,https://1337.abcvg.info:443/announce,https://shahidrazi.online:443/announce,https://t.213891.xyz:443/announce,https://torrent.tracker.durukanbal.com:443/announce,https://tr.abiir.top:443/announce,https://tr.abir.ga:443/announce,https://tr.nyacat.pw:443/announce,https://tracker.alaskantf.com:443/announce,https://tracker.foreverpirates.co:443/announce,https://tracker.ghostchu-services.top:443/announce,https://tracker.kuroy.me:443/announce,https://tracker.moeblog.cn:443/announce,https://tracker.moeking.me:443/announce,https://tracker.pmman.tech:443/announce,https://tracker.qingwa.pro:443/announce,https://tracker.uraniumhexafluori.de:443/announce,https://tracker.zhuqiy.com:443/announce,https://tracker1.520.jp:443/announce,udp://anna.bt.bontal.net:6969/announce,udp://d40969.acod.regrucolo.ru:6969/announce,udp://extracker.dahrkael.net:6969/announce,udp://martin-gebhardt.eu:25/announce,udp://node02.torrentonline.cc:42069/announce,udp://ns575949.ip-51-222-82.net:6969/announce,udp://open-tracker.demonoid.ch:6969/announce,udp://open.demonii.com:1337/announce,udp://open.demonoid.ch:6969/announce,udp://open.stealth.si:80/announce,udp://opentor.org:2710/announce,udp://opentracker.io:6969/announce,udp://p4p.arenabg.com:1337/announce,udp://public.demonoid.ch:6969/announce,udp://retracker.lanta.me:2710/announce,udp://retracker01-msk-virt.corbina.net:80/announce,udp://run.publictracker.xyz:6969/announce,udp://t.overflow.biz:6969/announce,udp://torrentclub.online:54123/announce,udp://tracker-de-2.cutie.dating:1337/announce,udp://tracker.0x7c0.com:6969/announce,udp://tracker.1h.is:1337/announce,udp://tracker.bittor.pw:1337/announce,udp://tracker.dler.com:6969/announce,udp://tracker.ducks.party:1984/announce,udp://tracker.filemail.com:6969/announce,udp://tracker.flatuslifir.is:6969/announce,udp://tracker.fnix.net:6969/announce,udp://tracker.gmi.gd:6969/announce,udp://tracker.opentorrent.top:6969/announce,udp://tracker.opentrackr.org:1337/announce,udp://tracker.playground.ru:6969/announce,udp://tracker.qu.ax:6969/announce,udp://tracker.skyts.net:6969/announce,udp://tracker.srv00.com:6969/announce,udp://tracker.t-1.org:6969/announce,udp://tracker.theoks.net:6969/announce,udp://tracker.torrent.eu.org:451/announce,udp://tracker.tryhackx.org:6969/announce,udp://tracker1.myporn.club:9337/announce,udp://tracker1.t-1.org:6969/announce,udp://tracker2.dler.org:80/announce,udp://tracker3.t-1.org:6969/announce,udp://www.torrent.eu.org:451/announce,wss://tracker.openwebtorrent.com:443/announce
~~~

在准备好这些文件后只需要做三件事：

1. 安置脚本：把脚本和配置文件放在指定的位置(一般放在配置文件目录下)
2. 创建Aria2服务：开机自动后台运行。
3. 创建脚本定时器服务：利用Systemd Timer每周执行一次更新Tracker

**第一步：**
将上面的文件放在`~/.config/aria2`目录下。

~~~shell
ls ~/.config/aira2
aria2.conf tracker_update.sh*  
~~~

**第二步：** 创建Aria2主服务程序

创建目录（如果不存在）：

~~~shell
mkdir -p ~/.config/systemd/user/
~~~

创建并编辑`aria2.service`文件：

~~~shell
nvim ~/.config/systemd/user/aria2.service
~~~

服务文件内容

~~~shell
[Unit]
Description=Aria2c Download Manager
After=network.target filesystem.target

[Service]
# %h 代表你的 Home 目录
ExecStart=/usr/bin/aria2c --conf-path=%h/.config/aria2/aria2.conf
# 确保 Aria2 进程意外退出后会自动重启
Restart=on-failure
# 启动类型，aria2c 默认前台运行适合 simple
Type=simple

[Install]
WantedBy=default.target
~~~

**第三步：** 创建更新Tracker的服务与定时器

创建并编辑`aira2-tracker-updater.service`文件

~~~shell
nvim ~/.config/systemd/user/aria2-tracker-updater.service
~~~

服务文件内容

~~~shell
[Unit]
Description=Run Aria2 Tracker Update Script

[Service]
Type=oneshot
# 指向你存放 update-tracker.sh 的路径
ExecStart=%h/.config/aria2/update-tracker.sh
~~~

创建并编辑`aria2-tracker-updater.timer`文件

~~~shell
nvim ~/.config/systemd/user/aria2-tracker-updater.timer
~~~

服务文件内容

~~~shell
[Unit]
Description=Weekly Update for Aria2 Trackers

[Timer]
# 每周运行一次 (默认是周一凌晨，也可以写 "Sat *-*-* 00:00:00" 指定周六)
OnCalendar=weekly
# 如果电脑关机错过了时间，开机后立即补跑一次
Persistent=true
# 开机后延迟 5 分钟再跑，避免刚开机抢占网络资源
OnBootSec=5min

[Install]
WantedBy=timers.target
~~~

**第四步：** 启动并运行

i. 重载配置（让systemd读取新文件）：

~~~shell
systemctl --user daemon-reload
~~~

ii. 启动并设置Aria2开机自启动：

~~~shell
systemctl --user enable --now aria2
~~~

iii. 启动并设置定时器开机自启动：

~~~shell
systemctl --user enable --now aria2-tracker-updater.timer
~~~

> 这里只要enable timer 不需要 enable service文件,timer 会去唤醒Service

**第五步：**验证是否成功

1. 查看Aria2是否在运行：

```shell
systemctl --user status aria2
```

1. 查看定时器是否设定成功：

```shell
systemctl --user list-timers --all
```

1. 手动测试一下更新脚本：

```shell
systemctl --user start aria2-tracker-updater.service
```

查看运行日志：

```shell
journalctl --user -u aria2-tracker-updater -e
```

配置完成后，你的 Arch Linux 将会：

- 用户登录后自动后台启动 Aria2。

- 每周自动运行一次 update-tracker.sh。

- 脚本运行完后会自动重启 aria2 服务（因为你的脚本里写了 systemctl --user restart aria2，这与我们在第二步创建的服务名是一致的，完美闭环）。
