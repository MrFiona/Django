### models ###
* models数据模型层

### ORM ###
* 对象关系映射(object relationship mapping),其实就是类和对象与表和记录映射
* 实现了数据模型和数据库的解耦，数据模型的设计不需要依赖于特定的数据库，通过简单的配置就以轻松更换数据库。（django框架会根据settings中设置的不同的数据库，产生不同的sql语句）
* 以操作对象的形式操作表结构
###  更换mysql数据库 ###
* sqlite数据库属于文件型数据库
* Mysql/Oracle 底层使用的(TCP 连接,socket),（连接池）（数据的连接库如果不使用了要关了，因为来一个连接，Mysql服务器就创建端口处理socket，计算机总共有65535个）
* 如何更换mysql


			DATABASES = {
			    'default': {
			        'ENGINE': 'django.db.backends.mysql', # 数据库的连接器（PyMysql）
			        'NAME':'usertest',# 数据库的名字
			        'HOST':'127.0.0.1', # 数据库主机地址
			        'PORT':'3306', # 数据库端口
			        'USER':'root', # 数据库账号
			        'PASSWORD':'admin' # 数据库密码
			        # 哪台主机的那个端口，那个数据库（账号+密码）
			    }
			}
 * 关系型数据库，表与表之间有关系（主外键关系等）。
 * 非关系型数据库，k=v （很像讲过的字典）
 * django支持常用的数据库，Mysql，Sqlite，redis，
### INSTALL_APPS配置模块的作用 ###
* 和model先关的，如果要操作数据库或者操作模型
* 和模板相关的
### 根据数据库产生模型 ###
* 如果是已有的数据库，如何产生数据模型

			python manage.py inspectdb > movie/models.py
										 模块/models.py	
* 他会将所有的表产生的模型都放到一个文件里面
* 删除一些django默认的表，删除一些和当前模块没有关系的
* auth_开头的是  和权限相关表  是  'django.contrib.auth',
* django_开头的是  django自身的一些表，
* django_migrations 记录了每个模块的迁移记录
* 迁移失败
### 兼容 ###
* python2

			pip install Python-Mysql 

			
* python3 
		
		__init__目录下

		import pymysql
		pymysql.install_as_MySQLdb()

* 去网上下载轮子.whl	

### 模型的定义  ###
* 一定要在settings的INSTALL_APPS中配置
* 定义模型

				# -*- coding: utf-8 -*-
				from __future__ import unicode_literals
				
				from django.db import models
				
				# Create your models here.
				
				class User(models.Model):
				    name = models.CharField(max_length=20)
* 产生迁移文件

			python manage.py makemigrations
* 如何查看迁移文件对应的sql语句

			python manage.py sqlmigrate modeldemo 0001
* 执行迁移文件产生表

			python manage.py migrate
* 在非模块的使用orm
	* 在INSTALL_APPS配置
	* 产生迁移文件

			python manage.py makemigrations moduledemo

   * 执行迁移文件
  			python manage.py migrate moduledemo

### django orm常用的字段 ###
* 关系
 * 1:1
 * 1：n  外键放在多的里面
 * n:n 
* 字段
 * id 不写的话  会自动创建主键自增的id 
 * charfield （字符串） max_length=20
 * textfiled  没有长度限制的
 
			class Category(models.Model):
			    name = models.CharField(max_length=20,unique=True)
			
			class Post(models.Model):
			    title = models.CharField(max_length=100)
			    content = models.TextField()
			    created = models.DateField()
			    category = models.ForeignKey(Category)
			

			cate = Category.objects.all()[0]
			# 时间：2017-11-11，外键的：对象
			post = Post(title='python数据结构',content='python的数据结构，列表,元组，字典，集合',created='2017-12-12',category=cate)
			post.save()

			# 类别获得帖子
			cate.post_set.all() 类别下所有的帖子
			# 帖子获得类别
			post.category 
	
			


* 属性
 * unique = True 是唯一的  

### 分页查询 ###

		select * from movie limit 0,20
		# 从第几个开始取，取多少个
### 查看orm执行的sql语句 ###


		def showsql():
			from django.db import connection
			queries = connection.queries
			print queries[-1]['sql']

### 单表查询 ###
* 所有的继承自models.Model的类 都会有一个叫objects（管理器）
* 查询
* 查询一个  （有且只能有一个）（少于一个或者多余一个都报错）
	
			Movie.objects.get(mid=147)
			
			SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`, `movie`.`mlink` FROM `movie` WHERE `movie`.`mid` = 147

* 获得第一个

			Movie.objects.first()	

			SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`, `movie`.`mlink` FROM `movie` ORDER BY `movie`.`mid` ASC LIMIT 1
