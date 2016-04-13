# YAML 简单总结

## 基本概念

### 档案

档案从独立的`---`行开始，直到独立的`...`行结束。

档案主要是为了方便流中处理 YAML。

### 字符串

字符串不需要使用引号包裹。

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

### 清单

如果我们需要表示这样的一个 JSON 内容：

```json
[
  "MAry", 
  "Bill"
]
```

#### 区块格式

使用`-`前缀表示清单内容：

```yaml
- Mary
- Bill
```

#### 内置格式

使用类似 Python 列表的方式来表示：

```yaml
[Mary, Bill]
```

### 杂凑表

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

### 混合使用清单和杂凑表

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