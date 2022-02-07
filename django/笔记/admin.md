## django-admin
admin管理后台，提供了较完善的后台管理数据库的接口，可供开发途中调用和测试使用

django 会搜集所有以注册模型类，
将模型类提供数据管理界面，供开发者使用

### 创建后台管理账号
> python3 manage.py createsuperuser


## 后台管理页面添加model
若要将自己定义的模型类也能在/admin 后台管理界面中显示和管理需要注册到后台

1. 应用app下admin.py 导入需要管理的模块
from .models import book

2. 调用admin.site.register 方法进行注册
admin.site.register(自定义模型类)

### 模型管理器类
注册后，使自己的model 更适合django后代风格管理
>django.contrib.admin   modeladmin

```
1. 再<应用app>/admin.py 定义模型管理器类
class xxxmanager(admin.modelAdmin):
    ...

2. 绑定注册模型管理器和模型类
from django.contrib import admin 
from .models import *
admin.site.register(YYYY,xxxManager) 绑定YYY 模型类 与管理器类XXXmanager
```

ex:
```
from django.contrib import admin
from .models import book

class bookmanager(admin.ModelAdmin)
    list_display=['id','title','price','market_price']

admin.site.register(book,bookmanger)
```


## 关系映射

关系型数据库，通常不会把所有数据放在同一张表，不宜拓展