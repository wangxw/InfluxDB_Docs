安装InfluxDB后，你就可以开始做一些很棒的事情。在这一部分内容中，我们会使用influx命令行接口（CLI）来和数据库进行交互，所有influxDB安装包中均包含CLI，这是一个轻量级工具并且操作简单。CLI通过默认8086端口向InfluxDB HTTP API发送请求来直接和InfluxDB通信。

注意：数据库同样可以用在生成原始HTTP请求，参见以curl应用为案例的[Writing Data](file:////influxdb/v1.3/guides/writing_data/)和[Querying Data](file:////influxdb/v1.3/guides/querying_data/)部分。

新建数据库：

如果你已在本地安装了InfluxDB，就可以在命令行中使用influx命令了。执行influx会启动CLI并自动连接到本地InfluxDB实例（此处我们假设你已经通过运行service influxdb start或直接运行influxd命令启动了服务）。输出结果类似如下内容：

注意：

lInfluxDB HTTP API默认使用端口8086。因此，influx会默认连接到8086端口和本地。如果你需更改这些默认值，可执行influx –help

l-precision参数指定了返回时间戳的格式/精度。在上述案例中，rfc3339告诉InfluxDB以RFC3399格式返回时间戳（YYYY-MM-DDTHH:MM:SS.nnnnnnnnnZ）。

命令行会以influx查询语言（a.k.a InfluxQL）的形式来接收输入。通过输入exit并回车来退出influxQL shell。

刚安装的InfluxDB除了系统自带的\_internal外，没有任何数据库，所以我们首先要建一个。创建数据库的命令为CREATE DATABASE &lt;db-name&gt;，其中&lt;db-name&gt;是你所建数据库的名字。数据库命名可使用任意带双引号的unicode字符串。也可以采用不带引号的ASCII字母、数字、下划线组合，要求数据库名不能以数字开始。

在这份指南中，我们的数据库名字使用mydb。

注意：键入enter键后，除新一行的输入提示符外不会呈现任何信息。在CLI中，这就意味着语句被执行且无报错。只有当出现问题时才会展示错误信息。对CLI，没有消息就是好消息。

既然我们已经创建了数据库mydb，我们接下来使用SHOW DATABASES命令来显示所有已存在的数据库：

注意：数据库\_internal被用来存储内部运行时指标。之后你可以查看该数据库来发现一些关于InfluxDB在底层如何运行的有趣的东西。

除SHOW DATABASES外，绝大多数influxQL语句必须针对某一个特定的数据库。你可以在每一次查询操作时都指明相应数据库，也可以采用CLI提供的更便捷的语句USE &lt;db-name&gt;：执行该语句后，后续所有请求都将自动地针对该数据库进行操作。例如：

现在，接下来的命令都只针对mydb数据库。

写数据及查询：

既然我们有了数据库，那InfluxDB就可以接受查询和写操作了。

首先，简单介绍一下数据存储。在InfluxDB中的数据由“时序”组织，包含类似cpu\_负载、温度等可测量值。时序有0个到很多个points，每个points代表了度量中的一个离散样本。Points的组成部分为：time（时间戳）、measurement测量指标名（例如cpu负载）、至少一个key-valuefield（测量值本身，如“value=0.64”，或“temperature=21.2”），和0个到多个key-valuetags包含任何关于值的元数据（如“host=server01”、“region=EMEA”、“dc=Frankfurt”）。

概念上来讲，你可以把measurement想成是一个主索引都是时间的SQL表。Fields和tags是表中的列。tags编入索引，而Fields没有。与SQL表的区别在于，使用InfluxDB，你可以有成千上万的measurements，且不需要预先定义架构，并且空值是不被存储的。

Points通过Line Protocol写入InfluxDB，格式如下。

下面几行都是points写入InfluxDB的案例：

注意：你可以在[Writing Data](file:///C:/influxdb/v1.3/guides/writing_data/)页中看到更多关于Line protocol的信息。

在CLI中使用INSERT跟随一个point的方式来将一条单独的时序数据插入InfluxDB：

上面point的测量指标值measurement为cpu，标签tags为host和region，可度量的value是0.64

现在我们来查询一下刚写入的数据：

注意：当我们写入point时并没有提供一个时间戳。当未提供时间戳时，InfluxDB会在point存入时为其分配本地当前的时间戳。这意味着你的时间戳是有差别的。

让我们尝试存储另一种数据：在同一个measurement中有两个fields：

要想在一个查询里返回所有的fields和tags，可以使用操作符\*：

InfluxDB中还有许多这里没有覆盖到的特征和关键字，另外也没有讲对它Go-style正则的支持。如：

上面内容是关于写数据到InfluxDB中和查询数据时需要的所有基本信息。想要了解更多关于InfluxDB写协议，请查看[**Writing Data**](http://docs.influxdata.com/influxdb/v1.3/guides/writing_data/)指南。想要了解更多查询语言，请查看[**Querying Data**](http://docs.influxdata.com/influxdb/v1.3/guides/querying_data/)指南。想要查看更多关于InfluxDB概念的信息，请查看[**Key Concepts**](http://docs.influxdata.com/influxdb/v1.3/concepts/key_concepts/)页。

