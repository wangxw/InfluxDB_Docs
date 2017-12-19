InfluxDB的命令行接口（`influx`）是用作HTTP API的交互shell，使用influx命令可以写入数据（手动写入或从文件写入）、交互查询数据、以及以不同格式查看查询输出。

* [启动 ](#qi-do-ng)influx
* influx [参数](#influx-参数)
* influx [命令](#influx命令)

### 启动influx

如果你从安装包管理工具安装了InfluxDB，那么influx命令（CLI）被安装在了`/usr/bin/influx`\(或者`/usr/local/bin/influx` OS X系统下\)。

使用CLI接口，需要首先启动influxd数据库服务然后在终端中启动influx命令，如果成功启动shell并且连接到了InfluxDB节点，将有如下输出内容：

```
$ influx
Connected to http://localhost:8086 version 1.4.x
InfluxDB shell 1.4.x
```

> 提示**:** CLI和InfluxDB版本需要一致，否则会导致查询解析错误。

现在你可以在终端中输入influxQL查询语句了，可以使用help命令得到一个可用命令的列表，使用Ctrl+C来中止一个长时间运行的InfluxQL查询。

### Influx 参数

influx命令启动时可以指定一些参数，使用`$ influx --help`可以列出这些参数，如下是这些参数的简要说明，在本部分的最后我们提供了`-execute`,`-format`, `-import`三个参数的详细信息。

`-compressed`  
配合`-import`命令使用，表示导入文件是被压缩的。

`-consistency 'any|one|quorum|all'`  
设置数据一致性层次

`-database 'database name'`  
连接的数据库名

`-execute 'command'`  
指定执行的命令

`-format 'json|csv|column'`  
指定server端返回的数据格式。 参考[format](https://docs.influxdata.com/influxdb/v1.4/tools/shell/#specify-the-format-of-the-server-responses-with-format)

`-host 'host name'`

指定连接的host，默认连接的是localhost  
`-import`  
从文件导入数据或者从文件导入已有的数据库，参考[import](https://docs.influxdata.com/influxdb/v1.4/tools/shell/#import-data-from-a-file-with-import).

`-password 'password'`  
连接服务器的密码，如果留空\(`-password ''`\)将会提示输入密码，或者，设置CLI的环境变量`INFLUX_PASSWORD`

`-path`  
指定导入文件的路径（配合`-import`使用）。

`-port 'port #'`  
指定连接的端口，默认情况下， InfluxDB使用`8086`端口。

`-pps`

配合-import使用，指定每秒允许导入的points数量，默认情况下pps为0不限制导入速度。

`-precision 'rfc3339|h|m|s|ms|u|ns'`

指定时间戳的格式／精度：`rfc3339`\(格式是`YYYY-MM-DDTHH:MM:SS.nnnnnnnnnZ`\)，或者是`h`\(小时\)，`m`（分钟），`s`（秒），ms（毫秒），u\(微秒\)，ns（纳秒）。

默认的精度是纳秒。

> 提示**:**设置精度为`rfc3339`\(`-precision rfc3339`\)只 在`-execute`选项的时候有效, 但是在 `-import`选项的时候无效，所有其他的精度  \(例如`h`,`m`,`s`,`ms`,`u`, 和ns\) 在`-execute`和`-import`选项的时候都有效。

`-pretty`  
json数据格式打开pretty显示

`-ssl`  
使用https请求。

`-username 'username'`

指定连接的用户名，或者设置CLI的环境变量`INFLUX_USERNAME`  
`-version`  
显示influx的版本信息并退出。

#### 使用-execute直接执行influxQL命令

执行查询无需指定数据库：

```
$ influx -execute 'SHOW DATABASES'
name: databases
---------------
name
NOAA_water_database
_internal
telegraf
pirates
```

执行查询需指定数据库，并且指定时间戳精度：

```
$ influx -execute 'SELECT * FROM "h2o_feet" LIMIT 3' -database="NOAA_water_database" -precision=rfc3339
name: h2o_feet
--------------
time                           level description        location         water_level
2015-08-18T00:00:00Z     below 3 feet                santa_monica     2.064
2015-08-18T00:00:00Z     between 6 and 9 feet  coyote_creek  8.12
2015-08-18T00:06:00Z     between 6 and 9 feet  coyote_creek  8.005
```

#### 使用-format指定server 端返回的数据格式

默认的数据格式是列式：

```
$ influx -format=column
[...]
> SHOW DATABASES
name: databases
---------------
name
NOAA_water_database
_internal
telegraf
pirates
```

使用csv格式：

```
$ influx -format=csv
[...]
> SHOW DATABASES
name,name
databases,NOAA_water_database
databases,_internal
databases,telegraf
databases,pirates
```
使用json格式：
```
$ influx -format=json
[...]
> SHOW DATABASES
{"results":[{"series":[{"name":"databases","columns":["name"],"values":[["NOAA_water_database"],["_internal"],["telegraf"],["pirates"]]}]}]}
``` 
使用 json格式并打开-pretty输出选项
```
$ influx -format=json -pretty
[...]
> SHOW DATABASES
{
    "results": [
        {
            "series": [
                {
                    "name": "databases",
                    "columns": [
                        "name"
                    ],
                    "values": [
                        [
                            "NOAA_water_database"
                        ],
                        [
                            "_internal"
                        ],
                        [
                            "telegraf"
                        ],
                        [
                            "pirates"
                        ]
                    ]
                }
            ]
        }
    ]
}
```
####使用-import从文件导入数据
导入文件有两个部分：
* DDL（数据描述语言）：包含创建数据库和管理保留策略的influxQL语句，如果你的数据库和保留策略已经存在，那么导入文件可以省略这部分。
* DML（数据操作语言）：
 Lists the relevant database and (if desired) retention policy and contains the data in line protocol.

#### Influx命令



