
### Last-Modified

在浏览器第一次请求某一个URL时，服务器端的返回状态会是200，内容是你请求的资源，同时有一个Last-Modified的属性标记(HttpReponse Header)此文件在服务期端最后被修改的时间：
```
Last-Modified:Tue, 24 Feb 2009 08:01:04 GMT
```
客户端第二次请求此URL时，根据HTTP协议的规定，浏览器会向服务器传送If-Modified-Since报头(HttpRequest Header)，询问该时间之后文件是否有被修改过：
```
If-Modified-Since:Tue, 24 Feb 2009 08:01:04 GMT
```
如果服务器端的资源没有变化，则自动返回HTTP304（NotChanged.）状态码，内容为空，这样就节省了传输数据量。当服务器端代码发生改变或者重启服务器时，则重新发出资源，返回和第一次请求时类似。从而保证不向客户端重复发出资源，也保证当服务器有变化时，客户端能够得到最新的资源。

注：如果If-Modified-Since的时间比服务器当前时间(当前的请求时间request_time)还晚，会认为是个非法请求

### Etag

HTTP协议规格说明定义ETag为`被请求变量的实体标记`。简单点即服务器响应时给请求URL标记，并在HTTP响应头中将其传送到客户端，类似服务器端返回的格式：
```
Etag:"5d8c72a5edda8d6a:3239"
```
客户端的查询更新格式是这样的
```
If-None-Match:"5d8c72a5edda8d6a:3239"
```
如果ETag没改变，则返回状态304

简单来说:

在客户端首次发出请求后，HttpReponse Header中包含Etag:"5d8c72a5edda8d6a:3239"标识，
告诉Client端，你拿到的这个的资源有表示ID：5d8c72a5edda8d6a:3239。
当下次需要发请求索要同一个URI的时候，浏览器同时发出一个If-None-Match报头(Http RequestHeader)此时包头中信息包含上次访问得到的
Etag:"5d8c72a5edda8d6a:3239"标识

如果If-None-Match为False，不返回200，返回304(Not Modified)

### Expires
```
Expires:Thu, 02 Apr 2019 05:14:08 GMT
```
需和Last-Modified结合使用。
用于控制请求文件的有效时间，当请求数据在有效期内时客户端浏览器从缓存请求数据而不是服务器端.当缓存中数据失效或过期，才决定从服务器更新数据


### Cache-Control: max-age=xxx 和 Expires

Expires = xxx，HTTP 1.0 版本，缓存的载止时间，允许客户端在这个时间(秒)之前不去检查（发请求）
max-age = xxx，HTTP 1.1版本，资源在本地缓存多少秒。

如果max-age和Expires同时存在，则会被Cache-Control的max-age覆盖。

Expires 的一个缺点就是，返回的到期时间是服务器端的时间，这样存在一个问题，如果客户端的时间与服务器的时间相差很大，那么误差就很大，
所以在HTTP 1.1版开始，使用Cache-Control: max-age=xxx 替代
