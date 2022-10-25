---
title: json
date: 2020-08-24 09:00:00
tags:
    - json
---

## JSON

### JSON的语法规则
- 数组(Array)用方括号`[]`表示
- 对象(Object)用大括号`{}`表示
- 名称/值对(name/value)组合成数组和对象
- 名称(name)置于双引号中，值(value)有字符串、数值、布尔值、 null、对象和数组
- 并列的数据之间用逗号`,`分隔
对象和数组。
```json
{
    "name": "zeyu",
    "age": 24
}
```

### 名称/值对(name/value)规则
- 名称`(Name)`是一个字符串,要用双引号括起来,不能用单引号,也不能没有引号,这一点与JavaScript不同
- 值的类型只有七种：字符串`(string)`、数值`(number)`、对象`(object)`、数组`(array)`、`true`、`false`、`null`。不能有这之外的类型，例如undefined、函数等

### 字符串(string)的规则
- 英文双引号括起来，不能用单引号，也不能没有
- 字符串中不能单独出现双引号`"`和右斜杠`\`
- 如果要打双引号或右斜杠，需要使用“右斜杠+字符”的形式，例如`\”`和`\\`，其它的转义字符也是如此