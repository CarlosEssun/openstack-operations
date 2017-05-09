

# `openstack restful api`使用
## 查找`openstack`的`restful api`开发手册
* [openstack-devloper](https://developer.openstack.org/api-ref/)
* 使用`curl`[curl-openstack](https://developer.openstack.org/api-guide/quick-start/api-quick-start.html)

# 传输格式及参数
* 查看上面的`url`看一下每一个请求在需要什么*必要*的参数，结构如何
* 可以通过在命令加上`--debug` 查看所需要参数
  如：
  ```
 $ openstack --debug service show 03920a98030d444fa4a0dd802a8b6b84
  ....
  REQ: curl -g -i -X GET http://controller01:35357/v3/services/03920a98030d444fa4a0dd802a8b6b84 -H "User-Agent: python-keystoneclient" -H "Accept: application/json" -H "X-Auth-Token: {SHA1}6b28ed9cfb0498654fd528d780f1fad68cdcc5c0"
"GET /v3/services/03920a98030d444fa4a0dd802a8b6b84 HTTP/1.1" 200 243
RESP: [200] Date: Tue, 09 May 2017 05:25:29 GMT Server: Apache/2.4.6 (CentOS) mod_wsgi/3.4 Python/2.7.5 Vary: X-Auth-Token x-openstack-request-id: req-f0152318-fe5d-47af-809c-a915dceca942 Content-Length: 243 Keep-Alive: timeout=5, max=97 Connection: Keep-Alive Content-Type: application/json 
RESP BODY: {"service": {"description": "Orchestration", "links": {"self": "http://controller01:35357/v3/services/03920a98030d444fa4a0dd802a8b6b84"}, "enabled": true, "type": "cloudformation", "id": "03920a98030d444fa4a0dd802a8b6b84", "name": "heat-cfn"}}

  ```
# 使用示例


