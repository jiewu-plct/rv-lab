#### 1. ssh进入Docker容器

````
cat >> ~/.ssh/config <<"EOT"
Host docker33113
    HostName 127.0.0.1
    Port 33113        
    User tester
    ProxyCommand ssh rvlab -W %h:%p

Host rvlab
    HostName lab.rvperf.org
    Port 22
    User jean

"EOT"
````

其中：

Host docker33113中

Port 33113：ssh可以访问的端口

User testter：登录容器账号的用户名

Host rvlab中

User jean：ssh可以访问的账号

执行ssh docker33113即可进入Docker容器

#### 2. 串口连接unmatched

可以使用picocom连接串口

````
$ sudo apt install picocom
$ dmesg | grep tty
$ sudo picocom -b 115200 /dev/ttyUSB1
````

#### 3. 访问openOCD

安装编译openOCD

````
$ sudo apt install git
$ git clone https://git.code.sf.net/p/openocd/code openocd-code
$ sudo apt install make libtool pkg-config
$ sudo apt install autoconf automake texinfo
$ sudo apt install libusb-1.0-0-dev
$ sudo apt install libhidapi-dev
$ cd openocd-code
$./bootstrap
$./configure
$ sudo make -j $(nproc)
$ sudo make install
````

执行命令访问openOCD

````
$ sudo openocd -f openocd.cfg
````

此时可以通过telnet连接本地的4444端口

````
$ sudo apt install telnet
$ telnet localhost 4444
````

还可以使用gdb

````
$ sudo apt install gdb
$ gdb
$ target remote 127.0.0.1:3333
$ info registers
````

#### 4. 使用tftp和nfs加载内核和文件系统

将需要存放到tftp server上的kernel image存放到通过ssh登录的docker容器的/home/tftpboot/目录下

将需要存放到nfs共享目录中的文件系统存放到通过ssh登录的docker容器的/home/nfsroot/目录下

启动unmatched，进入u-boot命令行界面，设置环境变量后就可以通过tftp和nfs加载内核和文件系统了

如果unmatched的T-Flash卡已经烧了官方的ubuntu image，开机后需要按任意键方可进入u-boot命令行界面

#### 5. 远程开机/关机/重启

在docker容器中执行以下命令：

````
$ curl -d '{"device":"67"}' -H 'Content-Type: application/json' http://10.8.8.123:8080/poweron //开机
$ curl -d '{"device":"67"}' -H 'Content-Type: application/json' http://10.8.8.123:8080/poweroff //关机
$ curl -d '{"device":"67"}' -H 'Content-Type: application/json' http://10.8.8.123:8080/reset   //重启
````

其中{"device": "67"}对应的是unmatched编号，http://10.8.8.123:8080是控制远程开机/关机/重启的server ip和port

#### 6. 设备和账号信息

| D1 IP      | Dokcer容器账号（用户名/密码) | Unmatched IP | Unmached NO. |
| ---------- | ---------------------------- | ------------ | ------------ |
| 10.8.8.113 | tester/rvlab->kernel067      | 10.8.8.117   | 67           |
| 10.8.8.114 | tester/rvlab->kernel068      | 10.8.8.118   | 68           |

控制远程开机/关机/重启的server ip和port: http://10.8.8.123:8080

