---
title: PyCode0007
date: 2016-07-15 11:28:06
updated: 2016-07-15 11:28:12
categories: [Learning, PyCode]
tags: [python]
---

# [Python 练习册，每天一个小程序](https://github.com/Yixiaohan/show-me-the-code)

## 第 0007 题

有个目录，里面是你自己写过的程序，统计一下你写过多少行代码。包括空行和注释，但是要分别列出来。

<!-- more -->

------------

- 正则匹配统计总行数、空行数和注释行数

```python
from pathlib import Path
import re

def countline(s):
    """
    统计文件中内容的总行数、空行数和注释行数
    :param s: 待统计的文本
    :return: 返回包含统计信息的dict
    """
    result = {}
    # 匹配总行
    result['totalline'] = re.findall(r'\n', s).__len__() + 1
    # 匹配空行
    result['blankline'] = re.findall(r'^( *(\n)?)$', s, flags=re.MULTILINE).__len__()
    # 匹配注释行
    result['commentline'] = re.findall(r'^( *#)', s, flags=re.MULTILINE).__len__()
    # 匹配多行注释
    mulcomment = re.findall(r'("""\n(.*\n)*?.*""")', s)
    for i in mulcomment:
        result['commentline'] += re.findall(r'\n', i[0]).__len__() + 1
    return result

if __name__ == '__main__':
    p = Path()
    pyfiles = list(p.glob('*.py'))

    result = {}
    for pyfile in pyfiles:
        with pyfile.open(encoding='utf-8') as f:
            s = f.read()
            temp = countline(s)
            for k, v in temp.items():
                result[k] = result.get(k, 0) + v
    print(result)
```

------------
