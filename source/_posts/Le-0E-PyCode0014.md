---
title: PyCode0014
date: 2016-07-23 07:34:46
updated: 2016-07-23 07:34:52
categories: [Learning, PyCode]
tags: [python]
---

# [Python 练习册，每天一个小程序](https://github.com/xiaofeig/show-me-the-code)

## 第 0014 题


纯文本文件 student.txt为学生信息, 里面的内容（包括花括号）如下所示：


    {
        "1":["张三",150,120,100],
        "2":["李四",90,99,95],
        "3":["王五",60,66,68]
    }


请将上述内容写到 student.xls 文件中，如下图所示：

<!-- more -->

![student.xls](http://i.imgur.com/nPDlpme.jpg)


- [阅读资料](http://www.cnblogs.com/skynet/archive/2013/05/06/3063245.html) 腾讯游戏开发 XML 和 Excel 内容相互转换


------------


- [OpenPyXL](http://openpyxl.readthedocs.io/en/default/index.html)


```python
import json
from openpyxl import Workbook


def loadjson(path):
    """
    加载指定路径的json格式的文件
    :param path: 文件路径
    :return: 返回文件中内容
    """
    with open(path, 'r', encoding='utf-8') as f:
        return json.load(f)


if __name__ == '__main__':
    content = loadjson('resource/student.txt')
    wb = Workbook()
    ws = wb.active
    ws.title = 'student'
    for i in content:
        content[i].insert(0, i)
        ws.append(content[i])
    wb.save('resource/student.xlsx')
```


------------
