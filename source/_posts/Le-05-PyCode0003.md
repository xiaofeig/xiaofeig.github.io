---
title: PyCode0003
date: 2016-07-01 02:01:50
updated: 2016-07-01 02:01:55
categories: [Learning, PyCode]
tags: [python]
---

# [Python 练习册，每天一个小程序](https://coding.net/u/xiaofeig/p/show-me-the-code/git)


## 第 0003 题

将 0001 题生成的 200 个激活码（或者优惠券）保存到 Redis 非关系型数据库中。

<!-- more -->

------------


- [redis.io](http://redis.io/)
- [redis.cn](http://redis.cn/)
- Windows
    - [win64-redis](https://github.com/MSOpenTech/redis)
    - 安装：下载[Redis-x64-xxx.zip](https://github.com/MSOpenTech/redis/releases)后解压即可
    - [启动：](http://jingyan.baidu.com/article/f25ef2546119fd482c1b8214.html)`redis-server.exe redis.windows.conf`
    - 连接：`redis-cli.exe`
- Linux
    - 安装：`apt-get install redis-server`
    - 后台启动：`redis-server &`
    - 连接：`redis-cli`
    - 关闭：`redis-cli shutdown`
- Python Client：[redis-py](https://github.com/andymccurdy/redis-py)(官方推荐版本)
    - 下载：[redis-2.10.3-py2.py3-none-any.whl](http://www.lfd.uci.edu/~gohlke/pythonlibs/)
    - 安装：`pip install redis-2.10.3-py2.py3-none-any.whl`

```python
import redis
from case0001 import generatecode

r = redis.StrictRedis(host='139.129.xx.ooo', port=6379, db=0)
codes = generatecode(200, 6, 10)
for code in codes:
    r.set(int(code[0:6], 16), code)
```

------------

