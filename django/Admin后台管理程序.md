
Admin后台管理程序
> django 自动管理工具是django.contrib的一部分，这是一个django的标准库，包含一系列的库auth、session、comments等重要库

# Admin模块的工作方式
> 后台管理程序拥有自己的模块、模板、视图处理函数view、URLPatterns

1、当服务启动时，Django从`` url.py`` 引导URLconf，调用``admin.autodiscover()``, 遍历INSTALLED_APPS 中配置，并且寻找相关的 ``admin.py`` 文件。 如果在指定的app目录下找到 ``admin.py`` ，它就执行其中的代码。

2、admin.py中每次调用`` admin.site.register()`` 都将那个模块注册到
管理工具中。 管理工具只为那些明确注册了的模块显示一个编辑/修改的界面

## 将Model加入admin中管理

```
admin.site.register(Author, AuthorAdmin)
admin.site.register(Book, BookAdmin)
admin.site.register(Publisher)
```

## 设置字段可选
1. blank=True；空字符提交给数据库
2. null=True；数据库字段可为空值\
**适用于部分数据库字段不接受空字符串，但又想要空值提交,如：整形、日期类型**
> NULL不同于空字符串“”，两者互斥。

## 字段标签（管理界面标签）
``verbose_name``

### 自定义列表
1、``list_display = ('__str__')``
显示哪些字段

```
list_display = ('title', 'publisher', 'publication_date')
```

2、``list_filter = ()``过滤器
```
list_filter = ('publication_date','title')
```

3、``date_hierarchy = None``date对象或映射字符

按日期的层级过滤

```
date_hierarchy = 'publication_date'
```
4、``search_fields = ()``
搜索条件设置

```
search_fields = ('first_name', "last_name") 根据姓名的关键字搜索 
```


### 自定义程序表单
1. ForeignKey

```
raw_in_filed = ('publisher',)
```

2. ManyToMany

```
filter_horizontal = ('author',)
```
