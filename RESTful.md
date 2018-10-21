### RESTful设计方法

- 域名  
应该尽量将API部署在专用域名之下  
https://api.example.com  
如果确实API很简单,不会有进一步扩展,可以考虑放在主域名下  
https://www.example.com/api/  

- 版本  
将API的版本号放入URL  
```
http://www.example.com/api/1.0/foo
http://www.example.com/api/1.1/foo
http://www.example.com/api/2.0/foo
```
将版本号放在HTTP头信息中  
```
Accept: vnd.example-com.foo+json; version=1.0
Accept: vnd.example-com.foo+json; version=1.1
Accept: vnd.example-com.foo+json; version=2.0
```

- 路径

每个网址代表一种资源资源作为网址,只能有名词,不能有动词,而且所用的名词往往与数据库的表名对应.  
GET /products : 返回所有产品信息  
POST /products : 将新建产品信息  
GET /products/4 : 将获取产品4  
PATCH | PUT /products/4 : 将更新产品4  
API中的名词应该使用复数.无论子资源或者所有资源.  
获取单个产品：http://127.0.0.1:8080/products/1  
获取所有产品：http://127.0.0.1:8080/products  

- HTTP动词  
资源的具体操作类型,由HTTP动词表示  

| 请求方式 | 说明 |
|--|--|
|GET(SELECT) | 从服务器取出资源(一项或多项)|
|POST(CREATE)|在服务器新建一个资源|
|PUT(UPDATE)|在服务器更新资源(客户端提供改变后的完整资源)|
|DELETE(DELETE)|从服务器删除资源|
|PATCH(UPDATE)|在服务器更新资源(客户端提供改变的属性)|
|HEAD|获取资源的元数据|
|OPTIONS|获取信息,关于资源的哪些属性是客户端可以改变的|

- 过滤信息  
url查询字符串,API提供参数,过滤返回结果
```
?limit=10　指定返回记录的数量
?offset=10　指定返回记录的开始位置
?page=2&per_page=100　指定第几页，以及每页的记录数
?sortby=name&order=asc　指定返回结果按照哪个属性排序，以及排序顺序
```

- 状态码  
```
200 OK - [GET/PUT/PATCH]：服务器成功返回用户请求的数据
201 CREATED - [POST]：用户新建数据成功。
204 NO CONTENT - [DELETE]：用户删除数据成功。
202 Accepted - [*]：表示一个请求已经进入后台排队（异步任务）
400 INVALID REQUEST - [POST/PUT/PATCH]：用户发出的请求有错误，服务器没有进行新建或修改数据的操作
401 Unauthorized - [*]：表示用户没有权限（令牌、用户名、密码错误）。
403 Forbidden - [*] 表示用户得到授权（与401错误相对），但是访问是被禁止的。
404 NOT FOUND - [*]：用户发出的请求针对的是不存在的记录，服务器没有进行操作，该操作是幂等的。
406 Not Acceptable - [GET]：用户请求的格式不可得（比如用户请求JSON格式，但是只有XML格式）。
410 Gone -[GET]：用户请求的资源被永久删除，且不会再得到的。
422 Unprocesable entity - [POST/PUT/PATCH] 当创建一个对象时，发生一个验证错误。
500 INTERNAL SERVER ERROR - [*]：服务器发生错误，用户将无法判断发出的请求是否成功
```

- 错误处理
```
{
  error: "<error message>"
}
```

- 返回结果

GET /collection：返回资源对象的列表（数组）   
GET /collection/resource：返回单个资源对象   
POST /collection：返回新生成的资源对象   
PUT /collection/resource：返回完整的资源对象   
PATCH /collection/resource：返回完整的资源对象   
DELETE /collection/resource：返回一个空文档   

- 超媒体(Hypermedia API)

返回结果中提供链接,连向其他API方法api.github.com
```
{
  "message": "API rate limit exceeded for 183.240.196.114. (But here's the good news: Authenticated requests get a higher rate limit. Check out the documentation for more details.)",
  "documentation_url":"https://developer.github.com/v3/#rate-limiting"
}
```
尽量使用JSON,避免使用XML

- 避免多级url   
除了第一级,其他级别都用查询字符串表述  
GET /authors/12?categories=2

- 幂等   
幕等性,N次变换与1次变换的结果相同.    
HTTP的幕等性(Idempotence)就是指一次或多次请求某一资源应该具有同样的副作用   
HTTP中的GET,PUT,DELETE是安全的,幂等的任意多次对同一资源的GET请求都不会刀子资源的状态变化,不用担心重复操作带来不同的语义及结果.POST,PATCH操作就是不安全的,当客户端向服务器发出请求后,服务器没有响应或者返回错误代码,客户端是不能安全的重复操作的,一定只能重新与服务器确认现在的资源状态才能决定下一步的操作
