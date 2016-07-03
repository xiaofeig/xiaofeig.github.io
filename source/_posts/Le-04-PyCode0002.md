---
title: PyCode0002
date: 2016-07-01 02:00:23
updated: 2016-07-01 02:00:28
categories: [Learning, PyCode]
tags: [python]
---

# [Python 练习册，每天一个小程序](https://github.com/Yixiaohan/show-me-the-code)

## 第 0002 题

将 0001 题生成的 200 个激活码（或者优惠券）保存到 **MySQL** 关系型数据库中。

<!-- more -->

------------

- [MySql Connector/Python doc](http://dev.mysql.com/doc/connector-python/en/)
- [mysql-connector-python-2.1.3-py3.4-winx64.msi](http://dev.mysql.com/downloads/connector/python/)
- 验证安装：`python34/Lib/site-packages/`目录下有`mysql`文件夹

```python
from datetime import datetime
from mysql.connector import errorcode
import mysql.connector

from case0001 import generatecode

config = {
    'user': 'root',
    'password': 'xxxxoooo',
    'host': '139.129.xx.ooo'
}

DB_NAME = 'python_demo'

TABLES = {}
TABLES['activation_code'] = (
   "create table activation_code ("
   "  id int(11) NOT NULL PRIMARY KEY,"
   "  activation_code char(16) NOT NULL,"
   "  create_time datetime NOT NULL "
   ")character set utf8 collate utf8_general_ci"
)

try:
    conn = mysql.connector.connect(**config)
    cursor = conn.cursor()

    # 创建数据库
    def createdb():
        try:
            cursor.execute('create database {} character set utf8 collate utf8_general_ci'.format(DB_NAME))
        except mysql.connector.Error as err:
            if err.errno == errorcode.ER_DB_CREATE_EXISTS:
                print("Database {} already exists.".format(DB_NAME))
            else:
                print(err)

    # 创建表
    def createtable():
        try:
            conn.database = DB_NAME
            cursor.execute(TABLES['activation_code'])
        except  mysql.connector.Error as err:
            if err.errno == errorcode.ER_TABLE_EXISTS_ERROR:
                print("Table {} already exists.".format('activation_code'))
            else:
                print(err)

    # 插入激活码
    def insert():
        codes = generatecode(200, 6, 10)
        try:
            for code in codes:
                add_code = ("insert into activation_code "
                            "(id, activation_code, create_time) "
                            "values (%s, %s, %s)")
                data_code =(int(code[0:6], 16), code, datetime.now())
                cursor.execute(add_code, data_code)
                conn.commit()
        except mysql.connector.Error as err:
            print(err)

    createdb()
    createtable()
    insert()

except mysql.connector.Error as err:
    if err.errno == errorcode.ER_ACCESS_DENIED_ERROR:
        print('Something is wrong with your user name or password')
    else:
        print(err)
else:
    cursor.close()
    conn.close()
```

------------


