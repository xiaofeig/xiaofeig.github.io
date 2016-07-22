---
title: PyCode0016
date: 2016-07-23 07:35:29
updated: 2016-07-23 07:35:33
categories: [Learning, PyCode]
tags: [python]
---

# [Python 练习册，每天一个小程序]( https://coding.net/u/xiaofeig/p/show-me-the-code/git)


## 第 0016 题


纯文本文件 number.txt, 里面的内容（包括方括号）如下所示：


    [
        [1, 82, 65535],
        [20, 90, 13],
        [26, 809, 1024]
    ]


请将上述内容写到 number.xls 文件中，如下图所示：

<!-- more -->

![numbers.xls](http://i.imgur.com/iuz0Pbv.png)


------------


```python
from openpyxl import Workbook
from case0014 import loadjson


if __name__ == '__main__':
    content = loadjson('resource/number.txt')


    wb = Workbook()
    ws = wb.active
    ws.title = 'number'


    for i in content:
        ws.append(i)


    wb.save('resource/number.xlsx')
```


------------