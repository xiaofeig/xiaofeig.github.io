---
title: PyCodeHuffman
date: 2016-07-01 01:58:53
updated: 2016-07-01 01:58:58
categories: [Learning, PyCode]
tags: [python]
---

# [Python 练习册，每天一个小程序](https://coding.net/u/xiaofeig/p/show-me-the-code/git)

## 插曲

写出Chongqing University of Posts and Telecommunications的Huffman编码。

<!-- more -->

要求：
1. 运行结果截图
2. 不考虑大小写，不考虑空格


------------



```python
from operator import attrgetter

class Node():
    def __init__(self, freq, key='', leftchild=None, rightchild=None):
        """
        :param freq: 频数
        :param key: 键值
        :param leftchild: 左子节点
        :param rightchild: 右子节点
        """
        self.freq = freq
        self.key = key
        self.leftchild = leftchild
        self.rightchild = rightchild

    def __str__(self):
        return '({}, {}, {})'.format(self.key, self.freq, self.code)

    __repr__ = __str__

def tocommonstr(str):
    """
    去掉空格，全部替换成大写
    :param str: 目标字符串
    :return: 返回无空格全大写的字符串
    """
    return str.replace(' ', '').upper()

def countfreq(str):
    """
    统计频数
    :param str: 目标字符串
    :return: 返回包含频数的节点集合
    """
    l = list()
    for c in set(str):
        l.append(Node(freq=str.count(c), key=c))
    return l


def spantree(l):
    """
    生成树
    :param l: 包含节点的集合
    :return: 放回最小生成树
    """
    for i in range(l.__len__()-1):
        # 排序
        l = sorted(l, key=attrgetter('freq', 'key'), reverse=True)
        # 选择最小的两个节点
        n1 = l.pop()
        n2 = l.pop()
        # 添加生成的节点
        l.append(Node(freq=n1.freq + n2.freq, leftchild=n1, rightchild=n2))
    return l[0]

def generatecode(l, tree, result):
    """
    生成Huffman编码
    :param l: 存放编码的集合
    :param tree: 最小生成树
    :param result: 包含编码的节点集合
    """
    if tree.leftchild != None:
        l.append('0')
        generatecode(l, tree.leftchild, result)
        l.append('1')
        generatecode(l, tree.rightchild, result)
    else:
        tree.code = ''.join(l)
        result.append(tree)
    if l.__len__() > 0:
        l.pop()

if __name__ == '__main__':
    targetstr = 'Chongqing University of Posts and Telecommunications'
    targetstr = tocommonstr(targetstr)
    targetlist = countfreq(targetstr)
    tree = spantree(targetlist)
    l = list()
    result = list()
    generatecode(l, tree, result)

    print('(字母,频数,编码)')
    for n in result:
        print('({}, {}, {:>6})'.format(n.key, n.freq, n.code))
```

------------

