本页提供安装、运行、配置InfluxDB方法。

## 安装要求

成功安装InfluxDB包需要获取root权限或管理员权限。

#### 网络要求

InfluxDB默认使用下列网络端口：

* TCP 8086端口：用作经由InfluxDB HTTP接口的客户端-服务器通信。
* TCP 8088端口：用作RPC服务的备份与还原。

除以上端口外，influxDB同样提供多种插件，这些插件可能需要自定义端口。所有的端口映射可以在配置文件中修改，默认安装时，配置文件位于：/etc/influxdb/influxdb.conf

#### NTP

InfluxDB使用主机的本地时间（UTC格式）来给数据分配时间戳，另外也是为了协调的目的。使用网络时间协议（NTP）来同步不同主机间的时间；如果主机时间和NTP不同步，写入InfluxDB的数据的时间戳就不精确。

## 安装

对于不想安装任何软件却准备使用InfluxDB的用户，可以查看我们的InfluxDB管理服务平台。

### Ubuntu & Debian

请根据[下载页](https://influxdata.com/downloads/)中的指导学习如何从文件中安装Debian包。

Debian和Ubuntu用户可以使用apt-get软件包管理器来安装最新的InfluxDB稳定版。

Ubuntu用户可使用下述命令添加influxData版本库：

```
curl -sL https://repos.influxdata.com/influxdb.key | sudo apt-key add -
source /etc/lsb-release
echo "deb https://repos.influxdata.com/${DISTRIB_ID,,} ${DISTRIB_CODENAME} stable" | sudo tee /etc/apt/sources.list.d/influxdb.list
```

Debian用户添加InfluxData版本库的命令：



```
curl -sL https://repos.influxdata.com/influxdb.key | sudo apt-key add -
source /etc/os-release
test $VERSION_ID = "7" && echo "deb https://repos.influxdata.com/debian wheezy stable" | sudo tee /etc/apt/sources.list.d/influxdb.list
test $VERSION_ID = "8" && echo "deb https://repos.influxdata.com/debian jessie stable" | sudo tee /etc/apt/sources.list.d/influxdb.list
test $VERSION_ID = "9" && echo "deb https://repos.influxdata.com/debian stretch stable" | sudo tee /etc/apt/sources.list.d/influxdb.list
```

然后，安装并启动InfluxDB服务：

```
sudo apt-get update && sudo apt-get install influxdb
sudo service influxdb start
```

如果你的操作系统使用的是systemd（Ubuntu 15.04版本以上，Debian 8以上），则使用下述命令：

```
sudo apt-get update && sudo apt-get install influxdb
sudo systemctl start influxdb
```

### Red Hat & CentOS

请根据下载页中的指导学习如何从文件中安装RPM包。

Red Hat和CentOS用户能够使用yum软件包管理器来安装最新的influxDB稳定版：

```
cat <<EOF | sudo tee /etc/yum.repos.d/influxdb.repo
[influxdb]
name = InfluxDB Repository - RHEL \$releasever
baseurl = https://repos.influxdata.com/rhel/\$releasever/\$basearch/stable
enabled = 1
gpgcheck = 1
gpgkey = https://repos.influxdata.com/influxdb.key
EOF
```

将版本库添加到yum配置文件中后，安装并启动influxDB服务，命令如下：

```
sudo yum install influxdb
sudo service influxdb start
```

如果你的操作系统使用的是systemd（CentOS 7+，RHEL 7+），则使用下述命令：

```
sudo yum install influxdb
sudo systemctl start influxdb
```

### SLES & openSUSE

openSUSE构建服务为SUSE Linux用户提供了RPM安装包：

```
# add go repository
zypper ar -f obs://devel:languages:go/ go
# install latest influxdb
zypper in influxdb
```

### FreeBSD/PC-BSD

InfluxDB是FreeBSD软件包系统的一部分，可以通过如下命令安装：

```
sudo pkg install influxdb
```

配置文件位于/usr/local/etc/influxd.conf；示例位于/usr/local/etc/influxd.conf.sample。

通过执行下面命令后台启动服务：

```
sudo service influxd onestart
```

系统运行时启动InfluxDB方法，在/etc/rc.conf文件中添加influxd\_enable="YES"。

### MAC OS X

OS X 10.8及更高版本的用户可以使用Homebrew软件包管理器来安装InfluxDB。安装brew后，你可以通过下述命令安装InfluxDB：

```
brew update
brew install influxdb
```

使用launchd在登录时启动InfluxDB，运行：

`ln -sfv /usr/local/opt/influxdb/*.plist ~/Library/LaunchAgents`

立即启动InfluxDB，运行：

```
launchctl load ~/Library/LaunchAgents/homebrew.mxcl.influxdb.plist
```

如果你不想或不需要使用launchtl，你可以在一个独立的终端窗口中运行：

```
influxd -config /usr/local/etc/influxdb.conf
```

## 配置

系统对每个配置文件设置都有内部默认值，使用influxd config命令可查看默认配置设置。

本地配置文件（/etc/influxdb/influxdb.conf）中大多数设置均被注释掉了；所有被注释掉的设置都取值于内部默认值。本地文件中未被注释掉的设置会覆盖重写内部默认值。请注意，本地配置文件无需包含每一项配置设置。

有两种方法可以通过配置文件来启动InfluxDB：

* 使用-config选项将进程指向正确的配置文件：

```
influxd -config /etc/influxdb/influxdb.conf
```

* 设置环境变量值INFLUXDB\_CONFIG\_PATH为配置文件路径，并启动进程。举例：

```
echo $INFLUXDB_CONFIG_PATH
/etc/influxdb/influxdb.conf


influxd
```

InfluxDB 首先会检查-config选项，接着去取环境变量。

更多信息请查看文档“配置”部分。  


## AWS上的虚拟主机服务

### 硬件

我们推荐使用两块SSD卷。一款用作influxdb/wal，一块用作influxdb/data。根据您负载情况，每卷应预分配1k-3k IOPS。influxdb/data卷应设置低IOPS及多磁盘空间，而influxdb/wal卷应设置高IOPS及较少磁盘空间。

每台机器建议8G以上RAM。

我们已经看到，机器在使用R4时会有更好的性能，因其比C3/C4和M4提供更大内存。

### 配置实例

下面的例子是假设您使用了两块SSD卷并正确挂载，假设每个卷挂载在/mnt/influx和/mnt/db上。获取更多配置操作信息，可参考亚马逊文档[《**Add a Volume to Your Instance**》](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ebs-attaching-volume.html)。

### 配置文件

你需要为你的每个InfluxDB实例正确更新配置文件。

```
...

[meta]
  dir = "/mnt/db/meta"
  ...

...

[data]
  dir = "/mnt/db/data"
  ...
wal-dir = "/mnt/influx/wal"
  ...

...

[hinted-handoff]
    ...
dir = "/mnt/db/hh"
    ...
```

### 权限

当InfluxDB数据和配置文件使用非标准目录时，一定要正确设置文件系统权限，方法如下：

```
chown influxdb:influxdb /mnt/influx
chown influxdb:influxdb /mnt/db
```





