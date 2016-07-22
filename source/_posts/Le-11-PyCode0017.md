---
title: PyCode0017
date: 2016-07-23 07:35:42
updated: 2016-07-23 07:35:45
categories: [Learning, PyCode]
tags: [python]
---

# [Python 练习册，每天一个小程序]( https://coding.net/u/xiaofeig/p/show-me-the-code/git)

## 第 0017 题


将 第 0014 题中的 student.xls 文件中的内容写到 student.xml 文件中，如下所示：

<!-- more -->

    <?xml version="1.0" encoding="UTF-8"?>
    <root>
    <students>
    <!--
        学生信息表
        "id" : [名字, 数学, 语文, 英文]
    -->
    {
        "1" : ["张三", 150, 120, 100],
        "2" : ["李四", 90, 99, 95],
        "3" : ["王五", 60, 66, 68]
    }
    </students>
    </root>


------------


- 想将json格式的数据以可读的格式输出，但没找到合适的方法


```python
from xml.dom import getDOMImplementation
from openpyxl import load_workbook


def loadxlsx():
    """
    加载student.xlsx
    :return: 返回文件中的内容（dict）
    """
    wb = load_workbook(filename='resource/student.xlsx')
    ws = wb['student']
    content = {}
    for i in range(0, 3):
        temp = []
        for j in range(1, 5):
            temp.append(ws.cell(row=i + 1, column=j + 1).value)
        content[ws.cell(row=i + 1, column = 1).value] = temp
    return content


def dumpxml():
    """
    将内容写入xml，并保存
    （格式不太良好）
    """
    content = loadxlsx()
    impl = getDOMImplementation()
    newdoc = impl.createDocument(None, 'root', None)
    root = newdoc.documentElement


    comment = newdoc.createComment('\n\t学生信息表\n\t"id" : [名字, 数学, 语文, 英文]\n\t')
    root.appendChild(comment)


    students = newdoc.createElement('students')
    text = newdoc.createTextNode(content.__str__())
    students.appendChild(text)
    root.appendChild(students)


    with open('resource/student.xml', 'w', encoding='utf-8') as f:
        f.write(newdoc.toprettyxml())


if __name__ == '__main__':
    dumpxml()
```




------------