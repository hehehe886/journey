## model 和ORM 介绍


model 层 和数据库打交道

## 创建数据库
1.
create database 数据库名 fefault charset utf8;
数据库名和项目名保持一致

2.
settings.py 配置，
DATABASES 配置项有默认的sqlite3 变成mysql
NAME：
USER
PASSWORD
HOST/PORT


## model 模型
模型层==》模型类
一个模型类代表数据库中的一张数据表
模型类中每一个类属性都代表数据库的一个字段
模型是数据交互的接口，是表示和操作数据库的方法和方式

## ORM 框架
ORM object relational mapping 对象关系映射
通过类和对象取代sql语句去操作数据库

- 作用：
1. 建立模型类和表之间的对应关系，允许通过面向对象的方式来操作数据库

2.根据设计的模型类生成数据库中的表格

3.通过简单的配置就可以进行数据库的切换

ORM 
类 对象 属性

对应

DB
表 行 字段

## migrage
migrate 指对模型所做更改，添加字段，删除模型等操作同步到数据库

步骤一：
生成迁移文件 python3 manage.oy makaemigrations
保存在migrations目录下

步骤二：
python3 manage.py migrate
将每个应用下的migrations目录中的文件同步回数据库

## 创建模型类流程
1.创建应用
2.在应用下的models.py 中编写模型类
```
from django.db import models
class 模型类名(models。Model):
    字段名 =models.字段类型(字段选项))
```
3.迁移同步到数据库 makemigrations & migrate

## 常用字段类型
模型类字段类型==》数据库字段类型

常用Field()：

BooleanField() 布尔值 ==》tinyint(1)
编程语言True/False ==》数据库 1或0

CharField()  ==》 varchar
模型类中属性必须指定`max_length`参数值

DateField() ==> date
参数：
auto_now: 每次保存对象时，自动设置该字段当前时间（True/False） 
auto_now_add 第一次创建时自动设置当前时间（True/False） 
default:设置当前时间

DateTimeField() ==》 datetime(6)
比DateField 时间更精确

FloatField() ==> double

DecimalField() ==> decimal(x,y)
金钱相关，带小数
必选参数：max_digits:位数总数
         decimal_places 小数点后的数字数量


EmailField() ==> varchar 

IntegerField() ==>int

ImageField() ==> varchar(100)
图片路径

TextField() ==> longtext
不定长的字符数据


### 字段选项
primary_key 
不设置，默认新增id字段为主键
True ==》这是该列未主键

blank
True：字段可设置未空
False：字段必须填写

null （不建议使用
True：表示该列值允许为空
Flase:建议default来设置默认值

default
设置所在列的默认值

db_index 
True 为该列增加索引

unique
True 表示该字段在数据库中的必须唯一

db_column 
指定字段名称，默认将采用属性名作为列名

verbose_name
admin界面上的显示名称

ex:

name= models.charField(max_length=30,unique=True,null=False,db_index=True)

字段选项：最长长度30，不唯一，不能空，创建索引

## 模型类 meta类
使用内部meta类，对模型类做控制 对应数据库就是对表在做变更修改
```
from django.db import models 
class Book(models.Model):
    title=models.CharField(max_length=50)
    price=models.DecimalField(max_digits=7,decimal_places=2)
    clasee Meta:
        db_table = 'book'
```

将表名从：应用名_Book 修改为 book

## migrate 数据库混乱
1.删除migrations 所有000?_XXXX.py 文件
2.删除数据库 drop database name;
3.重新创建 create database name default charset utf8;
4.python3 manage.py makemigrations
5.python3 manage.py migrate

## orm CRUD 增删改查操作
CRUD 值在做计算处理中 create read Update Delete

ORM CRUD 核心 ==》 模型类.`管理器对象`

```
class mymodel(models.Model)
    ...
mymodel.objects.creste(...)
```
继承models.Model的模型类，都会有一个objects对象被同样继承下来，叫管理器对象

增删改查通过此objects实现

### 创建数据
方式一：


mymodel.objects.create(属性=值，属性2=值2,..)
成功返回创建好的实体对象
失败则抛出异常

方式二：
面向对象
创建mymodel实例对象，调用save()保存
```
obj=mymodel(属性=值，属性2=值2)
obj.属性=值
obj.save()
```
### django shell
django shell 交互模式下做响应sehll 操作

可以代替编写view 的代码来进行直接操作
`项目代码变化，需要重新django shell`

> python3 manage.py shell




## ORM 查询操作

