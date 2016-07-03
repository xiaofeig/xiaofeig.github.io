---
title: PyCode0001
date: 2016-07-01 01:56:26
updated: 2016-07-01 01:56:32
categories: [Learning]
tags: [python]
---

# [Python 练习册，每天一个小程序](https://github.com/Yixiaohan/show-me-the-code)

## 第 0001 题

做为 Apple Store App 独立开发者，你要搞限时促销，为你的应用生成激活码（或者优惠券），使用 Python 如何生成 200 个激活码（或者优惠券）？

<!-- more -->

要求：
1. 唯一
2. 在数据库中便于查找


------------


```python
import random
import string

def generatecode(num, prefixnum, postnum):
    """
    生成唯一激活码，序列号（prefixnum位）+无序字符串（postnum位）
    :param num: 要生成的个数
    :param prefixnum: 前缀位数
    :param postnum: 后缀位数
    :return: 含激活码的集合
    """
    l =[]
    for i in range(num):
        prefix = '{:0{prefixnum}X}'.format(i, prefixnum=prefixnum)
        post = ''
        for j in range(postnum):
            post += random.choice(string.ascii_letters+string.digits)
        l.append(prefix+post)
    return l

if __name__ == '__main__':
    l = generatecode(200, 6, 10)

    for s in l:
        print(s)
```

------------

