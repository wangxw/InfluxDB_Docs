

本页提供安装、运行、配置InfluxDB方法。

### 安装要求

成功安装InfluxDB包需要获取root权限或管理员权限。

#### 网络要求

InfluxDB默认使用下列网络端口：

* TCP 8086端口：用作经由InfluxDB HTTP接口的客户端-服务器通信。
* TCP 8088端口：用作RPC服务的备份与还原。

除以上端口外，influxDB同样提供多种插件，这些插件可能需要自定义端口。所有的端口映射可以在配置文件中修改，默认安装时，配置文件位于：/etc/influxdb/influxdb.conf

#### NTP

InfluxDB使用主机的本地时间（UTC格式）来给数据分配时间戳，另外也是为了协调的目的。使用网络时间协议（NTP）来同步不同主机间的时间；如果主机时间和NTP不同步，写入InfluxDB的数据的时间戳就不精确。

### 安装

对于不想安装任何软件却准备使用InfluxDB的用户，可以查看我们的InfluxDB管理服务平台。

Ubuntu & Debian

请根据[下载页](https://influxdata.com/downloads/)中的指导学习如何从文件中安装Debian包。

Debian和Ubuntu用户可以使用apt-get软件包管理器来安装最新的InfluxDB稳定版。

Ubuntu用户可使用下述命令添加influxData版本库：

```
curl -sL https://repos.influxdata.com/influxdb.key | sudo apt-key add -
source /etc/lsb-release
echo "deb https://repos.influxdata.com/${DISTRIB_ID,,} ${DISTRIB_CODENAME} stable" | sudo tee /etc/apt/sources.list.d/influxdb.list
```



