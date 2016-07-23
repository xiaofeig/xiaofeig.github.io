---
title: PyCode0012
date: 2016-07-15 11:31:21
updated: 2016-07-15 11:31:26
categories: [Learning, PyCode]
tags: [python]
---

# [Python 练习册，每天一个小程序](https://github.com/xiaofeig/show-me-the-code)

## 第 0012 题

敏感词文本文件 filtered_words.txt，里面的内容 和 0011题一样，当用户输入敏感词语，则用 星号 \* 替换，例如当用户输入「帝都是个好城市」，则变成「\*\*是个好城市」。

<!-- more -->

------------

- 使用到`re.sub(...)`这个**&#42;&#42;**函数

```python
import re
from case0011 import generatepat, getflag

def ignoreword(s, pattern):
    return re.sub(pattern, lambda mo: mo.group(0).__len__()*'*', s)

if __name__ == '__main__':
    path = 'resource/filtered_words.txt'
    pattern = '(' + generatepat(path) + ')'
    flag = 0
    while flag == 0:
        s = input('Input: ')
        result = ignoreword(s, pattern)
        print(result)
        flag = getflag()
    print("Bye...")
```

------------
