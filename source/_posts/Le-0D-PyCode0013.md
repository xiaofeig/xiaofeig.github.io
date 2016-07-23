---
title: PyCode0013
date: 2016-07-23 07:34:24
updated: 2016-07-23 07:34:28
categories: [Learning, PyCode]
tags: [python]
---

# [Python 练习册，每天一个小程序](https://github.com/xiaofeig/show-me-the-code)

## 第 0013 题

用 Python 写一个爬图片的程序，爬 [这个链接里的日本妹子图片 :-)](http://tieba.baidu.com/p/2166231880)

<!-- more -->

- [参考代码](http://www.v2ex.com/t/61686 "参考代码")


------------


```python
import requests
import re
from pathlib import Path


if __name__ == '__main__':
    # 网页内容
    response = requests.get('http://tieba.baidu.com/p/2166231880')
    content = str(response.content)


    # 图片存放路径
    path = Path('resource/imgs/')
    if not path.exists():
        path.mkdir()


    # 正则匹配图片链接
    p = r'<img.*?class="BDE_Image" src="(.*?)".*?>'
    result = re.findall(p, content)


    # 获取图片并写入
    for i in result:
        resp = requests.get(i)
        filename = re.search(r'\/([0-9a-f]+\.\w+)', i).group(1)
        with open('resource/imgs/'+filename, 'wb') as f2:
            f2.write(resp.content)
```


------------
