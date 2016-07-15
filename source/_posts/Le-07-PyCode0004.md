---
title: PyCode0004
date: 2016-07-15 11:20:34
updated: 2016-07-15 11:20:42
categories: [Learning, PyCode]
tags: [python]
---

# [Python 练习册，每天一个小程序](https://github.com/Yixiaohan/show-me-the-code)

## 第 0004 题

任一个英文的纯文本文件，统计其中的单词出现的个数。

------------

- 统计的可能不太精确，以字符边界`\b`作为分隔的要素
- 只考虑到单词中可能出现的`-`和`'s`
- 文本中还可能出现网址链接、伪代码等，这些未考虑

```python
import re

def countNum(s):
    """
    :param s: 需要统计的文本
    :return: 返回文本中单词的个数
    """
    pattern = r"(\b\w+(\-\w+)*('s)?\b)"
    result = re.findall(pattern, s)
    return result.__len__()

if __name__ == '__main__':
    with open('resource/python-tutorial.txt') as f:
        print(countNum(f.read()))
```

------------