all() 查询全部记录，返回queryset查询对象
get() 查询符合条件的单一记录
filter() 查询符合条件的多条记录
exclude() 查询符合条件之外的全部记录


- all()
mymodel.objects.all() ==> select * from table

返回QuerySet 容器对象，内部存放mymodel 实例
```
from bookstore.models import Book
books = Book.objects.all()
for book in books:
    print("书名"，book.title,'出版社',book.pub)
```

> 自定义QuerySet 输出格式
```
def __str__(self):
    return '%s_%s_%s_%s'%(self.title,self.price,self.pub,self.market_price)

```

- valuse('列1','列2')
mymodel.objects.valuse('列1','列2') ==> select 列1，列2 from table

返回QuerySet 容器对象，返回字典
{列1':值1,'列2':值2}
```
from bookstore.models import Book
a = Book.objects.valuse('title','pub')
for book in a:
    print(book['titile'],book['pub'])
```

- valuse_list('列1','列2')

mymodel.objects.valuse_list(...)  ==> select 列1，列2 from table

返回QuerySet 容器对象，返回元组
```
from bookstore.models import Book
a = Book.objects.valuse_list('title','pub')
for book in a:
    print(book[0],book[1])
```

- order_by()
mymodel.objects.order_by('-列','列')
使用sql 中的order by语句对查询结果根据某个字段选择性的进行排序
默认升序，降序在列前增加`-`

```
from bookstore.models import Book
a = Book.objects.order_by('-price')
for book in a:
    print(book[0],book[1])
a2 = Book.objects.valuse('title').order_by('-price')

```
>print(a2.query)
>将返回sql 原生语句

## 条件查询
- filter(条件)
mymodel.objects.filter(属性=值，属性2=值2)
返回包含此条件的全部数据集 
and 查询

```
from bookstore.models import Book
books = book.objects.filter(pub='清华大学出版社')
for book in books:
    print("书名",book.title)

```

- exclude(条件)
与filter 相反

- get(条件)
mymodel.objects.get(条件)
返回满足条件的唯一一条数据，（数据库id字段 唯一）
如果查询数据不唯一则 model.multipleobjectsreturned异常
如果没有数据则model.doesnotexist 异常

## 灵活条件查询
- __exact: 等值配
```
author.objects.filter(id__exact=1)
等同与sql语句：
select * from author where id = 1;
```

- __contains 包含指定值
```
author.objects.filter(name__contains='w')
等同与sql语句：
select * from author where name like "%w%";
```
- __startswith 以什么开始
>select * from author where name like "w%";
- __endwith 以什么结束
select * from author where name like "%w";

- __gt >
```
author.objects.filter(age__gt='50')
等同与sql语句：
select * from author where age  > 50;
```
- __gte >=
- __lt  <
- __ite <=
- __in 查找数据是否在指定范围内

```
author.objects.filter(country__in =['jp','cn','us'])
等同与sql语句：
select * from author where country in ('jp','cn','us');
```

- __range 查找数据是否在指定的区间范围内
```
author.objects.filter(age__range=(30.50))
等同与sql语句：
select * from author where age  BETWEEN 35 and 50;
```

## 更新数据
修改数据
### 单个修改
1. 查询
通过get()得到要修改的实体对象

2. 改
通过对象.属性的方式修改
obj=
3. 保存
通过对象.save()保存数据
ojb.save()

```
from bookstore.models import Book
b = book.objects.get(id=1)
b.price=22
b.save()
```

### 批量修改数据
直接调用queryset的update(属性=值) 实现批量修改
```
from bookstore.models import Book
b = book.objects.filter(id__gt=3)
or
b2 = book.objects.all()
b2.update(market_price=100)
```


## 删除操作
### 单个数据删除
1. 查
get()
2. deletc()

```
from bookstore.models import Book
try:
    b = book.objects.get(id=1)  
    b.deletc()
except:
    print("删除失败")
```
### 批量删除
1. 查找查询结果集中满足条件的全部queryset查询集合对象
2. 调用查询集合对象的delete方法实现删除

```
from bookstore.models import Book
try:
    b = book.objects.filter(id__gt=5)
    b.deletc()
except:
    print("删除失败")
```

### 伪删除
实际中，通常不会轻易在业务里把数据真正删除掉，而是伪删除
伪删除: 增加字段is_avlinle(或avrive view) 布尔值字段，删除时将字段设置成false

使用伪删除，确保显示数据的查询，增加了is_active=True的过滤查询

## ORM F对象 Q对象
### F对象
F对象代表数据库中某条记录的字段信息
作用:
- 通常对数据库的字段在`不获取的情况下`进行操作
- 用于类数据（字段）之间的比较

```
from django.db.models import F
F('列名')

```

具体实例一：
```
更新book实例中所有的零售价涨10元
old做法:
from django.db.models import book
books=book.objects.all()
for book in books:
    book.market_price=book.market_price+10
    book.save()

F对象做法：
from django.db.models import book
from django.db.models import F
book.object.all().update(market_price=F('market_price')+10)

sql 语句：
UPDATE `bookstore_book` SET `market_price` =(`bookstore_book`.`market_price` + 10 )
```


具体实例二：
```
对数据库中两个字段的值进行比较，列出那些书的零售价高于定价？
old做法：
from django.db.models import book
for book in books:
books=book.objects.all()
    print(book.title,'price',book.price,'market_price',book.market_price)

F对象做法：
from django.db.models import book
from django.db.models import F
books=book.objects.filter(market_price__gt=F('price'))
sql 语句：
SELECT * FROM 'bookstore_book' WHERE 'bookstore_book'.'market_price' > ('bookstroe_book'.'price')


> 具体实例一：F对象做法优在搞并发请求数量更效率
> 具体实例二：F对象做字段间的比较查询更效率，字段一 比较 字段二
```
### Q对象
在当前获取查询结果集 
使用 
| 逻辑或 
~ 逻辑非
& 逻辑和

&~ 逻辑与非 （条件1成立，条件2不成立）

> from django.db.models import Q


```
实例一
找出定价<20 或 清华大学出版社出版的书籍
book.objects.filter(Q(price__lt=20)|Q(pub='清华大学出版社'))

实例二
找出定价小于20，非清华大学出版社出版的书籍
book.objects.filter(Q(price__lt=20)&~Q(pub='清华大学出版社'))

```

## 聚合擦查询和原生数据库操作

### 聚合查询
针对数据库表的一个字段数据进行部分或全部进行统计查询
例如：查询bookstpre_book 数据表中的全部书的平均价格，查询所有书籍数量，需要使用到聚合查询

聚合查询： 整表聚合、分组聚合

#### 整表聚合
导入引用
>from django.db.models import *
>聚合方法： Sum Avg Count Max Min

语法:
mymodel.objects.aggregate(自定义结果变脸名=聚合函数('列'))

返回：{"结果变量名":"值"}

```
实例一：
获取平均价格
from django.db.models import Book Avg
res = book.objects.aggregate(theavg=Avg('price'))
print(res['theavg'])

实例二：
获取所有书籍数量
from django.db.models import Book Count
res=book.objects.aggregate(all=Count('id'))
print(res['all'])
```

#### 分组聚合
通过计算查询结果中的每一个对象所关联的对象集合，从而得出总记值（平均值或总和），即为查询集的每一项生成聚合

语法：
- Queryset.annotate(自定义结果变量名=聚合函数('列'))

返回 Queryset

```

1.先分组 mymodel.objects.values('列1','列2')

pub_set = book.objects.valuse('pub')

# pub_set 为一组queryset

2. 通过返回结果的queryset.annotate()方法聚合得到分组结果
pub_count_set= pub_set.annotate(all=Count('pub'))
print(pub_count_set)

得到各个出版社分别出版了多少本书

```
分组聚合之后，可以having 复杂查询
```
pub_count_set= pub_set.annotate(all=Count('pub')).filter(all__gt=1)

```
### 原生数据库操作

django 直接用sql 语句方式进行通信数据库
有sql 注入风险从而不推荐使用sql原生语句进行查询


方法一：

查询：mymodel.objects.raw(sql语句，拼接参数)
返回 RawQuerySet 集合对象【只支持基础操作，比如循环】

```
books = models.book.objects.raw('select * from bookstore_book')
for book in books:
    print(book)
```

**sql注入**
使用原生语句小心sql注入
例如：
```
搜索好友的表单框输入 1 or 1 = 1
attack = book.objects.raw('select * from bookstore_book wjhere id =%s'%('1 or 1 = 1 '))

查询到所有用户数据
```

sql防注入：
> attack = book.objects.raw('select * from bookstore_book wjhere id =%s',['1 or 1 = 1 '])


#### cursor
使用cursor 增删改查
导入引用:
from django.db import connection 

```
创建cursor类的构造函数创建cursor对象再使用对象
目的：保证异常时候释放cursor资源，通常使用with语句进行上下文管理
from django.db import connection 
with connetion.cursor() as cur:
    cur.excute('sql原生语句'，拼接参数)
```