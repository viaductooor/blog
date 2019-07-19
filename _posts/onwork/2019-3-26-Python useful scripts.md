---
title: Python 常用代码段
tags: [python]
categories: [python]
layout: post
---

* content
{:toc}

# Read file in Python

通常方式是：

```python
with open(url,options,encoding) as file:
    # 一次读取所有数据
    content = file.read() 
    # 按行读取数据
    for line in file:
        print(line)
    # 写数据
```

这里面url是文件地址；options是文件打开的模式，可以是：

- `'r'`：只读
- `'w'`：写，会覆盖之前的文件；文件不存在则创建
- `'a'`：写，会在之前的文件后面添加，不覆盖
- `'r+'`：可读可写
- 缺省： 只读

encoding在处理含有中文字符的文档时几乎是必填项，一般为`'utf-8'`。



# Read Excel File in Python

可以使用xlrd库来读取Excel表格的数据。

读取数据之前先要打开workbook和sheet：

```python
import xlrd
file = "thinkpage_cities.xls"

with xlrd.open_workbook(file) as workbook:
    sheet = workbook.sheet_by_index(1)
    # then process the sheet
```

之后可以直接用下标来访问某一个单元格的数据：

```python
sheet.cell_value(0,0) #访问第一行第一列的数据
```

或者得到一整行的数据：

```python
sheet.row_values(0) #获取第一行的数据，以列表的形式
```

通过`sheet.nrows`可以得到行数（`sheet.ncols`是得到列数），据此可以按行遍历表格中的所有数据：

```python
for i in range(0,sheet.nrows):
    row = sheet.row_values(i)
    # process row
```

完整示例代码：

```python
import xlrd
import json

file = "thinkpage_cities.xls"

cq = []

with xlrd.open_workbook(file) as workbook:
    sheet = workbook.sheet_by_index(1)
    for i in range(1,sheet.nrows):
        row = sheet.row_values(i)
        if row[6] == 'Chongqing': # 行政归属2（英文）
            id = row[0]
            name_cn = row[1]
            name_en = row[2]
            cq.append({
                'city_id':id,
                'city_name_cn':name_cn,
                'city_name_en': name_en
            })

with open('cq_areas.json','w',encoding='utf-8') as file:
    file.write(json.dumps(cq))
```



# Regex

在Python中使用正则表达式需要导入包`import re`。下表中列出了re的常用函数。


Function |	Description
---- | ----
findall	| Returns a list containing all matches
search	| Returns a Match object if there is a match anywhere in the string
split	| Returns a list where the string has been split at each match
sub	| Replaces one or many matches with a string

## findall

这些re的函数基本都会至少接受两个参数，第一个是pattern，第二个是需要处理（匹配）的原字符串。

findall返回的是一个列表，长度等于匹配成功的次数。列表里面的元素是包含groups的元组，如果没有设置group则为单个的元素：

```python
import re

pattern = ".(el)(.)."
string = "hello, world! hello, world!"
match = re.findall(pattern,string)
print(match)

```

打印出来的结果是：

```
[('el', 'l'), ('el', 'l')]
```

## search

search是搜索字符串当中是否有能匹配到正则表达式的子串。返回结果是一个MatchObject，如果匹配不到则返回None。

MatchObject有几个常用的方法：

### groups()

返回表示groups的元组，如果没有设置group则会返回空元组。

###  group(index)

几乎等于groups()的结果使用下标，不过group(0)始终表示匹配到的完整的子串，后面的索引顺延表示各个group，因此参数比groups()使用的下标要大1。

```python
import re

pattern = ".(el.)."
string = "hello, world! hello, world!"
match = re.search(pattern,string)

print(match.groups()) # ('ell',)
print(match.group(0)) # hello
print(match.group(1)) # ell
```

# sub

sub函数接收三个参数，第一个是pattern，第二个是需要替代成的字符串，第三个是源字符串。

返回替代后的字符串。

# split

第一个参数是pattern，第二个是源字符串。

返回分割后的列表。

## Python 中正则表达式使用的字符

### 符号

Character	| Description	| Example
---|---|---
`[]`	| A set of characters	| `"[a-m]"`	
back slash	|Signals a special sequence (can also be used to escape special characters)	| `"\d"`
`.`	| Any character (except newline character)	| `"he..o"`	
`^`	| Starts with	| `"^hello"`	
`$`	| Ends with	| `"world$"`	
`*`	| Zero or more occurrences	| `"aix*"`	
`+`	| One or more occurrences	| `"aix+"`	
`{}`	| Exactly the specified number of occurrences	| `"al{2}"`	
`|`	| Either or	| `"falls|stays"`	
`()`	| Capture and group

### 转义字符

如果正则表达式中有转义字符的话，最好使用`r'\AThe'`这种方式消除歧义（反斜杠在字符串中的转义作用与在正则表达式中的转义作用）。

Character	| Description	| Example
---|---|---
\A	| 匹配起始符	| "\AThe"	
\b	| 起始符和终止符	| r"\bain" r"ain\b"	
\B	| 非起始符非终止符	| r"\Bain" r"ain\B"	
\d	| 0-9的数字	| "\d"	
\D	| 非数字	| "\D"	
\s	| 空格	| "\s"	
\S	| 非空格	| "\S"	
\w	| 匹配word character（包括0-9,a-z,和下划线）	| "\w"	
\W	| 匹配非word character	| "\W"	
\Z	| 非终止符	| "Spain\Z"

### 集

Set	| Description
---|---
`[arn]`	| a或r或n	
`[a-n]`	| a-n其中之一的字母
`[^arn]`	| a、r、n之外的字符	
`[0123]`	| 0或1或2或3	
`[0-9]`	| 0-9其中之一的数字
`[0-5][0-9]`	| 00-59其中之一的数字（两位）
`[a-zA-Z]`	| a-z或者A-Z的字母，换言之，任意一个字母
`[+]`	| 在方括号中, `+, *, ., |, (), $,{} `都没有他们在正则表达式中的意义, 因此 [+] 仅仅表示加号（虽然在方括号外有其他的含义）。

<mark>默认`.`是匹配换行符以外的符号，如果要匹配换行符，需要再选项中指定标志位`DOTALL`!</mark>