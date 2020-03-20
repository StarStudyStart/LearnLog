1. 使用命名组可以让你的URLconfs更加清晰，减少搞混参数次序的潜在BUG
- 如果有任何命名的组，Django会忽略非命名组而直接使用命名组。
- 否则，Django会把所有非命名组以位置参数的形式传递。
在以上的两种情况，Django同时会以关键字参数的方式传递一些额外参数。
2. 传递额外的参数到视图函数中
```
# urls.py
from django.conf.urls.defaults import *
from mysite import views
urlpatterns = patterns('',
(r'^foo/$', views.foobar_view, {'template_name': 'template1.html'}),
(r'^bar/$', views.foobar_view, {'template_name': 'template2.html'}),
)

# views.py
from django.shortcuts import render_to_response
from mysite.models import MyModel
def foobar_view(request, template_name):
    m_list = MyModel.objects.filter(is_new=True)
    return render_to_response(template_name, {'m_list': m_list})
```
3.伪造捕捉到的URLconf值

```
urlpatterns = patterns('',
(r'^mydata/birthday/$', views.my_view, {'month': 'jan', 'day': '06'}),
(r'^mydata/(?P<month>\w{3})/(?P<day>\d\d)/$', views.my_view),
)
```

4.创建通用视图

```
# urls.py
from django.conf.urls.defaults import *
from mysite import models, views
urlpatterns = patterns('',
(r'^events/$', views.object_list, {'model': models.Event}),
(r'^blog/entries/$', views.object_list, {'model': models.BlogEntry}),
)
# views.py
from django.shortcuts import render_to_response
def object_list(request, model):
    obj_list = model.objects.all()
    template_name = 'mysite/%s_list.html' % model.__name__.lower()
    return render_to_response(template_name, {'object_list': obj_list})
```
5.捕捉值和额外参数之间的优先级

==额外URLconf参数的值会被使用==

6.使用缺省视图参数

```
# urls.py
from django.conf.urls.defaults import *
from mysite import views
urlpatterns = patterns('',
(r'^blog/$', views.page),
(r'^blog/page(?P<num>\d+)/$', views.page),
)
# views.py
def page(request, num='1'):
# Output the appropriate page of blog entries, according to num.
# ...
```
7. 从URL中捕获文本(python3的捕获方式中会自动转化)

```
# 无论何种视类型，图函数接受的都是字符串
re_path("(?P<year>[0-9]{1,2})/", views.get_current,name="current_time"),
#控制台输出类型
<class ' str'>

# 视图函数接收的是int型
path("<int:year>/", views.get_current,name="current_time"),
#控制台输出类型
<class 'int'>
```
8. GET与POST逻辑处理分离

```
# views.py
from django.http import Http404, HttpResponseRedirect
from django.shortcuts import render_to_response
def method_splitter(request, GET=None, POST=None):
    if request.method == 'GET' and GET is not None:
        return GET(request)
    elif request.method == 'POST' and POST is not None:
        return POST(request)
    raise Http404
def some_page_get(request):
    assert request.method == 'GET'
    do_something_for_get()
    return render_to_response('page.html')
def some_page_post(request):
    assert request.method == 'POST'
    do_something_for_post()
    return HttpResponseRedirect('/someurl/')
# urls.py
from django.conf.urls.defaults import *
from mysite import views
urlpatterns = patterns('',
    # ...
    (r'^somepage/$', views.method_splitter, {'GET': views.some_page_get, 'POST': views.some_page_p
    # ...
)
```

9.装饰器（减少视图函数中的重复代码）

```
def requires_login(view): # 外层传入函数
    def new_view(request, *args, **kwargs): # 内层传入函数参数
        if not request.user.is_authenticated():
            return HttpResponseRedirect('/accounts/login/')
        return view(request, *args, **kwargs) # 返回原有函数
    return new_view
    
@requires_login
def view(request):
    # do something 
# 相当于
view = requires_login(view)
view(request)
```

10. include（）
>  每当Django遇到  include() 时，它将截断匹配的URL，并把剩余的字符串发往包含的
URLconf作进一步处理

- 一个被包含的URLconf接收任何来自parent URLconfs的被捕获的参数以及额外参数
- 参数总是传递到被包含的URLconf中的每一 行，不管那些行对应的视图是否需要这些参数。 
```
# urls.py
from django.conf.urls.defaults import *
urlpatterns = patterns('',
    (r'^blog/', include('inner'), {'blogid': 3}),
    )
# inner.py
from django.conf.urls.defaults import *
urlpatterns = patterns('',
    (r'^archive/$', 'mysite.views.archive'),
    (r'^about/$', 'mysite.views.about'),
    (r'^rss/$', 'mysite.views.rss'),
)
```

