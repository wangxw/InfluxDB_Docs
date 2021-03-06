InfluxDB是一个旨在处理高并发写入和查询负载的时序数据库，它是TICK框架的第二部分，influxdb用于任何包含大量时序数据应用的后台存储，包括Devops监控、应用指标数据、物联网传感器数据以及实时分析。

### 主要特征

如下为influxdb当前支持的主要特性，使其可以作为时序数据应用的一个很好选择。

* 专门针对时间序列数据写入而定制的高性能数据存储，TSM引擎支持高速存取和数据压缩。
* 完全go语言实现，编译为单个二进制文件，无需外部依赖。
* 简单、高效的写入和查询HTTP（S） API。
* 插件支持其他数据接入协议例如Graphite、collectd 以及OpenTSDB。
* 易于查询聚合数据的表达式类SQL查询语言。
* 标签支持序列索引以实现快速高效查询。
* 支持数据保留策略，可高效自动清理过期失效数据。
* 支持连续查询，自动计算聚合数据，使频繁查询更高效。
* 内置web管理接口。



