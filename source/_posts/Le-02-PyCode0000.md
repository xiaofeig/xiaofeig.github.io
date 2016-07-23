---
title: PyCode0000
date: 2016-07-01 01:51:04
updated: 2016-07-01 01:51:20
categories: [Learning, PyCode]
tags: [python]
---

# [Python 练习册，每天一个小程序](https://github.com/xiaofeig/show-me-the-code)

## 第 0000 题

将你的 QQ 头像（或者微博头像）右上角加上红色的数字，类似于微信未读信息数量那种提示效果。 类似于图中效果

<!-- more -->

![](https://camo.githubusercontent.com/d518d3929e4054ce2f9183b23e52908da7e5632d/687474703a2f2f692e696d6775722e636f6d2f736732646b75592e706e673f31)

------------

- [Python Image Library](http://www.pythonware.com/products/pil/)
- [Tutorial](http://effbot.org/imagingbook/)
- 下载[Pillow-3.2.0-cp34-cp34m-win_amd64.whl](http://www.lfd.uci.edu/~gohlke/pythonlibs/#pillow)
- 安装
    1.  `python -m pip install Pillow-xxxx.whl`
    2.  或`pip install Pillow-xxxx.whl`

```python
from PIL import Image
from PIL import ImageDraw
from PIL import ImageFont

img = Image.open('image/avatar.jpeg')

draw = ImageDraw.Draw(img)
ttFont = ImageFont.truetype ('arial.ttf', 20)
draw.text((img.size[0]-20, 5), '2', fill=(255, 0, 0), font=ttFont)

del draw
# img.save('image/hello.jpeg', 'JPEG')
img.show()
img.close()
```

------------