* 获得最后一个

			Movie.objects.last()

			SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`, `movie`.`mlink` FROM `movie` ORDER BY `movie`.`mid` DESC LIMIT 1
* 获得记录的总数

			Movie.objects.count()

			SELECT COUNT(*) AS `__count` FROM `movie`
* 获得多个
* 获得所有的记录

			Movie.objects.all()
			# 在diango的1.11.6中默认只是获得21个，
			# 懒加载
			SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`, `movie`.`mlink` FROM `movie` LIMIT 21
* 切片 （不支持负数索引）
		Movie.objects.all()[20:40]
		# 底层直接使用了limit字句，可以自动的分页
		# django的orm性能非常强大，能节省很多工作		
		SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`, `movie`.`mlink` FROM `movie` LIMIT 20 OFFSET 20
* 过滤
		#  集和（游标，结果集，查询集）
		Movie.objects.filter(mname='麻辣学院')

		SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`, `movie`.`mlink` FROM `movie` WHERE `movie`.`mname` = '麻辣学院' LIMIT 21
	
* 模糊查询 
	* like
	* %(多个字符) _(一个字符)
	* SELECT * from movie WHERE mname LIKE '%爱情_'
* 查询爱情结尾的
			
			
			
			SELECT * from movie WHERE mname LIKE '%爱情'

			Movie.objects.filter(mname__endswith='爱情')

			SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`, `movie`.`mlink` FROM `movie` WHERE `movie`.`mname` LIKE BINARY '%爱情' LIMIT 21
* 查询爱情开头的

			SELECT * from movie WHERE mname LIKE '爱情%'
			
			Movie.objects.filter(mname__startswith='爱情')
			
			SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`, `movie`.`mlink` FROM `movie` WHERE `movie`.`mname` LIKE BINARY '爱情%' LIMIT 21
* 包含爱情的

		SELECT * from movie where mname like '%爱情%'

		Movie.objects.filter(mname__contains='爱情')


		SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`, `movie`.`mlink` FROM `movie` WHERE `movie`.`mname` LIKE BINARY '%爱情%' LIMIT 21
* 完全相等

			Movie.objects.filter(mname__exact='麻辣学院')
			Movie.objects.filter(mname='麻辣学院')
	
			SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`, `movie`.`mlink` FROM `movie` WHERE `movie`.`mname` = '麻辣学院' LIMIT 21
* 忽略大小写

			mnane__i*** 忽略大小写
* 查询某个字段是否为null

		Movie.objects.filter(mname__isnull=True)

* 多条件查询

		Movie.objects.filter(mname__contains='爱情',mid=147)
		
			and

			
		SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`, `movie`.`mlink` FROM `movie` WHERE (`movie`.`mname` LIKE BINARY '%爱情%' AND `movie`.`mid` = 147) LIMIT 21

		Movie.objects.filter(mname__contains='爱情').filter(mid=147)

* 部分查询

			Movie.objects.values('mname','mid').filter(mname__contains='爱情')
* 排除一部分

			Movie.objects.filter(mname__contains='爱情').exclude(mname__startswith='爱情')

			SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`, `movie`.`mlink` FROM `movie` WHERE (`movie`.`mname` LIKE BINARY '%爱情%' AND NOT (`movie`.`mname` LIKE BINARY '爱情%')) LIMIT 21

