

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
1. 获取`token`
```python

      def get_token():
        auth = {
            "auth": {
                "identity": {
                    "methods": ["password"],
                    "password": {
                        "user": {"name": self.__user,
                                 "domain": {"name": self.__domain},
                                 "password": self.__password
                                 }
                    }
                }
            }
        }
        params = json.dumps(auth)
        try:
            headers = {"Content-type": "application/json", "Accept": "application/json;charset=UTF-8"}
            __url = self.__url + settings.AUTH_PRE
            req = urllib2.Request(__url, params, headers)
            response = urllib2.urlopen(req, timeout=10)
            token = response.info().getheader("X-Subject-Token")
            return token
	except Exception as e：
	  loger.error(e)
```
2. 获得带有区域的`token`之前必须要找到对应`project id`
```python

	 def get_project_id( _token):
	 try:
	    if isinstance(_token, str):
		headers = {"Content-type": "application/json",
			   "Accept": "application/json;charset=UTF-8",
			   "X-Auth-Token": _token,
			   'User-Agent': 'Mozilla/5.0 (Windows; U; Windows NT 6.1; en-US; rv:1.9.1.6) '
					 'Gecko/20091201 Firefox/3.5.6'
			   }
		__url = self.__url + settings.PROJECT_PRE
		response = requests.get(__url, headers=headers)
		res_json = response.json()
		return res_json['projects'][0]['id']
	    else:
		settings.LOGGER.error("get token at {} operation failed ".format(inspect.stack()[1][3]))
	 except Exception as e:
	    settings.LOGGER.error(e)
```
3. 当使用带有project id 的区域认证时，就可以取到`catalog`
```python

      def get_catalog(self, *args, **kwargs):
        auth = {
            "auth": {
                "identity": {
                    "methods": ["password"],
                    "password": {
                        "user": {"id": kwargs.get('id', ''),
                                 "password": self.__password
                                 }
                    }
                },
                "scope": {
                    "project": {
                        "id": kwargs.get('project_id', '')
                    }
                }
            }
        }
        data = json.dumps(auth)
        headers = {"Content-type": "application/json", "Accept": "application/json;charset=UTF-8"}
        __url = self.__url + settings.AUTH_PRE
        try:
            req = urllib2.Request(__url, data, headers=headers)
            response = urllib2.urlopen(req, timeout=10)
            token = response.info().getheader("X-Subject-Token")
            response.encoding = 'utf-8'
            response_json = json.loads(response.read())
            response_json = self.__byteify(response_json)
            response_json['authtoken'] = token
            # print response_json
            return response.getcode(), response_json
        except Exception as e:
            settings.LOGGER.error(e)

```
