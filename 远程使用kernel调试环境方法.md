#### 1. ssh进入Docker容器

````
cat >> ~/.ssh/config <<"EOT"
Host docker33113
    HostName 127.0.0.1
    Port 33113        
    User root
    ProxyCommand ssh rvlab -W %h:%p

Host rvlab
    HostName lab.rvperf.org
    Port 22
    User jean

"EOT"
````

执行ssh docker33113即可进入Docker容器

#### 2. 串口连接unmatched

可以使用picocom连接串口

````
$ apt install picocom
$ dmesg | grep tty
$ picocom -b 115200 /dev/ttyUSB1
````

#### 3. 访问openOCD

安装编译openOCD

````
$ apt install git
$ git clone https://git.code.sf.net/p/openocd/code openocd-code
$ apt install make libtool pkg-config
$ apt install autoconf automake texinfo
$ apt install libusb-1.0-0-dev
$ apt install libhidapi-dev
$ cd openocd-code
$./bootstrap
$./configure
$ make -j $(nproc)
$ make install
````

执行命令访问openOCD

````
$ openocd -f openocd.cfg
````

此时可以通过telnet连接本地的4444端口

````
$ apt install telnet
$ telnet localhost 4444
````

还可以使用gdb

````
$ apt install gdb
$ gdb
$ target remote 127.0.0.1:3333
$ info registers
````

#### 4. 使用tftp和nfs加载内核和文件系统

将需要存放到tftp server上的kernel image存放到docker容器的/home/tftpboot/目录下

将需要存放到nfs共享目录中的文件系统存放到docker容器的/home/nfsroot/目录下

启动unmatched，进入u-boot界面，设置环境变量后就可以通过tftp和nfs加载内核和文件系统了

#### 5. 远程开机/关机/重启

在docker容器中执行以下命令：

开机：curl -d '{"device": "24"}' -H 'Content-Type: application/json'  http://10.8.8.126:8080/poweron

关机：curl -d '{"device": "24"}' -H 'Content-Type: application/json'  http://10.8.8.126:8080/poweroff

重启：curl -d '{"device": "24"}' -H 'Content-Type: application/json'  http://10.8.8.126:8080/reset

其中{"device": "24"}对应的是unmatched编号