* 排序

		Movie.objects.order_by('mid')
	
		SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`, `movie`.`mlink` FROM `movie` ORDER BY `movie`.`mid` ASC LIMIT 21


		Movie.objects.order_by('-mid')

		SELECT `movie`.`mid`, `movie`.`mname`, `movie`.`mdesc`, `movie`.`mimg`, `movie`.`mlink` FROM `movie` ORDER BY `movie`.`mid` DESC LIMIT 21

* 时间
 * 学编程 （搜索问题的能力）  （解决问题的能力） ，业务
 * api，
 * 查询大于某个时间的记录

		Post.objects.filter(created__gt='2017-10-20')

		SELECT `post_post`.`id`, `post_post`.`title`, `post_post`.`created` FROM `post_post` WHERE `post_post`.`created` > '2017-10-20' LIMIT 21
 * 查询最近一个月的帖子（查询最近不活跃的用户）
 
		def get_recent_month_posts():
		    import datetime
		    current = datetime.date.today()-datetime.timedelta(days=30)
		    current = str(current)
		    return Post.objects.filter(created__gt=current)
 * 查询十月二十号--十一月二十号的所有的记录
   * 错误的

			Post.objects.filter(created__in=('2017-10-20','2017-11-20'))
			指的是这两个时间中的一个
  
  * 正确 （sql语句）

		Post.objects.filter(created__range=('2017-10-20','2017-11-20'))

				
		SELECT `post_post`.`id`, `post_post`.`title`, `post_post`.`created` FROM `post_post` WHERE `post_post`.`created` BETWEEN '2017-10-20' AND '2017-11-20' LIMIT 21



* ascill 码总共有 127个  最多可以表示256  2^8     就用一个字节表示

* 添加
	* 方法1
	
				post = Post(title='博客11',created='2017-2-28')
				# post.id none
				post.save()
				# post.id 有了（多表插入的）
   * 方法2

				Post.objects.create(title='博客12',created='2018-11-11')
				返回值是添加的对象
				
* 删除
	* 方法1
	
				post.delete()
   * 方法2 (删除所有包含博客名字的帖子)

				Post.objects.filter(title__contains='博客').delete()
* 更新

	* 方法1  （更新的是所有的字段）
			(没有id主键，就添加，有主键就更新)
			
			post = Post.objects.first()
			post.title='更新了'
			post.save()
			 	
			UPDATE `post_post` SET `title` = '更新了', `created` = '2017-11-27' WHERE `post_post`.`id` = 26
  * 方法2（只更新修改的字段），推荐使用

		Post.objects.filter(id=26).update(title='又更新了')


		UPDATE `post_post` SET `title` = '又更新了' WHERE `post_post`.`id` = 26

### 字段 ###
* django所有的数据模型都继承自models.Model
	* CharField  max_length   （输入框）
	* TextField 没有长度限制的字符串  （文本域）
	* DateField 日期
	* DateTimeField 日期+时间
	* BooleanField 真假
	* NullBooleanField Null，真假，
	* Integer 整数
	* PositiveIntegerField 正整数
	* DecimalField  max_digits(几位数) decimal_places（小数点后保留几位）
	* ImageField  图片 依赖于 Pillow(处理图片) upload_to='houtai'  指定文件上传到目录
	* FileField
	* Auto
	* ForeignKey 1:n
	* ManyToManyField n:n
	* EmailField 邮箱
	* UUIDField 重复的概率非常低基本可以忽略,全世界都不一样的标示，uuid的产生和服务器的环境有关(CPU,网关，) 唯一性的标示，用户模块，订单号
* 不同的字段在后台对应不同的html的组件
### 属性(数据库层，(form)表单的) ###
* unique 标示这个字段唯一
* default  默认的意思 ，（如果不写的话就使用默认的值） 
* null=True 允许字段为null，（允许数据库为null）数据库层面的
* blank=True 表单阶段的，admin后台的
* auto_now 针对时间的，自动调整当前，（当修改条目的时候，这个时间会自动更新），每次修改都会更新  （修改，保存的时候才会生效，）
* auto_now_add 针对时间的，只添加一次，（创建的时间）


### 重点理解属性 ###
* 表单层面的东西（js判断字段是否是"    "），不需要重写迁移数据库
* unique=True  可以任何字段
* default 数据库+表单层面（同时生效）
* auto_now_add 创建的时候会自动的添加时间(数据库),(后台表单层面找不到这个字段)
* auto_now 每次修改的时候自动更新,（数据库+表单层面）
* null=True 数据库层面，允许为Null，不是空字符串
* blank=True 表单层面的，后台选填
*  EmailField 表单层面的

### uuid ###

			uuid.uuid4().get_hex()
### django迁移失败如何处理 ###
* 删除模块下（问题模块）的所有的迁移记录
* 删除数据库中django_migrations中问题模块的迁移记录

			DELETE FROM django_migrations WHERE app='模块名'
* 删除这个模块下已经产生的所有的表
* 重新生成迁移文件
* 重新迁移
### 关系(博客) ###
* 帖子，类别，标签
* 1:1 (1张表)


	![](https://i.imgur.com/DKtJqx6.png)


	![](https://i.imgur.com/qJNixuy.png)

		
		* 咱们自己理解的1：1 ，分表上 （有些特殊的字段经常修改）


				class Post(models.Model):
				    id=models.AutoField(auto_created=True,primary_key=True)
				    title = models.CharField(max_length=20,unique=True)
				# 这个表的id即是这张表的主键也是Post表的外键
				class PostContent(models.Model):
				    id = models.ForeignKey(Post,primary_key=True,)
				    content = models.TextField()
  * 系统提供的 OneToOneField(1：1 分表)


				
			class Post(models.Model):
			    title = models.CharField(max_length=20,unique=True)
			
			class PostContent(models.Model):
				# 底层 这张表的外键持有Post表的主键
			    content = models.TextField()
			    post = models.OneToOneField(Post)
					
 * 正向查询（通过Post获得PostContent）

				post.postcontent
 * 逆向查询（通过PostContent获得Post）
 
				postcontent.post
 * 删除的时候：
 		级联删除
		（如果一张表中字段在其他表中作为外键，那么删除这张表的记录的时候会级联将其他表的记录删除）	

		
* 1：n (2张表)

				
			class Category(models.Model):
			    name = models.CharField(max_length=20,unique=True)
			
			class Post(models.Model):
			    title = models.CharField(max_length=20,unique=True)
			    content = models.TextField()
			    created = models.DateField(auto_now_add=True)
			    modified = models.DateTimeField(auto_now=True)
			    category = models.ForeignKey(Category)

 * 添加（先判断有没有类别，没有类别添加类别，）

				def save_post(title,content,category):
				
					    try:
							# 获得不了会报异常
					        cate = Category.objects.get(name=category)
					    except Category.DoesNotExist :
					        cate = Category(name=category)
					        cate.save()
					    post = Post(title=title,content=content,category=cate)     
					    post.save()
* 修改 （单独修改自己的即可）

* 查询
	
	× 正向查询（通过类别获得类别的帖子）

			
					cate = Category.objects.first()
					cate.post_set.all()
   * 逆向查询（通过帖子获得类别）

				
					post = Post.objects.first()
					post.category
* 删除

		
		级联删除
		（如果一张表中字段在其他表中作为外键，那么删除这张表的记录的时候会级联将其他表的记录删除）

* n:n （底层3张表）（帖子---》标签）

					

			
			class Tag(models.Model):
				    name = models.CharField(max_length=20,unique=True)
			
			class Post(models.Model):
			    title = models.CharField(max_length=20,unique=True)
			    content = models.TextField()
			    created = models.DateField(auto_now_add=True)
			    modified = models.DateTimeField(auto_now=True)
			    # category = models.ForeignKey(Category)
			    tags = models.ManyToManyField(Tag)
* 如何添加？
	
		因为是多对多，会产生第三章表，（post_id和tag_id都是外键）必须得有tag，和post才能存对象

		先创建tag（tag_id）,之后有post（post_id），在之后post.add(tag1,tag2) 完成多对多


			def save_post(title,content,*tags):
				# 获取Tag标签的对象
			    raw_tags=[]
			    for tag in tags:
			        try:
			            raw_tag = Tag.objects.get(name=tag)
			        except Tag.DoesNotExist:
			            raw_tag = Tag.objects.create(name=tag)
			        raw_tags.append(raw_tag)
				# 创建帖子对象存到数据库
			    post = Post.objects.create(title=title,content=content)
				# 把	标签和帖子关联（解压）
			    post.tags.add(*raw_tags)
 * 更新（自己改自己）
 * 查找
 	* 正向查找（标签找帖子） 

			tag = Tag.objects.first()
			tag.post_set.all()
   * 逆向查找(帖子找标签)

			post = Post.objects.first()
			post.tags.all()
 * 删除 
	
		级联删除
		
### (可迭代都可以)参数解压 ###
* 为什么引入参数解压？
* 求和
* 求多个参数的和
			# 传入元组或者列表
			def add(arr):
			    sum=0
			    for n in arr:
			        sum+=n
			    return sum
* 可变参数 *num 可以接受0个或者多个参数

			def add(*num):

				 sum=0
			    for n in num:
			        sum+=n
			    return sum
* python2中只能在方法参数中使用*

			
			def add(*num):

				 sum=0
			    for n in num:
			        sum+=n
			    return sum
			add(1,2,3)
		
			nums = range(1,101)
			add(nums) 错了 num=([1,2..,100],)  int+=list

			
			add(*nums) 自动解压了
		
### 可变参数 ###
* 我定义一个方法的时候，不知道传几个参数
* 可变参数

			def 方法名(var1,var2,*agrs):
				pass

			可变参数
			def add(num1,num2,*nums):
			    sum = num1+num2
			    for n in nums:
			        sum+=n
			    return sum
			# 关键字 **代表的是字典
			def show(name,sex,*agrs,**kwargs):
    			pass
* 如果只写变量，给*agrs,如果k=v 给 kwargs
			




### 参数解压 ###
* 以字典和元组举例（只要是可迭代的都可以解压），如果解压元组*,解压字典**

			def show(name,age,birthday):
   				 print name,age,birthday
 * 数据是从数据库查询来的

			('张安',10,'asdsad')

			show(*d)
 * 列表

			user={'name':'张三','age':10,'birthday':'asdsad'}

			show(**user)

			

### 结合 ###


			def show1(name,age,birthday):
   				 print name,age,birthday
    		def show(name,age,*args):
    			show1(name,age,*args)

			show('张三',10,'2017-11-11')		

			def show(name,age,*args,**kwargs):
    			show1(name,age,*args,**kwargs)

			show('zhangsan',10,birthday='2017-11-11')

### 关系练习 ###
* django的orm，双向查找很X，双向绑定

### Manager ###
* objects (Manager)
* Manager是所有的数据模型（models.Model）的类都会有的，而且这个东西至少有一个
* 自定义管理器（重写父类不够牛x的方法）
* 帖子

			
		class Post(models.Model):
		    title = models.CharField(max_length=20)
		    is_delete = models.BooleanField(default=False)
* 获得没有删除的帖子

		def get_posts():
			
			return Post.objects.filter(is_delete=False).all()


		class PostMananger(Manager):
		    def all(self):
		        print '冲洗了'
				# bug  filter
		        return Manager.all(self).filter(is_delete=False)

		class Post(models.Model):
		    title = models.CharField(max_length=20)
		    is_delete = models.BooleanField(default=False)
		    objects = PostMananger() # 自己定义了一个管理器
		    def __unicode__(self):
		        return u'%s'%self.title

		# 最完善的版本


			class PostMananger(Manager):
			    # def all(self):
			    #     print '冲洗了'
			    #     return Manager.all(self).filter(is_delete=False)
			    def get_queryset(self):
			        return Manager.get_queryset(self).filter(is_delete=False)
			class Post(models.Model):
			    title = models.CharField(max_length=20)
			    is_delete = models.BooleanField(default=False)
			    objects = PostMananger() # 自己定义了一个管理器
			    def __unicode__(self):
			        return u'%s'%self.title

* 一个数据模型至少有一个管理器（一旦自定义了管理器，系统默认提供的就不会添加了，子类一定比父类牛x）


				class Post(models.Model):
				    title = models.CharField(max_length=20)
				    is_delete = models.BooleanField(default=False)
				    objects = Manager()
				    postmanager = PostMananger() # 自己定义了一个管理器
				    def __unicode__(self):
				        return u'%s'%self.title
* 自定义管理之添加关系


		

				class PostManager(Manager):
				    def create(self,**kwargs):
				        # 修改kwargs中类型
				        category_name=kwargs.get('category')
				        category = self.__get_cagetory(category_name)
				        kwargs['category']=category
						# 移出参数中的tags
				        tags = kwargs.pop('tags')
				        # 在创建帖子
				        post = Manager.create(self, **kwargs)
						# 帖子和标签关联起来
				        post.tags.add(*self.__get_tags(tags))
				        return post

				    def __get_tags(self, tags):
				        raw_tags = []
				        for tag in tags:
				            try:
				                raw_tag = Tag.objects.get(name=tag)
				            except Tag.DoesNotExist:
				                raw_tag = Tag.objects.create(name=tag)
				            raw_tags.append(raw_tag)
				        return raw_tags
				
				    def __get_cagetory(self, category_name):
				        try:
				            cate = Category.objects.get(name=category_name)
				        except Category.DoesNotExist:
				            cate = Category.objects.create(name=category_name)
				        return cate
* 删除

			
		class Post(models.Model):
		    title = models.CharField(max_length=120,unique=True)
		    content = models.TextField()
		    created = models.DateField(auto_now_add=True)
		    modified = models.DateTimeField(auto_now=True)
		    category = models.ForeignKey(Category)
		    tags = models.ManyToManyField(Tag)
		    is_delete = models.BooleanField(default=False)
		    postmanage = PostManager()
		
		    def delete(self, using=None, keep_parents=False):
		        self.is_delete=True
		        self.save()
		

	
		
		class PostManager(Manager):

			    def create(self,**kwargs):
			        # 修改kwargs中类型
			        category_name=kwargs.get('category')
			        category = self.__get_cagetory(category_name)
			        kwargs['category']=category
			        tags = kwargs.pop('tags')
			        # 修改kwagrs中类型
			        post = Manager.create(self, **kwargs)
			        post.tags.add(*self.__get_tags(tags))
			        return post
			
			    def __get_tags(self, tags):
			        raw_tags = []
			        for tag in tags:
			            try:
			                raw_tag = Tag.objects.get(name=tag)
			            except Tag.DoesNotExist:
			                raw_tag = Tag.objects.create(name=tag)
			            raw_tags.append(raw_tag)
			        return raw_tags
			
			    def __get_cagetory(self, category_name):
			        try:
			            cate = Category.objects.get(name=category_name)
			        except Category.DoesNotExist:
			            cate = Category.objects.create(name=category_name)
			        return cate
			    def get_queryset(self):
			        raw_queryset = Manager.get_queryset(self).filter(is_delete=False)
			        def delete1(queryset):
			            for post in queryset:
			                post.is_delete=True
			                post.save()
			        import new
			        raw_queryset.delete=new.instancemethod(delete1,raw_queryset,QuerySet)
			        print 'queryser----id---<%d'%id(raw_queryset)
			        return raw_queryset
			
			    # 克隆了一个
			    def filter(self,**kwargs):
			        raw_queryset = Manager.filter(self,**kwargs)
			        def delete1(queryset):
			            for post in queryset:
			                post.is_delete=True
			                post.save()
			        import new
			        raw_queryset.delete=new.instancemethod(delete1,raw_queryset,QuerySet)
			        return raw_queryset

* 排序
 * 常用的排序规则定义到Manager中


			   def get_queryset(self):
			        raw_queryset = Manager.get_queryset(self).filter(is_delete=False).order_by('-created')
				return raw_query
			
			
* Manager 和具体的数据模型先关，


### 反射 ###
* 设置

			class User():
				pass


 * 类设置属性	
 				
			User.datas=['asd']
	
 * 类静态设置方法
 
			@staticmethod
			def showstatic():
				print 'static'
			User.showstatic=showstatic
					

 * 类设置类方法

			@classmethod
			def showsclass(cls):
				print 'class'
			User.showsclass=showsclass
			

			

 * 类设置成员方法（所有的对象都会有这个成员方法）


			def showself(self):
				print 'showself-----',self
		
			User.showself=showself
			#	所有的对象都有showself方法
 * 给对象设置属性  
			u = User()
			u.age=10 只有这个对象有这个属性


 * 给对象设置特有方法
 
			import types

			def showu(self):
				print '只有这个对象有这个方法',self

			u.showu=types.MethodType(showu,u)

* getattr(获得一个对象的属性),hasattr(判断有没有这个属性),setattr(给对象设置一个属性),delattr(删除一个对象的属性)



			class Index_Handler():
				def get(self):
						print '处理了get请求'
				def post(self):
						print '处理了post请求'

			def handle_request(method):
				handler = Index_Handler()
				if hasattr(handler,method):
					getattr(handler,method)()
				else:
					print '405请求方式没有实现'
		
			handle_request('get')
			
			handle_request('post')
			
			handle_request('delete')


         
			index = Index_Handler()
			setattr(index,'name','asdsadsadasd')
		
			delattr(index,'name')

### 聚合函数 ###
* COUNT,MAX,MIN,AVG,SUM
* 统计求和

				SELECT count('*') from post_post
	
				from django.db.models import Count
									
				Post.objects.aggregate(a=Count('*'))
				SELECT COUNT(*) AS "a" FROM "post_post"
* Max

				SELECT max(id) from post_post # 获得帖子的最大id
				
				from django.db.models import Max
				Post.objects.aggregate(maxid=Max('id'))
				
				SELECT MAX(id) AS 'maxid' FROM 'post_post'
* Min

				SELECT min(id) from post_post # 获得帖子的最小id
				
				from django.db.models import Min
				Post.objects.aggregate(minid=Min('id'))
				
				SELECT MIN(id) AS 'minid' FROM 'post_post'
* Avg     

				SELECT avg(id) from post_post
				from django.db.models import Avg
				Post.objects.aggregate(avg=Avg('id'))
* Sum

				SELECT sum(id) from post_post
		
				from django.db.models import Sum
				Post.objects.aggregate(sum=Sum('id'))	
### 分组聚合 ###
* 统计同一时间下有多少条记录，（归档）

				SELECT count('*') ,created from post_post GROUP BY created

				
				Post.objects.values('created').annotate(count=Count('*'))
				
				SELECT "post_post"."created", COUNT(*) AS "count" FROM "post_post" GROUP BY "post_post"."created" LIMIT 21

			
				
* 统计一个时间范围内有多少条记录
	
				SELECT count('*') FROM  post_post WHERE created BETWEEN '2017-10-20' and '2017-12-20'


				SELECT "post_post"."created", COUNT(*) AS "count" FROM "post_post" GROUP BY "post_post"."created" LIMIT 21

	
				SELECT COUNT(*) AS "count" FROM "post_post" WHERE "post_post"."created" BETWEEN '2017-10-20' AND '2017-12-20'
* 类别下有多少条帖子 			
				

				SELECT count(*) ,post_category.name from post_post,post_category WHERE post_post.category_id==post_category.id GROUP BY post_post.category_id
				# __ 魔法下划线 category__name Category里面的name
				Post.objects.values('category__name').annotate(count=Count('*'))

				SELECT "post_category"."name", COUNT(*) AS "count" FROM "post_post" INNER JOIN "post_category" ON ("post_post"."category_id" = "post_category"."id") GROUP BY "post_category"."name" LIMIT 21

### 根据类别id查找类别下的帖子 ###
* 关联查询
		# 每个数据模型都会自带外键的id
		Post.objects.filter(category_id=2)
		Post.objects.first().category.id  性能不好 查询2次数据库（先查出帖子，在查category）
		Post.objects.first().caetory_id 性能好  直接查询
* 魔法下划线 （关联查询）


		Post.objects.filter(category__name__startswith='a')

### 原生查询 ###
* 任何管理器对象都都有一个raw

			 # Raw query must include the primary key
			 Post.objects.raw('select * from post_post')
* connection （当前数据库的连接对象）

			from django.db import connection
			cursor = connection.cursor()
			datas = cursor.execute('SELECT count(*) ,post_category.name from post_post,post_category WHERE post_post.category_id==post_category.id GROUP BY post_post.category_id')
			cursor.close()


			for data in datas:
				print data

			
			(4, u'asdads')
			(1, u'asdads1')
* conection.queries 保存了这个执行的所有的sql语句
 * 获得最新执行的sql语句
 


				def showsql():
					from django.db import connection

					queries = connection.queries
	 				print queries[-1]['sql']


### Q查询 ###
* orm的filer(id=5).filter(created='2017-12-15') and
* Q查询支持and，or,~(与或非)
* 我想查询id=3的帖子或者created='2017-12-15'				
			
	
				from django.db.models import Q
				Post.objects.filter(Q(id=5)|Q(created='2017-12-15')) or
				Post.objects.filter(Q(id=5)&Q(created='2017-12-15'))  and
				Post.objects.filter(~Q(id=5))  not

### F查询 ###
* （获得当前字段的值）
* 给所有帖子的创建时间，都+1天


			from django.db.models import F
			import datetime
			Post.objects.all().update(created=F('created')+datetime.timedelta(days=1))

* 查询是热门的记录（100个人看了商品60个买了，100看了帖子50个评论了）
 * 查询热门帖子 100看了帖子50个评论了,而且阅读的数量必须大于30

		
		Post.objects.filter(read_count__lte=F('remark_count')*2).filter(read_count__gt=30)


		Post.objects.filter(Q(read_count__lte=F('remark_count')*2)&Q(read_count__gt=30))


### 装饰器 ###
* 当修改方法的时候，（开闭原则，对扩展打开，对修改关闭）
* 装饰器
		
			def wrapper(fun):
			    def __wrapper(*args,**kwargs):
			        content = fun(*args,**kwargs)
			        return '<b>%s</b>'%content
			    return __wrapper
				
			@wrapper
			def get_text():
				return 'hello world'

		def warpper(blod=True):
		    def _wrapper(fun):
		        def __wrapper(*args,**kwargs):
		            content=fun(*args,**kwargs)
		            if blod:
		                content = '<b>%s</b>'%content
		            return content
		        return __wrapper
		    return _wrapper
		@wrapper()
		def get_text():
				return 'hello world'
		get_text()


### 事务(原子型，操作是不可分割的，要么成功要么失败) ###
* 应用场景（多表操作数据库（修改））
* 事务的伪代码

				@atomic
				def add():
				    print '添加第一张表的数据'
				    print '添加第二章表的数据'
				
				def atomic(fun):
					def __wrapper(*args,**kwargs):
						print '开启事务'
						try:
							content = fun(*args,**kwargs)
							print '提交事务'
							return content
						except:
							print '事务回滚'	
					return __wrapper
* atomic

		
				# Post 实体类的save方法	
				  from django.db.transaction import  atomic
				    @atomic
				    def save(self, force_insert=False, force_update=False, using=None,
				             update_fields=None):
						# 保存category的对象
				        try:
				            cate = Category.objects.get(name=self.category.name)
				        except Category.DoesNotExist:
				            cate = Category.objects.create(name=self.category.name)
				        self.category=cate
				        1/0
						# 调用super方法，将post对象存到数据库
				        return models.Model.save(self, force_insert=False, force_update=False, using=None,
				             update_fields=None)

						
		
				    def save(self, force_insert=False, force_update=False, using=None,
				             update_fields=None):
						# 保存category的对象
				        try:
				            cate = Category.objects.get(name=self.category.name)
				        except Category.DoesNotExist:
				            cate = Category.objects.create(name=self.category.name)
				        self.category=cate
						#上面的类别就存到数据库了
				        1/0
						# 调用super方法，将post对象存到数据库
				        return models.Model.save(self, force_insert=False, force_update=False, using=None,
				             update_fields=None)
							

### QuerySet（查询集） ###
* django.db.models.query  QuerySet
* 惰性查询 （延迟查询）
 *  查询的数据有限

			Post.objects.all()
			
		
			SELECT "post_post"."id", "post_post"."title", "post_post"."created", "post_post"."category_id", "post_post"."read_count", "post_post"."remark_count" FROM "post_post" LIMIT 21 #在1.11.6里面 获得21
 * 延迟查询数据
 
			posts = Post.objects.all() # 没有真正的查询数据
		
			什么时候调用什么时候查询
			print posts
			# 才查询
* 缓存（查询的对象）
 * 缓存的条件 （一旦遍历了之后数据就会被缓存）


			posts = Post.objects.all() （QuerySet延迟加载）
			# 惰性查询
			print posts 查询数据库，没有缓存
			# 查询数据库，并且将数据缓存到QuerySet
			for p in posts:
				print p
			print posts 没有查询数据库，使用的queryset里面的缓存


			posts = Post.objects.all()[1:3] # 惰性查询没有执行
			print posts 查询数据库，没有缓存
			# 查询数据库，并且将数据缓存到QuerySet
			for p in posts:
				print p
			
			print posts 没有查询数据库，使用的queryset里面的缓存


			
			post = Post.objects.first() # (Post的对象,不是QuerySet，所以没有延迟加载这一说)，所以查了
			print post  #和QuerySet没有关系
			
			first(),last(),get()都是直接的对象，而不是QuerySet，所以既没有延迟加载和缓存这一说

			
			# 因为切片的索引不一致
			posts1 = Post.objects.all()[1:] # 惰性查询
			# 查询数据库 缓存数据
			for p in posts1:
				print p
		
			
			posts2 = Post.objects.all()[2:] # 惰性查询

			print posts2 #	查询数据库，没有从posts1中获取数据



			# a用户访问了页面的数据
			posts1 = Post.objects.all()[1:] # 惰性查询
			# 查询数据库 缓存数据
			for p in posts1:
				print p
		
			# b用户访问和a访问了同一个界面
			posts2 = Post.objects.all()[1:] # 惰性查询
			
			print posts2 #	查询数据库，没有从posts1中获取数据


			id(posts1) != id(posts2) 不是同一个查询集合  

			# 这缓存有什么用？必须得同一个查询集合才能用
			# 性能低	
			def index_view(request):
			    posts = Post.objects.all()
			    print  type(posts),id(posts)
			    return render(request,'index.html',{'posts':posts})
			# 将posts提成全局的
			 posts = Post.objects.all() 默认存到内存
			def index_view(request):
			    print  type(posts),id(posts)
			    return render(request,'index.html',{'posts':posts})


			

			posts[1:2] # 缓存的里面的

			posts = Post.objects.all() #惰性查询
			# 读取数据库 并且缓存数据
			for p in posts:
				print p
			print posts #使用缓存
			print posts[1:] # 还是使用缓存

----------
			 posts = Post.objects.all() 默认存到内存LocMemCache，之后配置redis即可
			CACHES = {
			    'default': {
			        'BACKEND': 'django.core.cache.backends.locmem.LocMemCache',
			    }
			}

### 扩展模型功能 ###
* MVT 架构 
 * 模板的业务 （显示隐藏判断循环）
 * V业务整个大的业务 
 * Model 也存在业务（model自己的业务）
 * 什么业务应该放到什么里面（注册登录举例）

* model层代码



			#coding=utf-8
			from django.db import models
			class UserNotFoundException(Exception):
			    def __init__(self, *args, **kwargs):  # real signature unknown
			        Exception.__init__(self,*args,**kwargs)
			
			class User(models.Model):
			    account = models.CharField(max_length=20)
			    password = models.CharField(max_length=20)
			    @staticmethod
			    def login(account ,password):
			        # 验证加密md5，加上时间戳毫秒 （md5加密，加盐）
			        try:
			            user = User.objects.get(account=account,password=password)
			            return user
			        except User.DoesNotExist:
			            # 异常转换
			            raise UserNotFoundException('当前用户sadsad不存在')
* view层代码


				def login(request):
				    account = request.POST.get('account')
				    password = request.POST.get('password')
				    try:
				        user = User.login(account,password)
				        return HttpResponse(u'欢迎:')
				    except UserNotFoundException as e:
				        return HttpResponse('%s'%e)






			

			
	

			
			



		
