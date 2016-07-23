---
title: PyCode0015
date: 2016-07-23 07:35:13
updated: 2016-07-23 07:35:17
categories: [Learning, PyCode]
tags: [python]
---

# [Python 练习册，每天一个小程序](https://github.com/xiaofeig/show-me-the-code)


## 第 0015 题


纯文本文件 city.txt为城市信息, 里面的内容（包括花括号）如下所示：


    {
        "1" : "上海",
        "2" : "北京",
        "3" : "成都"
    }


请将上述内容写到 city.xls 文件中，如下图所示：

<!-- more -->

![city.xls](http://i.imgur.com/rOHbUzg.png)


------------


```python
from openpyxl import Workbook
from case0014 import loadjson


if __name__ == '__main__':
    content = loadjson('resource/city.txt')


    wb = Workbook()
    ws = wb.active
    ws.title = 'city'


    for i in content:
        ws['A'+i] = i
        ws['B%s'%(i)] = content[i]


    wb.save('resource/city.xlsx')
```


------------
