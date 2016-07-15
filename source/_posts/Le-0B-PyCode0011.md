---
title: PyCode0011
date: 2016-07-15 11:29:39
updated: 2016-07-15 11:29:43
categories: [Learning, PyCode]
tags: [python]
---

# [Python 练习册，每天一个小程序](https://github.com/Yixiaohan/show-me-the-code)

## 第 0011 题

敏感词文本文件 filtered_words.txt，里面的内容为以下内容，当用户输入敏感词语时，则打印出 Freedom，否则打印出 Human Rights。

    帝都
    程序员
    python
    滚粗
    尼妹
    王尼玛
    腻害
    煞笔
    papi酱

------------

```python
import re

def generatepat(path):
    """
    根据文件内容生成pattern
    :param path: 文件路径
    :return: 生成正则匹配的pattern
    """
    with open(path, 'r', encoding='utf-8') as f:
        keywords = f.read().split('\n')
        pattern = '|'.join(keywords)
        return pattern

def matchword(s, pattern):
    """
    搜索关键字
    """
    result = re.search(pattern, s)
    if result == None:
        print('Freedom')
    else:
        print('Human Rights')

def getflag():
    """
    生成下一步的标记
    :return: 下一步的标记flag
    """
    next = True
    while next:
        try:
            flag = int(input('Continue-> 0, Exit-> 1: '))
            if flag == 0 or flag ==1:
                next = False
            else:
                print("Oops! Just 0 or 1. Thanks!")
        except ValueError:
            print("Oops!  That was no valid number.  Try again...")
    return flag

if __name__ == '__main__':
    path = 'resource/filtered_words.txt'
    pattern = generatepat(path)
    flag = 0;
    while flag == 0:
        s = input('Input: ')
        matchword(s, pattern)
        flag = getflag()
    print("Bye...")
```

------------