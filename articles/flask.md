# Flask

## 创建虚拟环境

mkdir myproject
cd myproject
python3 -m venv venv

## 激活虚拟环境

. venv/bin/activate

## 安装 Flask

pip install Flask

## 启动服务器

### 1. 设置环境变量

export FLASK_APP=app.py

如果文件名为 app.py 或者 wsgi.py ，那么您不 需要设置 FLASK_APP 环境变量。
### 2. 启动

flask run -h 0.0.0.0 -p 8001

flask --app hello run

这种可以不设置环境变量 FLASK_APP

### 3. 调试模式

flask run --debugger -h 0.0.0.0 -p 8001

flask --debug run

export FLASK_ENV=development

## 扩展

### 常用

1. FLASKSQLAlchemy: 数据库
2. FalskWTF: 表单
3. Flask-mail: 
4. FlaskRESTful:接口

## URL配置及路由

### 1. 使用装饰器

@app.route(url_name, methods)


### 2. 使用 API 配置

app.add_url_rule(url, url_name, view_name)

```
@app.route('/')
@app.route('/index')
def hello_world():
    return 'Hello World! Flassskwww'

app.add_url_rule('/test', 'test', hello_world)
```

### 路由匹配规则

#### 1. 匹配整个文字

@app.route('/hello')

#### 2. 传递参数

@app.route('/user/<username>')

#### 3. 指定参数类型

@app.route('/post/<int:post_id>')

### 查看URL规则列表

app.url_map

### URL 逆向解析（根据函数名称解析对应的URL）

#### 1. url_for(url_name, **kwargs)

#### 2. 静态文件引用

url_for('static', filename='style.css')

### 获取 URL 中的值

@app.route('/page/<page_no>')
def list_user(page_no):

@app.route('/page/')
@app.route('/page/<page_no>') // page_no 为可选的时候，给一个默认值
def list_user(page_no=1):

## 应用上下文

falsk 中可能有多个应用实例 

1. current_app: 当前应用的实例
2. g: 处理请求是的临时存储对象，每次请求都会重设这个变量

## 请求上下文

### request 

请求对象，封装了客户端发出的HTTP请求中的内容

### session

用户会话（dict），各个请求之间的数据共享

## 请求报文

### 常用参数

1. method: 请求类型（GET/POST...）
2. form: POST 请求数据 dict
3. args: GET 请求数据 dict
4. values: POST 请求和 GET 请求数据集合 dict
5. files: 上传的文件数据 dict
6. cookies: 请求中的 cookie dict
7. headers: HTTP 请求头

## 请求钩子

### 1. before_request

每个请求到达前执行

### 2. before_first_request

服务器初始化后第一个请求到达前执行

### 3. after_request

每次请求处理完后执行，如果请求过程产生了异常，则不执行

### 4. teardown_request

每次请求处理完后执行，如果请求过程产生了异常也执行

## 响应

可以是字符串

可以是元组 (response, status, headers), (response, headers)

1. response: 响应内容
2. status: 响应状码
3. headers: 响应头信息

使用 make_response 代替

## 内部视图

redirect() 实现重定向

abort() 处理错误

## 模板

### 模板引擎

Flask 使用 Jinja2 作为默认模板引擎

默认配置

1. template_folder='templates': 模板的默认目录
2. render_template(): (x)html，自动转义，严格的格式检查
3. render_template_string(): 字符串自动转义
4. {% autoscape %}: 手动设置是否转义

全局对象

1. config: 配置信息
2. request: 请求对象
3. session: 会话对象
4. g: 请求相关的全局变量

全局函数

1. url_for(): URL 解析函数
2. get_flashed_messages(): 获取会话消息

上下文处理器

1. 在模板的上下文中添加新的内容
2. 内容可以是变量，也可以是函数

## 模板中的变量

{{ value }}

## 模板标签

1. {% tag %}
2. {% tag %}
   内容
   {% endtag %}

标签中可包含表达式，如：if条件控制

{% if condition_a %}
    满足a
{% elif condition_b %}
    满足b
{% else %}
    都不满足
{% endif %}

内置判断条件

{% if value is defined %}
...
{% endif %}

1. defined/undefined
2. none
3. number/string
4. even/odd: 奇偶判断
5. upper/lower: 大小写判断

if 标签中的其他逻辑判断

1. and，or
2. ==, !=
3. >, <
4. >=, <=
5. in, not in

for 循环

<ul>
{% for key, value in data.items() %}
    <li class="{{loop.cycle('odd', 'even')}}" >
    {{ key }}:{{ value }}
    </li>
{% else %}
    <li>无数据</li>
{% endfor %}
</ul>

## 过滤器

作用是修改变量，如：格式化显示

用 | 进行分割，可以链式调用

{{ name|striptags|title }}

使用标签

{% filter upper %}
    This text becomes uppercase
{% endfilter %}

## 内置过滤器

求绝对值：{{ value|abs }}

默认值显示: 
    default(value, default_value='', boolean=False)

    {{ value|default('默认值')}}
    {{ value|d('默认值')}}

html 转义

    {{ value|escape }} 或者 {{ value|e }}

...

## Flask ORM

ORM 定义

对象关系映射: 数据库表与对象的映射

### flask-sqlalchemy

安装

pip install -U Flask-SQLAlchemy

安装依赖

pip install mysqlclient

配置

数据库URI SQLALCHEMY_DATABASE_URI

mysql://scott:tiger@localhost/mydatabase
mysql://用户名:密码@地址/数据库名称

绑定到 Flask 对象

db = SQLAlchemy(app)

ORM 模型创建

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)

指定表名称

__tablename__ = 'weibo_user'

手动创建数据库

创建表

db.create_all(bind='db1')

删除表

db.drop_all()



