# 通用配置

## 核心概念

* Row 是Waterdrop逻辑意义上一条数据，是数据处理的基本单位。在Filter处理数据时，所有的数据都会被映射为Row。

* Field 是Row的一个字段。Row可以包含嵌套层级的字段。

* raw_message 指的是从input输入的数据在Row中的`raw_message`字段。

* __root__ 指的是Row的最顶级的字段相同的字段层级，常用于指定数据处理过程中生成的新字段在Row中的存储位置(top level field)。


---

## 配置文件

一个完整的Waterdrop配置包含`spark`, `input`, `filter`, `output`, 即：

```
spark {
    ...
}

input {
    ...
}

filter {
    ...
}

output {
    ...
}

```

* `spark`是spark相关的配置，

可配置的spark参数见：
[Spark Configuration](https://spark.apache.org/docs/latest/configuration.html#available-properties),
其中master, deploy-mode两个参数不能在这里配置，需要在Waterdrop启动脚本中指定。

* `input`可配置任意的input插件及其参数，具体参数随不同的input插件而变化。

* `filter`可配置任意的filter插件及其参数，具体参数随不同的filter插件而变化。

filter中的多个插件按配置顺序形成了数据处理的pipeline, 上一个filter的输出是下一个filter的输入。

* `output`可配置任意的output插件及其参数，具体参数随不同的output插件而变化。

`filter`处理完的数据，会发送给`output`中配置的每个插件。


---

## 配置文件示例

一个示例如下：

```
spark {
  # Waterdrop defined streaming batch duration in seconds
  spark.streaming.batchDuration = 5

  spark.app.name = "Waterdrop"
  spark.ui.port = 13000
}

input {
  socket {}
}

filter {
  split {
    fields = ["msg", "name"]
    delimiter = ","
  }
}

output {
  stdout {}
}
```