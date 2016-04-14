# YAML 简单总结

## 基本概念

### 档案

档案从独立的`---`行开始，直到独立的`...`行结束。

档案主要是为了方便流中处理 YAML。

### 字符串

字符串可以不使用双引号包裹，但这种情况下可能会被自动转成非字符串类型。

```yaml
str: "123"
num: 123
float: 12.3
bool: [True, Yes, yes, true]
```

等价 JSON：

```json
{
  "bool": [
    true, 
    true, 
    true, 
    true
  ], 
  "float": 12.300000000000001, 
  "num": 123, 
  "str": "123"
}
```

#### 保存新行

使用`|`来表示保存新行：

```yaml
text: |
    123
    456
```

等效 JSON：

```json
{
  "text": "123\n456"
}
```

#### 折叠新行

使用`>`来表示折叠新行：

```yaml
text: >
    123
    456
```

等效 JSON：

```json
{
  "text": "123 456"
}
```

### 列表

如果我们需要表示这样的一个 JSON 内容：

```json
[
  "Mary", 
  "Bill"
]
```

#### 区块格式

使用`-`前缀表示列表内容：

```yaml
- Mary
- Bill
```

#### 内置格式

使用类似 Python 列表的方式来表示：

```yaml
[Mary, Bill]
```

### 字典

如果我们需要表示这样的一个 JSON 内容：

```json
{
  "age": 24, 
  "name": "Mary"
}
```

#### 区块格式

使用`:`来分割键值对，左侧为键，右侧为值。

```
name: Mary
age: 24
```

#### 内置格式

使用类似 Python 字典的方式来表示：

```yaml
{name: Mary, age: 24}
```

### 混合使用列表和字典

如果我们需要表示这样的一个 JSON 内容：

```json
{
  "students": [
    {
      "age": 24, 
      "name": "Mary"
    }, 
    {
      "age": 23, 
      "name": "Bill"
    }
  ]
}
```

#### 区块格式

```yaml
students:
    - name: Mary
      age: 24
    - name: Bill
      age: 23
```

#### 内置格式

```yaml
students: [{name: Mary, age: 24}, {name: Bill, age: 23}]
```
## 高级用法

### 合并和参考

#### 锚点标记

在字典中可以使用`&`来标记一个锚点，以便后面的结点参考和字典合并：

```yaml
tag: &id-tag
```

#### 结点参考

在字典中使用`*`来引用一个锚点，相当于将指定的树状结构复制过来：

```yaml
tag-ref: *id-tag
```

#### 字典合并

使用特殊的键`<<`加上结点参考能够将指定的树状结构复制到当前层级，相当于继承，可以覆盖和添加键值：

```yaml
tag-inherit:
    >>: *id-tag
```

#### 混合使用

比如有个可以混合物理机和虚拟机组网的平台，可以使用以下 YAML 来表示结构：

```yaml
hypervisors:
    ip-10-21-1-10: &hypervisor-10-21-1-10
        name: ip-10-21-1-10
        ip: 10.21.1.10
        region: wuxi

    ip-10-21-1-11: &hypervisor-10-21-1-11
        name: ip-10-21-1-11
        ip: 10.21.1.11
        region: beijing

servers:
    - type: vm
      hypervisor: *hypervisor-10-21-1-11
      name: ip-10-21-2-10
      ip: 10.21.2.10

    - type: physical
      <<: *hypervisor-10-21-1-10
      hypervisor: *hypervisor-10-21-1-10
```

等效 JSON：

```json
{
  "hypervisors": {
    "ip-10-21-1-10": {
      "ip": "10.21.1.10", 
      "region": "wuxi", 
      "name": "ip-10-21-1-10"
    }, 
    "ip-10-21-1-11": {
      "ip": "10.21.1.11", 
      "region": "beijing", 
      "name": "ip-10-21-1-11"
    }
  }, 
  "servers": [
    {
      "hypervisor": {
        "ip": "10.21.1.11", 
        "region": "beijing", 
        "name": "ip-10-21-1-11"
      }, 
      "type": "vm", 
      "name": "ip-10-21-2-10", 
      "ip": "10.21.2.10"
    }, 
    {
      "ip": "10.21.1.10", 
      "region": "wuxi", 
      "type": "physical", 
      "name": "ip-10-21-1-10", 
      "hypervisor": {
        "ip": "10.21.1.10", 
        "region": "wuxi", 
        "name": "ip-10-21-1-10"
      }
    }
  ]
}
```

### 强制转型

对于字符串可以使用双引号来进行声明，也可以用`!!str`的方式来强制转型。

```yaml
num2float: !!float 123
str2int: !!int "12"
str2bool: !!bool "true"
float2str: !!str 12.3
```

等效 JSON：

```json
{
  "str2bool": true, 
  "float2str": "12.3", 
  "num2float": 123.0, 
  "str2int": 12
}
```

### 自定义类型

在一些特定的 YAML 解析器中允许自定义类型，使用`!Class`来表示：

```yaml
object: !Class {attr1: value1, attr2: value2}
```