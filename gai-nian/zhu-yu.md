#### [aggregation](https://docs.influxdata.com/influxdb/v1.4/concepts/glossary/#aggregation)（聚合）

InfluxQL的聚合函数，用于返回一批数据点的合计数值，参考InfluxQL 函数查看所有的可用和即将上线的聚合函数列表。

#### batch（批）

使用换行（0x00）分隔的采用行协议格式的一组数据点。一批数据点可用一次HTTP请求写入到数据库，这样可以通过减少HTTP头来大量提升效率。推荐批处理的规模是5000-10000个数据点（不同应用场景可能更小或更大）

#### continuous query（CQ连续查询）

连续查询是在一个数据库内可以周期性自动执行的InfluxQL查询。连续查询在select语句中包含一个函数，并且必须包含一个GROUP BY time（）子句，参阅连续查询。

相关概念

