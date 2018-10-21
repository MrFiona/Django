### Views的作用 ###
* MTV/MVT,MVC中是控制层，
* views写什么代码？
 * 操作model，渲染模板 
### 简单的命名规范  ###
* **_view 专门渲染模板的
* 动词 专门处理请求的 
### url格式 ###
* 协议://主机:端口/路径?参数  
* uri （同一资源标识符）是url的超集：	

				file:///C:/Users/admin/Desktop/%E8%AE%A2%E5%8D%95%E7%95%8C%E9%9D%A2.html

### 在浏览器中输入url，回车，做了什么事？ ###
* 浏览器输入域名 www.bjsxt.com   
* 因为在互联网中唯一表示一个服务器：ip
* 域名解析 DNS解析  
 * 就是根据域名找到ip   类似python中的元组[(域名,ip),(域名,ip)]
 * 寻找过程
 * HOSTS文件中找  （域名 和ip组）


					127.0.0.1 www.baidu.com

		![](https://i.imgur.com/dZguxTu.png)
 * 去DNS服务器找
 
			 ![](https://i.imgur.com/4pOzyFz.png)
 * 常用的dns服务器

					114.114.114.114
					8.8.8.8
 * dns劫持,dns污染		
* 找到ip+端口(80)
* 建立socket请求
 * http属于应用层协议，http协议是基于TCP/IP 
* 服务器返回响应
* 浏览器将响应存储到本地（完全的下载下来）
		chrome://cache/
* 浏览器读取下载下来的文件，渲染
* 用户就看到最终的网站了
* socket就关闭了。（HTTP属于无状态协议）
### OSI7层参考模型 ###
* 理论上的
![](https://i.imgur.com/JeqVKgd.jpg)
			
* 分秋
 * UDP （局域网一般使用udp）
 * A电脑的传输层 --加头（封装）-> A电脑的网络层--(加头封装)-->数据链路层--加头封装->物理层--网线--》B电脑物理层--(解包)--》B电脑的数据链路层-解包->B网络层--解包--》B传输层--》解包  飞球获得了自己的数据
 * p2p  点对点传输
* web服务器
 * http 
 * A应用层-加包->。。。。A物理层 --->服务器的物理层--->服务器的传输层（TCP）   
 * HTTP服务器在TCP/IP（socket）
 * CS    Client  Server
 * 不一定都是对称的
* TCP/UDP 区别
 * TCP属于长连接，有状态，安全
 * UDP类似发邮件（数据报） ，不安全
 * TCP应用  web服务器，FTP等
 * UDP应用，视频，语音。卡一下（打电话听不清，丢包了）
 * UDP要比TCP快
 * 传输层
* TCP/IP协议簇
 *  网络传输层
 *  传输层(TCP/UDP)作用
 	* 传输数据 传给谁?(ip+port)  找到ip主机的port的监听程序
 *  网络ip层的作用
 	*  根据域名找到ip
### TCP/IP的四层模型 ###
* 对OSI7层参考模型的简化
* 实际应用的

	![](https://i.imgur.com/zW9Ifxn.jpg)

### HTTP协议 ###
* HTTP属于应用层协议，依赖底层的TCP/IP
* HTTP特点
 * 无状态
 *  因为socket建立完成之后，数据传输之后，立马就关闭了socket
 *  下次过来再也不认识了
* HTTP1.0 和HTTP1.1
 *  HTTP1.0 的特点
 *  建立socket链接-->传输--》关闭
 *  HTTP1.0加一个请求头，那么效果就和HTTP1.1一样
 *  Connection:keep-alive  (请求头是给服务器看的)
 * HTTP 1.1
  * 在现代的浏览器中，输入一个url会传输好多个不同的数据， HTTP1.0就有点坑
  *  建立socket链接-->传输-->传输--传输。。。。。--》关闭

### HTTP请求，响应报文 ###
* 请求报文


				GET http://127.0.0.1:8000/ HTTP/1.1 # 请求行 \r\n
				Host: 127.0.0.1:8000
				Connection: keep-alive
				Cache-Control: max-age=0
				User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.62 Safari/537.36
				Upgrade-Insecure-Requests: 1
				Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
				Accept-Encoding: gzip, deflate, br
				Accept-Language: zh-CN,zh;q=0.9
				Cookie: token="2|1:0|10:1512638937|5:token|44:NzU5NmQ0MDRlZGMwNGJiOWFiZDk3OWU5NjAzYjg4MzM=|e878431f88de4e3350207062ba3488247683636900f23afc6ff051b0144ea399"; csrftoken=d53nFxAL5cnIBOpt5mGsfz5zR9sxo4npRO8vPOTj0flmfOmFm1bh0BIh2owTVbAE
				
				# 请求空行
				# 请求体 （post请求有）
 * 请求行				
				
				GET http://127.0.0.1:8000/ HTTP/1.1 # 请求方式GET 地址 http协议版本

 * 请求头（浏览器告诉服务器的一些信息）


				Host: 127.0.0.1:8000 # 主机地址
				Connection: keep-alive # 保持连接
				User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.62 Safari/537.36    # 浏览器是谁？ 爬虫
				Upgrade-Insecure-Requests: 1 # http升级到HTTPS
				Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8 # 浏览器告诉服务器我能接受什么
				Accept-Encoding: gzip, deflate, br  # 浏览器告诉服务器，我能解码gzip压缩后的内容
				Cookie: token="2|1:0|10:1512638937|5:token|44:NzU5NmQ0MDRlZGMwNGJiOWFiZDk3OWU5NjAzYjg4MzM=|e878431f88de4e3350207062ba3488247683636900f23afc6ff051b0144ea399"; csrftoken=d53nFxAL5cnIBOpt5mGsfz5zR9sxo4npRO8vPOTj0flmfOmFm1bh0BIh2owTVbAE # Cookie中值，浏览器在访问的时候会自动带给服务器 

				#ip没有在请求头，在socket链接中

 * 请求空行
 * 请求体

	
			POST http://www.sxt.cn/index/login/login.html HTTP/1.1
			Host: www.sxt.cn
			Proxy-Connection: keep-alive
			Content-Length: 33
			Cache-Control: max-age=0
			Origin: http://www.sxt.cn
			Upgrade-Insecure-Requests: 1
			Content-Type: application/x-www-form-urlencoded
			User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.62 Safari/537.36
			Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
			Referer: http://www.sxt.cn/index/login/login.html
			Accept-Encoding: gzip, deflate
			Accept-Language: zh-CN,zh;q=0.9
			Cookie: UM_distinctid=15e78e240fd697-0ffbf1e3cbd676-5d153b16-100200-15e78e240fe9c2; 53gid2=10536421375018; 53revisit=1506596663864; CNZZDATA1261969808=928198901-1505266650-null%7C1512545966; PHPSESSID=pm7frp35o28p9mtmu8g0dov256
			# 请求空行
			user=18235444249&password=1111111   # 请求体

* 响应报文

		HTTP/1.0 200 OK
		Date: Mon, 18 Dec 2017 03:08:55 GMT
		Server: WSGIServer/0.1 Python/2.7.13
		X-Frame-Options: SAMEORIGIN
		Content-Type: text/html; charset=utf-8
		Content-Length: 225
		
		<!DOCTYPE html>
		<html lang="en">
		<head>
		    <meta charset="UTF-8">
		    <title>Title</title>
		</head>
		<body>
		<form>
		    <input type="text" name="kw"><input type="submit" value="百度一下你就知道">
		</form>
		</body>
		</html> 
 * 响应行

			HTTP/1.0 200 OK  #HTTP协议 和连接断不断无关（请求头告诉的） 状态码200 状态的描述
 * 响应头

	
			Date: Mon, 18 Dec 2017 03:08:55 GMT # 时间
			Server: WSGIServer/0.1 Python/2.7.13 # 服务器的名字
			X-Frame-Options: SAMEORIGIN  # 只允许同源的iframe标签显示
			Content-Type: text/html; charset=utf-8 # 服务器一定要告诉浏览器什么格式的
			Content-Length: 225 # 响应的长度，下载做进度的时候使用
 * 响应空行

 * 响应体
 

			HTTP/1.0 200 OK
			Date: Mon, 18 Dec 2017 03:08:55 GMT
			Server: WSGIServer/0.1 Python/2.7.13
			X-Frame-Options: SAMEORIGIN
			Content-Type: text/html; charset=utf-8
			Content-Length: 225
			# 响应空行
			<!DOCTYPE html>#响应体
			<html lang="en">
			<head>
			    <meta charset="UTF-8">
			    <title>Title</title>
			</head>
			<body>
			<form>
			    <input type="text" name="kw"><input type="submit" value="百度一下你就知道">
			</form>
			</body>
			</html> 


### 请求方式 ###
* get/post


			#request_line = 'POST http://www.sxt.cn/index/login/login.html HTTP/1.1'
			#request_line = 'GET http://www.sxt.cn/index/login/login.html HTTP/1.1'
			request_line = 'DELETE http://www.sxt.cn/index/login/login.html HTTP/1.1'
			request_method = request_line.split()[0].lower()
			class IndexHandler():
				def get(self):
					print '处理get请求'
				def post(self):
					print '处理post请求'
				def delete(self):
					print '处理了delete请求'
				

			indexHandler = IndexHandler()
			if hasattr(indexHandler,request_method):
				getattr(indexHandler,request_method)()
			else:
				print '405方法没有实现'


![](https://i.imgur.com/5UUYGth.png)

* get 查询
* post 修改，添加
* delete  删除
* head  只是获得请求头

### 状态码 ###
[https://www.cnblogs.com/lxinxuan/archive/2009/10/22/1588053.html](https://www.cnblogs.com/lxinxuan/archive/2009/10/22/1588053.html)
* 200  成功
* 300 
	* 301 永久重定向
	* 302 重定向
	* 304  not modified  读取浏览器缓存
* 400 错误请求
 * 404 not found 找不到
 * 405 方法没有实现
 * 403 禁止访问，csrf
* 500服务器崩了
			

###请求获得 ###
* 获得所有的请求头

				request.META # 获得所有的请求头 dict






				TMP:C:\Users\admin\AppData\Local\Temp
		PYTHONIOENCODING:UTF-8
		COMPUTERNAME:ADMIN-PC
		wsgi.multiprocess:False
		RUN_MAIN:true
		HTTP_COOKIE:token="2|1:0|10:1512638937|5:token|44:NzU5NmQ0MDRlZGMwNGJiOWFiZDk3OWU5NjAzYjg4MzM=|e878431f88de4e3350207062ba3488247683636900f23afc6ff051b0144ea399"; csrftoken=d53nFxAL5cnIBOpt5mGsfz5zR9sxo4npRO8vPOTj0flmfOmFm1bh0BIh2owTVbAE
		USERDOMAIN:admin-PC
		SERVER_PROTOCOL:HTTP/1.1
		SERVER_SOFTWARE:WSGIServer/0.1 Python/2.7.13
		PSMODULEPATH:C:\Windows\system32\WindowsPowerShell\v1.0\Modules\
		SCRIPT_NAME:
		COMMONPROGRAMFILES:C:\Program Files\Common Files
		PROCESSOR_IDENTIFIER:Intel64 Family 6 Model 58 Stepping 9, GenuineIntel
		REQUEST_METHOD:GET
		VBOX_MSI_INSTALL_PATH:D:\VirtualBox\
		PROGRAMFILES:C:\Program Files
		PROCESSOR_REVISION:3a09
		SYSTEMROOT:C:\Windows
		QUERY_STRING:
		PATH:C:\Python27\Scripts;C:\Program Files (x86)\Common Files\NetSarang;C:\ProgramData\Oracle\Java\javapath;C:\Windows\system32;C:\Windows;C:\Windows\System32\Wbem;C:\Windows\System32\WindowsPowerShell\v1.0\;C:\Python27;C:\Python27;C:\Python27\Lib\site-packages;C:\Python27\Lib;C:\Python27\DLLs;C:\Python27\Lib\lib-tk;C:\Program Files (x86)\MySQL\MySQL Server 5.5\bin;C:\Program Files (x86)\Java\jdk1.8.0_144\bin;C:\Users\admin\Anaconda3;C:\Users\admin\Anaconda3\Scripts;C:\Program Files\Git\cmd\;C:\Program Files\nodejs\;C:\Users\admin\AppData\Local\Programs\Fiddler;C:\Users\admin\AppData\Roaming\npm
		PYTHONUNBUFFERED:1
		PROGRAMFILES(X86):C:\Program Files (x86)
		CONTENT_LENGTH:
		HTTP_UPGRADE_INSECURE_REQUESTS:1
		HTTP_CACHE_CONTROL:max-age=0
		HTTP_CONNECTION:keep-alive
		REMOTE_HOST:
		TEMP:C:\Users\admin\AppData\Local\Temp
		REMOTE_ADDR:127.0.0.1
		COMMONPROGRAMFILES(X86):C:\Program Files (x86)\Common Files
		PROCESSOR_ARCHITECTURE:AMD64
		wsgi.url_scheme:http
		ALLUSERSPROFILE:C:\ProgramData
		SERVER_PORT:8000
		LOCALAPPDATA:C:\Users\admin\AppData\Local
		HOMEPATH:\Users\admin
		DJANGO_SETTINGS_MODULE:requestheaderdemo.settings
		PROGRAMW6432:C:\Program Files
		USERNAME:admin
		HTTP_ACCEPT:text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
		LOGONSERVER:\\ADMIN-PC
		COMSPEC:C:\Windows\system32\cmd.exe
		PROGRAMDATA:C:\ProgramData
		PYTHONPATH:D:\django_workspace\django108\day06\am\requestheaderdemo;C:\Python27;C:\Python27\Lib\site-packages;C:\Python27\Lib;C:\Python27\DLLs;C:\Python27\Lib\lib-tk;C:\Python27\Scripts
		wsgi.multithread:True
		 			CSRF_COOKIE:d53nFxAL5cnIBOpt5mGsfz5zR9sxo4npRO8vPOTj0flmfOmFm1bh0BIh2owTVbAE
		wsgi.input:<socket._fileobject object at 0x0000000003E43E58>
		HTTP_USER_AGENT:Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.62 Safari/537.36
		HTTP_HOST:127.0.0.1:8000
		SESSIONNAME:Console
		PATHEXT:.COM;.EXE;.BAT;.CMD;.VBS;.VBE;.JS;.JSE;.WSF;.WSH;.MSC
		PATH_INFO:/
		FP_NO_HOST_CHECK:NO
		WINDIR:C:\Windows
		wsgi.file_wrapper:wsgiref.util.FileWrapper
		HTTP_ACCEPT_ENCODING:gzip, deflate, br
		wsgi.version:(1, 0)
		HOMEDRIVE:C:
		APPDATA:C:\Users\admin\AppData\Roaming
		SERVER_NAME:admin-PC
		wsgi.run_once:False
		__COMPAT_LAYER:DisableUserCallbackException
		SYSTEMDRIVE:C:
		GATEWAY_INTERFACE:CGI/1.1
		HTTP_ACCEPT_LANGUAGE:zh-CN,zh;q=0.9
		PYCHARM_HOSTED:1
		NUMBER_OF_PROCESSORS:4
		PROCESSOR_LEVEL:6
		CONTENT_TYPE:text/plain
		wsgi.errors:<open file '<stderr>', mode 'w' at 0x0000000001EF2150>
		COMMONPROGRAMW6432:C:\Program Files\Common Files
		OS:Windows_NT
		PUBLIC:C:\Users\Public
		USERPROFILE:C:\Users\admin
 * django的header有一个特点（HTTP_USER_AGENT）
  * User_Agent的应用  简单的反爬虫 
  * REMOTE_ADDR 获得ip，屏蔽异常的ip
  * Referer 网页是从那边进来的（放盗链）HTTP_REFERER

* 请求参数
 *  GET请求参数（url后面）

			request.GET.get('kw')  # 爱情
			request.GET.getlist('aihao')  #['打球','打游戏']
		    		
 * post请求参数（放在请求体）
 
		![](https://i.imgur.com/zH56k3w.png)
 * post请求的html中的url和urls中的url，一定要一致（/记得加），
 * post 403 csrf相关的


			request.POST.get('kw')  # 爱情
			request.POST.getlist('aihao')  #['打球','打游戏']
				
* request.GET和request.POST 都是QueryDict，
* get和post区别
 * get请求的请求参数在url后面，url长度不能超过2k
 * post请求的请求在请求体中 
					
### 文件上传 ###
* html代码（enctype）服务器才知道你上传了文件

			<form method="post" action="/upload/" enctype="multipart/form-data">
			    <input type="text" name="name"><br>
			    <input type="file" name="img"><br>
			    <input type="submit">
			</form>
* request.FILES      MultiValueDict

				#files=request.FIELS.getlist('img')
				file = request.FIELS.get('img')
				#file InMemoryUploadFile 
				content = file.read()
				with open(file.name,'wb')	as fw:
					fw.write(content)

* 动态创建文件夹


				def upload(request):
				    username = request.POST.get('name')
				    import os
				    if not os.path.exists(username):
				        os.makedirs(username)
				    f = request.FILES.get('img') # 获得浏览器上传的文件
				    with open(os.path.join(os.getcwd(),username,f.name),'wb') as fw:
				        fw.write(f.read())
				    return HttpResponse('上传成功')
* 上传大文件呢？（服务器内存）


					    with open(os.path.join(os.getcwd(), username, f.name), 'wb') as fw:
					        # 分片读取
					         for chunk in f.chunks():
					             fw.write(chunk)
							
			
### 响应 ###
* 如何HttpResponse里面的内容


			def warpper(fun):
			    def __wraper(*args,**kwargs):
			        response =fun(*args,**kwargs)
			        content = '<font color="red">%s</font>'%response.content
			        response.content = content
			        return response
			    return __wraper
			@warpper
			def response(request):
			    respons=HttpResponse('乡音提提')
			    return respons

* 如何设置响应头


			    respons=HttpResponse('乡音提提')
				# 不让别人知道服务器到底是使用什么语言写的
    			respons.setdefault('Server':'张三的服务器')
 * 设置content-type  (MIMETYPE) (*/*)

		 respons=HttpResponse('乡音提提',content_type='text/html;charset=gbk')
 * 设置响应吗


		 		 respons=HttpResponse('乡音提提',content_type='text/html;charset=gbk',status=400)


* 显示图片


			def showimg(request):
			    with open('c.jpg','rb') as fr:
			        content=fr.read()
			    return HttpResponse(content,content_type='image/jpeg')

* 显示json（前后端分离使用）


		 def showjson(request):
		    user = {'name':'zhansgan','age':10}
		    import json
		    content = json.dumps(user) # 字典对象转换成json
		    return HttpResponse(content,content_type='application/json')
			#json.loads(字符串) # 字符串转换成对象
* 使用jsonresponse

			def showjson(request):
			    user = {'name':'zhansgan','age':10}
			    # import json
			    # content = json.dumps(user) # 字典对象转换成json
			    # return HttpResponse(content,content_type='application/json')
			    return JsonResponse(user)
* 下载文件（浏览器不认识的默认下载）
 

			def showmovie(request):
			    with open('a.avi','rb') as fr:
			        content = fr.read()
			    # Content - Disposition: attachment;
			    # filename =“filename.xls”
			    response= HttpResponse(content)
				# 下载文件的头
			    response.setdefault('Content-Disposition','attachment;filename="adsad.avi"')
			    return  response 
* 文件下载

			from django.http import FileResponse
			 response = FileResponse(open('myfile.png', 'rb'))

* 404,500,403，


			def responseerr(request):
			    raise Http404()
			def responseerr(request):
			    return HttpResponseForbidden()

			
			def responseerr(request):
			    raise  Exception(';asd')
* 定制错误界面
 * DEBUG = False
 * ALLOWED_HOSTS = ['*']
 * 在模板中写即可404.html，500.html
* 302重定向
 *  url地址会发生改变
 *  浏览器访问我们的url，我告诉浏览器访问其他url
 *  一般post请求处理完之后，不是直接render，而是重定向到其他地址（避免反复的提交数据，明天详细的讲）
 

			def redir(request):
   				 return HttpResponseRedirect('http://www.bjsxt.com')

			def redir(request):
			    response = HttpResponse(status=302)
			    response.setdefault('Location','http://www.baidu.com')
			    return response
			
						
 * 重定向自己的

			
			def redir(request):
			    return HttpResponseRedirect('/login.html')

 * 作业
 * 登录模块
 * 输入账号密码--》处理登入-->成功--》重定向到用户中心--》失败的话--》冲顶向到登录界面。
 * 刷新（执行浏览器上一次请求，post请求很蛋疼，）

### Cookie ### 
* 客户端存储技术（将数据存储到浏览器的）


				def store_cookie(request):
				    # Cookie客户端的存储技术（浏览器存储）
				    response = HttpResponse()
				    response.set_cookie('user','zhangsan')
				    return response


----------
				
			GET http://127.0.0.1:8000/store/ HTTP/1.1
			Host: 127.0.0.1:8000
			Connection: keep-alive
			Cache-Control: max-age=0
			User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/62.0.3202.62 Safari/537.36
			Upgrade-Insecure-Requests: 1
			Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8
			Accept-Encoding: gzip, deflate, br
			Accept-Language: zh-CN,zh;q=0.9


	

----------
			HTTP/1.0 200 OK
			Date: Tue, 19 Dec 2017 01:57:44 GMT
			Server: WSGIServer/0.1 Python/2.7.13
			X-Frame-Options: SAMEORIGIN
			Content-Type: text/html; charset=utf-8
			Content-Length: 0
			Set-Cookie: user=zhangsan; Path=/

* 存储cookie
 * key
 * value
 * path  (存储的路径) path='/asd'name这个cookie什么时候携带呢？/asd/***  默认的话path='/' 访问网站的任何网页都会携带


			在set_cookie的时候，指定path，那么之后再访问path和path的子目录的时候才会携带
			path和cookie会不会自动携带相关，只有在访问的路径是path或者path的子目录的时候才会自动携带


 * 过期时间
 	* 会话结束过期 （浏览器一关闭数据就丢失了）
		 expires=None

 	* 设置过期时间

			max_age=60*10 （s）
			expires=datetime.datetime.today()+datetime.timedelta(days=14)

 	* 永不过期


			max_age=60*100000000000000000000
			expires=datetime.datetime.today()+datetime.timedelta(days=10000000000)
  

			
 * domain
 		  		
		
			.bjsxt.com
			sx.bjsxt.com
			sh.bjst.com
						
			单点登录: 登录一次可以访问服务器所有的系统

* 什么时候携带cookie（属于用户隐私）
 * path 访问path的目录或者子目录(/goods自动携带)(/abc不会携带)
 * expires  没有过期
 * domain 相同  (访问百度的时候，不会携带自己项目的cookie，因为127.0.0.1，百度的域名.baidu.com) tieba.baidu.com  baike.baidu.com
 * 并且的意思


* Cookie会在请求的时候自动携带

* 如和过得携带的cookie的呢？


			request.COOKIES (字典)
* 删除cookie(是服务器告诉浏览器删除)

			response.set_cookie('name','value',expires=None)  # 浏览器不关闭删除不了
			# 了解的
			response.set_cookie('name','value',max_age=-1) # 浏览器立马让cookie过期
			    response.delete_cookie(‘key’)

* 原理

			def set(request):
			   response =  HttpResponse()
			   response.setdefault('Set-Cookie','server=asdasd;Path=/')
			   return response
			# 也存储了,也能获得
			# 原来set_cookie（就是设置一个响应头为Set-Cookie）,request.COOKIES(其实就是获得请求头Cookie的值)，只不过是框架提供了简单的api
			# Cookie特点是会自动携带

* 限制
 * 单条cookie的大小有限制（存电影）
 * 同一个域名下的Cookie的数量也是有限制的（2亿存储） 
### Cookie的应用 ###
* 记录用户的浏览记录
* 推荐商品
 * 第三方咨询，提供一个连接(接口)， www.jd.com/history 
 * 不同源cookie不会携带，

### 什么时候执行get请求 ###
* a form method='get' 浏览器 回车

### 什么时候执行post请求 ###
* method = 'post'

### 浏览器中f5刷新 ###
* 执行上一次请求
### Session ###
* 服务器存储技术
*  会话 （HTTP属于无状态协议）
*  基于cookie的 
*  原理

		浏览器访问我的时候，我给浏览器的设置一个cookie （seesionid：每个浏览器都不相同），下次浏览器在访问的时候会自动在请求头携带cookie的sessionid,服务器根据sessionid去存储中查找之前用户操作的数据，将无状态的变成有状态了
* django中的session数据默认存储到数据了
* django使用session的条件
	* INSTALL_APPS   'django.contrib.sessions',
	* MIDDLEWARE     'django.contrib.sessions.middleware.SessionMiddleware',
  
* 操作
	类似字典
	request.session['asd']='asd'

		

![](https://i.imgur.com/jKKoqQD.png)


		 user = request.session.get('user','')

![](https://i.imgur.com/jukr3uz.png)

		服务器获得请求头中携带的sessionid，去数据库中django_session表中查找数据
		
 * session SessionStore
 * session.get() 类似字典
 * session['asd']='asd'	 类似字典
 * session.set_expiry()时间 过期时间 60*10（10分钟之后过期） 0（浏览器关闭就过期） None（全局的seesion过期时间2周）可以在settings中自己设置 

			SESSION_COOKIE_AGE=60 * 60 * 24 * 7 * 4
 * 删除session中部分数据

			  del request.session['age']
 * 清空session的数据

			session.clear()
 * 获得sessionid

			request.session.session_key
 * 让session失效
	
			   request.session.flush() 删除session，（数据库删除，cookie中sessionid删除（cookie的过期时间设置成-1））
 * 清空数据库中无用的session，（原因是用户手动清除了浏览器的cookie数据）？等数据库时间过期之后在执行这个命令

			# liunx脚本
			python manage.py clearsessions

 * 基于session写一个用户登录		
 * 一个路径（path）可以存储几个cookie（多个）

* 购物车例子
					 
* 储存
 * 在settings中默认使用的是


			SESSION_ENGINE = 'django.contrib.sessions.backends.db'
 
 * session默认储存在数据库 


		
			class User(object):
			    def __init__(self,acccount,password):
			        self.account = acccount
			        self.password = password
			def store(request):
			    # request.session
			    request.session['user']=User('admin','1234456')
			    return HttpResponse('存储成功')
			# 因为数据库中存储的是字符串	
 * 内存


			SESSION_ENGINE = 'django.contrib.sessions.backends.cache'
			# 可以存储自定义对象，内存不用json序列化
			# 服务器重启，数据没了
 
 * 内存+数据库（双缓存）

			SESSION_ENGINE = 'django.contrib.sessions.backends.cached_db'	
			# 内存速度快，数据库慢
			# 储存的步骤，先存到内存，在存到数据库
			# 先从内存读，在从数据库读，如果数据库读到了，放到内存
 *  file

			
 		SESSION_ENGINE = 'django.contrib.sessions.backends.file'
		SESSION_FILE_PATH = os.getcwd()
 * single_cookies

		SESSION_ENGINE = 'django.contrib.sessions.backends.signed_cookies'
		# 将数据加密，存到cookie中了（存到浏览器）	（session太浪费服务器资源了）

		![](https://i.imgur.com/iBDG5U1.png)
 * redis 

		# centos装上redis。（配置开机自启，配置后台运行，防火墙关了）

			
### 为什么python序列化这么难用 	
* 序列化  将对象转换成字符串
* 反序列化  将字符串转换成对象
* java强类型语言，python弱类型（同一个类的2的对象，这两个对象的属性，一样么）
* jsonpickle

			pip install jsonpickle



			#coding=utf-8

			class Address(object): # 继承新式类
			    def __init__(self,add):
			        self.add = add
			class User(object):
			    def __init__(self,name,add):
			        self.name = name
			        self.add = add
			
			u = User('张三',Address('北京'))
			import jsonpickle
			s= jsonpickle.encode(u)  # 序列化
			u1 = jsonpickle.decode(s) # 反序列化
			print u1

* 前后端分离

			#coding=utf-8
			class Address(object):
			    def __init__(self,province):
			        self.province = province
			
			class User(object):
			    def __init__(self,account,add):
			        self.account = account
			        self.add = add
			
			if __name__ == '__main__':
			    u = User('张三',Address('北京'))
			    import jsonpickle
			    s = jsonpickle.encode(u)
			    print s
			    u1= jsonpickle.decode(s)
			    print u1.account


 
				#序列化到数据库直接使用这个就行
				'{"py/object": "__builtin__.User", "add": {"py/object": "__builtin__.Address", "province": "\\u5317\\u4eac\\u5b66\\u751f"}, "name": "\\u5f20\\u4e09"}'

				# 如果是前后端分离的话，

				
				 u = User('张三',Address('北京'))
			    import jsonpickle
			    s = jsonpickle.encode(u,False)


				# 序列化部分字段	


			#coding=utf-8
			class Address(object):
			    def __init__(self,province):
			        self.province = province
			
			class User(object):
			    def __init__(self,account,password,add):
			        self.account = account
			        self.password = password
			        self.add = add
			    def __getstate__(self):
			        print '调用了'
			        state = self.__dict__.copy()
			        del state['password']
			        return state
			
			
			if __name__ == '__main__':
			    u = User('admin','123456',Address('北京'))
			    import jsonpickle
			    s = jsonpickle.encode(u,unpicklable=False)
			    print s


			
				




				
* 想办法让数据库能存储对象。
		* 继承

### html显示图片 ###
* 路由

		 url(r'^abc/.*$', views.show),

	
* 显示图片的	
		
		def show(request):
		    import re
		    se = re.match(r'^/abc/(.*)$',request.path)
		    path = se.group(1)
		    import  os
		    filepath = os.path.join(os.getcwd(),'static',path)
		    if not os.path.exists(filepath):
		        raise  Http404()
		    response = FileResponse(open(filepath,'rb'),content_type='image/jpg')
		    return response
* 优化的


		

		def show(request):
		    import re
		    se = re.match(r'^/abc/(.*)$',request.path)
		    path = se.group(1)
		    import  os
		    filepath = os.path.join(os.getcwd(),'static',path)
		    if not os.path.exists(filepath):
		        raise  Http404()
		    if filepath.endswith('.jpg'):
		        content_type='image/jpg'
		    elif filepath.endswith('.css'):
		        content_type='text/css; charset=UTF-8'
		    elif filepath.endswith('.js'):
		        content_type='text/javascript; charset=UTF-8'
		    response = FileResponse(open(filepath,'rb'),content_type=content_type)
		
		    return response

* abc(url的地址)--》(真实static文件夹是映射关系)



###  static ###
* 存放全是静态的东西，css，js，图片
* static_url （给浏览器使用的）
* STATICFILE_DIRS=[] 真实目录

		
		# 1：n的映射
		STATIC_URL = '/static/'
		STATICFILES_DIRS=[
		    os.path.join(os.getcwd(),'abc'),
		    os.path.join(os.getcwd(),'dasd')
		]

* 实际配置


			# STATIC_URL = '/abc/' #url地址
			#
			# STATICFILES_DIRS=[ # 映射的真实的物理目录
			#     os.path.join(os.getcwd(),'abc'),
			#     os.path.join(os.getcwd(),'dasd')
			# ]
			STATIC_URL='/static/'
			STATICFILES_DIRS=[
			    os.path.join(os.getcwd(),'static')
			]
* 默认304，没有改变，直接让浏览器读取缓存
* os
 * os.path.getmtime('file') 获得修改的时间
 * os.path.getctime('file') 创建的时间
 * os.path.getatime('file') 访问的时间
 * os.path.getsize('file')  文件大小

### 捕获传参数 ###
* resetful url (url进行设计)
* http://127.0.0.1:8000/post/2017/11/12
			
			url(r'^post/.*$', views.get_post),


			def get_post(request):
			    import re
			    match = re.match('/post/(\d{4})/(\d{1,2})/(\d{1,2})',request.path)
			    print match.group(1),match.group(2),match.group(3)
			    return  HttpResponse('帖子:')
* 

* django在url中做了正则

		    url(r'^post/(\d{4})/(\d{1,2})/(\d{1,2})$', views.get_post),


			def get_post(request,year,month,day):
			    return HttpResponse(u'帖子:%s-%s-%s'%(year,month,day))
* staticdemo

		
 			url(r'^static/(.*)$',views.show)

			def show(request,path):
			    import os
			    filepath = os.path.join(os.getcwd(),'static',path)
			    if not  os.path.exists(filepath):
			        raise Http404()
			
			    response = FileResponse(open(filepath,'rb'),content_type='image/jpg')
			
			    return response
* 具名捕获（不推荐使用）

						
		 url(r'^post/(?P<month>\d{4})/(?P<year>\d{1,2})/(?P<day>\d{1,2})$', views.get_post),

			
		match = re.match(r'^post/(?P<month>\d{4})/(?P<year>\d{1,2})/(?P<day>\d{1,2})$','post/2017/11/12')

		match.groupdict()
		# 打印的结果
		{'month': '2017', 'day': '12', 'year': '11'}
* 如果url配置中有捕获的话，会将捕获的值当作参数传给方法

* resetful url的风格		
	* 廖雪峰	 resetful url	url的格式怎么设计

### url函数讲解 ###
* 正则
* 处理的函数
* kwargs 关键传参

			    url(r'^movie/(\w+)$',views.movie_view,kwargs={'db':'电影的数据库'}),
				
				def movie_view(request,category,db):
				    print db
				    return HttpResponse(u'电影:%s'%category)

					# 可以动态的传递参数
				url(r'^movie/(\w+)$',views.movie_view,kwargs={'config':os.path.join(os.getcwd(),'config','1.ini')}),
			
				# 给同一个模块下所有的方法都传默认参数

				  url(r'^post/', include('post.urls'),kwargs={'config':'configasdsad'}),
* name
			# 给url起了一个别名（子路由）
			 url(r'^secondasdsa',view=views.secod_view,name='sen'),
    		# 主路由的
			 url(r'^re/', include('redemo.urls',namespace='re')),
		  	# re:sen
			# post:sen		
			from django.urls.base import reverse
			url = reverse('re:sen') # 准换成地址

			HttpRedirctResponse(url)
### 逆向解析 ###
* 根据re:sen 获得真实的地址（模块之间，url之间达到了解耦）
* reverse(re:sen)  命名空间：地址的名字
	* 在主路由给每一个模块，在include函数中设置namespace='re'
	* 在这个模块子路由中，在url函数中设置name=‘sen’ 给url起别名

### url路由的流程 ###
* url----》settings有ROOT_URLCONF （根路由）---》分发给子路由--》最终处理的函数

* 树形结构

	![](https://i.imgur.com/TrUe52i.png)

* 路由器的工作原理

	# ip---> 传输层报文中封装了ip（从哪来的到哪去的）

	手机-wifi--》路由器---》电信的路由器---》获得数据---》返回给电信的路由器---》路由器---》手机
* http请求报文


![](https://i.imgur.com/WEeWJAX.jpg)
* ip报文

![](https://i.imgur.com/hGGJToW.jpg)

### static原理  ###
* 能访问自己的STATIC文件夹，如果内容没有发生改变返回304，让浏览器读取缓存，否则的话发送新内容


			#coding=utf-8
		from django.http.response import HttpResponse, Http404, HttpResponseNotModified
		from .settings import MYFILE
		
		# MIME_TYPE={'jpg':'image/jpg'}
		def get_content_type(filepath):
		    content_type=''
		    if filepath.endswith('.jpg'):
		        content_type='image/jpg'
		    elif filepath.endswith('.css'):
		        content_type='text/css;charset=UTF-8'
		    elif filepath.endswith('.js'):
		        content_type='text/javascript;charset=UTF-8'
		    elif filepath.endswith('.html'):
		        content_type = 'text/html;charset=UTF-8'
		    return content_type
		
		def static_view(request,path):
		    import os
		    filepath = os.path.join(os.getcwd(),MYFILE,path)
		    if not  filepath:#文件找不到
		        raise Http404()
		
		
		    IF_MODIFIED_SINCE = request.META.get('HTTP_IF_MODIFIED_SINCE', '')
		    last_modified = str(os.path.getmtime(filepath))
		    if IF_MODIFIED_SINCE == last_modified: #文件没有修改
		        return  HttpResponseNotModified()
		    else:
		        #文件修改了，重新返回内容
		        content_type = get_content_type(filepath)
		        with open(filepath, 'rb') as fr:
		            content = fr.read()
		        response = HttpResponse(content, content_type=content_type)
		        # 将时间发给浏览器
		        response.setdefault('Last-Modified', last_modified)
		        return response
