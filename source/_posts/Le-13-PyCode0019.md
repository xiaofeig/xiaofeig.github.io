---
title: PyCode0019
date: 2016-07-23 07:36:15
updated: 2016-07-23 07:36:19
categories: [Learning, PyCode]
tags: [python]
---

# [Python 练习册，每天一个小程序](https://github.com/xiaofeig/show-me-the-code)

## 第 0019 题


将 第 0016 题中的 number.xls 文件中的内容写到 number.xml 文件中，如下

<!-- more -->

所示：


    <?xml version="1.0" encoding="UTF-8"?>
    <root>
    <numbers>
    <!--
        数字信息
    -->


    [
        [1, 82, 65535],
        [20, 90, 13],
        [26, 809, 1024]
    ]


    </numbers>
    </root>

------------

```python
from openpyxl import load_workbook
from case0017 import dumpxml

def loadxlsx(path, wbname):
    """
    载入xlsx文件内容
    :param path: 文件路径
    :param wbname: wb名称
    :return: 返回文件内容（dict）
    """
    wb = load_workbook(filename=path)
    ws = wb[wbname]
    content = []
    for i in range(0, 3):
        temp = []
        for j in range(0, 3):
            temp.append(ws.cell(row=i + 1, column=j+1).value)
        content.append(temp)
    return content

if __name__ == '__main__':
    path = 'resource/number.xlsx'
    wbname = 'number'
    content = loadxlsx(path, wbname)

    comment = '\n\t数字信息\n\t'
    element = 'numbers'
    output = 'resource/number.xml'
    dumpxml(content, comment, element, output)
```

------------
