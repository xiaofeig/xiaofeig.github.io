---
title: PyCode0006
date: 2016-07-15 11:21:24
updated: 2016-07-15 11:21:29
categories: [Learning, PyCode]
tags: [python]
---

# [Python 练习册，每天一个小程序](https://github.com/xiaofeig/show-me-the-code)

## 第 0006 题

你有一个目录，放了你一个月的日记，都是 txt，为了避免分词的问题，假设内容都是英文，请统计出你认为每篇日记最重要的词。

<!-- more -->

------------

- 打开文件夹下的每个文件
- 正则匹配获取文件中的每个单词
- 统计单词出现的频率
- 过滤频率最高的单词

```python
from pathlib import Path
import re

def countfreq(s):
    """
    计算文本中单词出现的频率
    :param s: 待计算的文本
    :return: 返回只包含频率最高的单词与频率的dict
    """
    regix = r"(\b\w+(\-\w+)*('s)?\b)"
    result = re.findall(regix, s)
    dic = {}
    freq = 0
    for r in result:
        dic[r[0].lower()] = dic.get(r[0].lower(), 0) + 1
        if dic[r[0].lower()] > freq:
            freq = dic[r[0].lower()]
    result={}
    for k in dic.keys():
        if dic[k] == freq:
            result[k] = freq
    return result

def iterdir(pathname):
    """
    遍历文件夹下的文件，统计每个文件中频率最高的单词
    :param pathname: 文件夹路径
    :return: 返回每个文件频率最高的单词dict
    """
    p = Path(pathname)
    result = {}
    for path in p.iterdir():
        if path.is_file():
            with path.open() as f:
                content = f.read()
                result[path.name] = countfreq(content)
    return result

if __name__ == '__main__':
    pathname = 'resource/diary/'
    result = iterdir(pathname)
    print(result)
```


------------
