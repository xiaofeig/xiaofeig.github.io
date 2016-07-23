---
title: PyCode0018
date: 2016-07-23 07:35:57
updated: 2016-07-23 07:36:01
categories: [Learning, PyCode]
tags: [python]
---

# [Python 练习册，每天一个小程序](https://github.com/xiaofeig/show-me-the-code)

## 第 0018 题


将 第 0015 题中的 city.xls 文件中的内容写到 city.xml 文件中，如下所示：

<!-- more -->

    <?xmlversion="1.0" encoding="UTF-8"?>
    <root>
    <cities>
    <!--
        城市信息
    -->
    {
        "1" : "上海",
        "2" : "北京",
        "3" : "成都"
    }
    </cities>
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
    content = {}
    for i in range(0, 3):
        content[ws.cell(row=i + 1, column = 1).value] = ws.cell(row=i + 1, column = 2).value
    return content


if __name__ == '__main__':
    path = 'resource/city.xlsx'
    wbname = 'city'
    content = loadxlsx(path, wbname)


    comment = '\n\t城市信息\n\t'
    element = 'cities'
    output = 'resource/city.xml'
    dumpxml(content, comment, element, output)
```


------------
