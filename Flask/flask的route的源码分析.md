# Flask route的源码分析
flask的例子
```python
from flask import Flask

app = Flask(__name__)

@app.route('/',methods=['GET'])
def index():
  return "hello world"

if __name__ == '__main__':
  app.run()
```
使用`app.route`装饰器,完成了url与视图函数的映射  
在`flask`中使用了`werkzueg`的`Rule`类、`Map`类和`MapAdapter`类实现了`{url:endpoint:view_func}`的映射关系  
`route`装饰器在`app.py`的`Flask`类中
```python
def route(self, rule, **options):
  def decorator(f):
    endpoint = options.pop('endpoint', None)
    self.add_url_rule(rule, endpoint, f, **options)
    return f
  return decorator
```
通过装饰器将`url`传入到`add_url_rule`当中的`rule`,`methods`以`**options`关键字参数传入到`add_url_rule`中
```python
def add_url_rule(self, rule, endpoint=None, view_func=None,
                 provide_automatic_options=None, **options):
```
在`add_url_rule`中,判断`endpoint`是否为空,为空调用`_endpoint_from_view_func`函数从视图函数中以字符串取出视图函数名,并将`endpoint`添加到字典`options`当中
```python
if endpoint is None:
    endpoint = _endpoint_from_view_func(view_func)
options['endpoint'] = endpoint
methods = options.pop('methods', None)
```
`_endpoint_from_view_func`函数返回视图函数的`__name__`属性
```python
def _endpoint_from_view_func(view_func):
    assert view_func is not None, 'expected view func if endpoint '\
                                  'is not provided.'
    return view_func.__name__
```
将`rule`、`methods`、`**options`传入`Rule`类构造出一个`rule`对象,将该`rule`对象添加到`url_map`当中.其中 `options['endpoint']=endpoint, rule=url` . 通过`routing.py`的`Rule`类建立了`(url,endpoint,methods)`的映射关系
```python
rule = self.url_rule_class(rule, methods=methods, **options)
self.url_map.add(rule)
```
`url_map`是`routing.py`的`Map`类创建出来的一个对象,用来存储`Rule`的对象
```python
self.url_map = Map()
```
`Map`内以列表来存储`Rule`的对象
```python
url_map = Map([
                Rule('/all/', defaults={'page': 1}, endpoint='all_entries'),
                Rule('/all/page/<int:page>', endpoint='all_entries')
            ])
```
`add_url_rule`的`view_func`就是`app.route`构造器修饰的视图函数,`app.py`的`Flask`类定义了`view_functions`为一个空字典,将`view_func`存入`view_functions`中,建立了`{endpoint:view_func}`的映射关系
```python
if view_func is not None:
    old_func = self.view_functions.get(endpoint)
    if old_func is not None and old_func != view_func:
        raise AssertionError('View function mapping is overwriting an '
                             'existing endpoint function: %s' % endpoint)
    self.view_functions[endpoint] = view_func
```
`Flask`的`url_map`存储了所有的(url,endpoint)映射关系;  
`Flask`的`view_functions`存储了所有的{endpoint:view_func}映射关系;  
至此,url与视图函数就通过endpoint映射起来了,请求时服务器对请求进行转发,解析得到url,通过`MapAdapter`类的`match`方法得到endpoint,再通过`view_functions`找到对应的视图函数,直接调用函数执行,就完成了一个完整的`route`   
