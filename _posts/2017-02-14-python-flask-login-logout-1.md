---
layout: post
title: 'Python - Flask 实现登录功能（一）'
tags: [IT]
description: >
  
  登录功能

  Python Flask 框架 实现登录模块

  引用 bootstrap 支持

  实现登录页面如下：

---

<!--more-->

![http://olinvkfrw.bkt.clouddn.com/3161463909.png][1]



实现过程

### 登录
登录提示输入 username 和 password 如果相符则 return Hello word



>> 如果用户名不符则返回
```python
error：'Invalid username'
```

>> 否则密码不符则返回

```python
'Invalid password'
```
     
>> 否则返回已经登录，并且返回 Hello word 

```python
session['logged_in'] = True
flash('You were logged in')
return "Hello word"
```


----------


## 更改

### 登录

登录提示输入 username 和 password 如果相符则 return Hello word

>> 如果用户名不符则返回
```python
error：'Invalid username'
```

![Image.png][2]

>> 否则密码不符则返回
```python
'Invalid password'
```

![Image.png][3]

>>否则返回已经登录，并且返回跳转页面到 / 


```python
session['logged_in'] = True
flash('You were logged in')
return redirect(url_for('hello_world'))
```


![Image.png][4]

### 登出

```python
session.pop('logged_in', None)
    flash('You were logged out')
    return render_template('logout.html')

```
![Image.png][5]

----------


`login.py`

```python
# -*- coding: UTF-8 -*-

from flask import (Flask, render_template, g, session, redirect,                            url_for,request, flash)
from flask_bootstrap import Bootstrap

DEBUG = True
SECRET_KEY = 'key'

app = Flask(__name__)

bootstrap = Bootstrap(app)

app.secret_key = SECRET_KEY
app.config.from_object(__name__)
app.config['USERNAME'] = 'admin'
app.config['PASSWORD'] = 'admin'


@app.route('/login')
def hello_world():
    return render_template('hello.html')


@app.route('/', methods=['GET', 'POST'])
def login():
    error = None
    if request.method == 'POST':
        if request.form['username'] != app.config['USERNAME']:
            error = 'Invalid username'
        elif request.form['password'] != app.config['PASSWORD']:
            error = 'Invalid password'
        else:
            session['logged_in'] = True
            flash('You were logged in')
            return redirect(url_for('hello_world'))
    return render_template('login.html', error=error)


@app.route('/logout')
def logout():
    session.pop('logged_in', None)
    flash('You were logged out')
    return render_template('logout.html')


if __name__ == '__main__':
    app.run(host="127.0.0.1")

```


----------


`templates` 文件夹下 的HTML代码我就不粘贴了，大部分都是基于jinjia2模板引擎渲染

放在 GitHub 中有兴趣的可以查看

[templates][6]


----------

## 另外解决了一个 typecho 上传图片失败的问题

首先更改 `usr/uploads` 777 权限并没有解决，后来自行狗哥一番用另一种方法解决了

`xxxxxxxxxxxx/var/Typecho/Common.php` 800多行左右

```php
public static function isAppEngine()
    {
        return !empty($_SERVER['HTTP_APPNAME'])                     // SAE
            || !!getenv('HTTP_BAE_ENV_APPID')                       // BAE
            || !!getenv('SERVER_SOFTWARE')                          // BAE 3.0
            || (ini_get('acl.app_id') && class_exists('Alibaba'))   // ACE
            || (isset($_SERVER['SERVER_SOFTWARE']) && strpos($_SERVER['SERVER_SOFTWARE'],'Google App Engine') !== false) // GAE;
    }

```

把 `!!getenv('SERVER_SOFTWARE')`  替换为  `!!getenv('HTTP_BAE_LOGID')` 


----------


``实现的比较无脑，页面也丑陋不堪，功能更是shi到不能再shi，连Remember me 的功能也没实现，但是本人正在学习进步中，下一次会来好好改进一番``

``个人觉得这个功能页面很是有问题，如果大家看出问题可以随时与我联系，希望大家多多指教。``


----------



  [1]: http://olinvkfrw.bkt.clouddn.com/3161463909.png
  [2]: http://olinvkfrw.bkt.clouddn.com/3402190077.png
  [3]: http://olinvkfrw.bkt.clouddn.com/1609114575.png
  [4]: http://olinvkfrw.bkt.clouddn.com/2363088965.png
  [5]: http://olinvkfrw.bkt.clouddn.com/592405647.png
  [6]: https://github.com/lmy1223/python_test_code/tree/master/flask_test/login-flask-bootstrap-test/templates