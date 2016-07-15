---
title: PyCode0009
date: 2016-07-15 11:28:37
updated: 2016-07-15 11:28:41
categories: [Learning, PyCode]
tags: [python]
---

# [Python 练习册，每天一个小程序](https://github.com/Yixiaohan/show-me-the-code)

## 第 0009 题

一个HTML文件，找出里面的**链接**。


------------

- 待完善

```python
import re

content = ''
with open('resource/toc.html', 'r', encoding='utf-8') as f:
    content = f.read()

p1 = r'src="(.*?)"'
p2 = r'href="(.*?)"'

r1 = re.findall(p1, content)
r2 = re.findall(p2, content)

print(r1 + r2)
```

------------
