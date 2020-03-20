# 表单
> django  Form对象可以创建表单和处理表单数据。
让代码更加简洁，errors form处理

> 表单提交成功可以方便的获取表单数据（重定向），数据校验失败可以显示错误信息

## 一、Form对象

#### 1、初始化
initial可以设置表单的初始值；即value值
```
form = TopicForm(initial={'subject': 'I love your site!'})  #设置初始值
form = TopicForm(request.POST) #已赋值
```

#### 2、方法
1. is_valid()
> call the is_valid() method to run validation and return a boolean designating whether the data was valid:
校验字段,并返回校验结果

2. 格式化输出as_table()、as_ul()、as_p()

```
>>> print(f)
<tr><th><label for="id_subject">Subject:</label></th><td><input type="text" name="subject" value="Hello" maxlength="50" required id="id_subject"></
td></tr>
<tr><th><label for="id_e_mail">E mail:</label></th><td><input type="email" name="e_mail" id="id_e_mail"></td></tr>
<tr><th><label for="id_message">Message:</label></th><td><textarea name="message" cols="40" rows="10" required id="id_message">
Nice site!</textarea></td></tr>

>>> print(f.as_p())
<p><label for="id_subject">Subject:</label> <input type="text" name="subject" value="Hello" maxlength="50" required id="id_subject"></p>
<p><label for="id_e_mail">E mail:</label> <input type="email" name="e_mail" id="id_e_mail"></p>
<p><label for="id_message">Message:</label> <textarea name="message" cols="40" rows="10" required id="id_message">
Nice site!</textarea></p>

>>> print(f.as_table())
<tr><th><label for="id_subject">Subject:</label></th><td><input type="text" name="subject" value="Hello" maxlength="50" required id="id_subject"></
td></tr>
<tr><th><label for="id_e_mail">E mail:</label></th><td><input type="email" name="e_mail" id="id_e_mail"></td></tr>
<tr><th><label for="id_message">Message:</label></th><td><textarea name="message" cols="40" rows="10" required id="id_message">
Nice site!</textarea></td></tr>

>>> print(f.as_ul())
<li><label for="id_subject">Subject:</label> <input type="text" name="subject" value="Hello" maxlength="50" required id="id_subject"></li>
<li><label for="id_e_mail">E mail:</label> <input type="email" name="e_mail" id="id_e_mail"></li>
<li><label for="id_message">Message:</label> <textarea name="message" cols="40" rows="10" required id="id_message">
Nice site!</textarea></li>
```

#### 3、属性
1. is_bound
是否绑定对象，判断是否传入表单数据

```
form = TopicForm(request.POST)
# is_bound() 返回true
form = TopicForm()
# is_bound() 返回false
```
2. erros属性，提供一个与字段对应的错误消息**字典表**

```
>>> f = ContactForm({'subject': 'Hello', 'message': ''})
>>> f.errors
{'message': [u'This field is required.']}
```
3.数据清理cleaned_data
> cleaned_data 会返回清理后的数据，如果form字段中使用整数型或日期型，form
框架会确保方法使用合适的Python整数型或 datetime.date 型对

**可选对象（required=false）、错误errors字段、自动清理为空**

### 二、表单字段
> Field 类表现校验逻辑 ，而widget部件表现显示逻辑
#### 1、字段：（类似模型字段）
> **字段类** 管理表单数据并在提交表单时执行验证

```
    subject = forms.CharField(max_length=50, label='主题')
    email = forms.EmailField(required=False, label='邮箱地址')
    message = forms.CharField(widget=forms.Textarea, label='内容')
```

#### 2、字段参数：

1. widget部件
2. label显示标签
3. required=false 可选，默认为false

### 三、自定义检验方式
> Django的form系统自动寻找匹配的函数方法，该方法名称以 clean_ 开头，并以字段名称(name_field)结束。 如果有这样的方法，它将在校验时被调用。

```
class ContactForm(forms.Form):
    subject = forms.CharField(max_length=50, label='主题')
    e_mail = forms.EmailField(required=False, label='邮箱地址')
    message = forms.CharField(widget=forms.Textarea, label='内容')

    def clean_message(self):
        message = self.cleaned_data['subject']
        num_words = len(message.split())
        if num_words < 4:
            raise forms.ValidationError('Not enough words!')
        return message
```

 clean_message()方法将在指定字段的默认校验逻辑之后执行
 
 
 # 补充与表单相关的属性
 
**{{ field.label }}**
> 字段的label，比如 Email address。
{{ field.label_tag }}
> 该字段的label封装在相应的HTML <label> 标签中。它包含表单的 label_suffix 。例如，默认的 label_suffix 是一个冒号：


```
<label for="id_email">Email address:</label>
{{ field.id_for_label }}
```

> 用于该字段的 ID（像上面的例子中的 id_email ）。如果您要手动构建label，您可能要用这个来替换 label_tag 。例如，如果你有一些内嵌的JavaScript并且想要避免硬编码字段的ID，这也很有用。

**{{ field.value }}**
> 字段的值。例如 someone@example.com 。

**{{ field.html_name }}**
> 字段名称：用于其输入元素的name属性中。如果设置了表单前缀，它也会被加进去。

**{{ field.help_text }}**
> 与该字段关联的帮助文本。

**{{ field.errors }}**
> 输出一个 
```
<ul class="errorlist">
```
，其中包含这个字段的所有验证错误信息。可以使用 {% for error in field.errors %} 循环来自定义错误信息的显示。在这种情况下，循环中的每个对象是包含错误信息的字符串。

**{{ field.is_hidden }}**
> 如果是隐藏字段，这个属性为 True ，否则为 False 。它作为模板变量没多大作用，但可用于条件测试，例如：

```
{% if field.is_hidden %}
   {# Do something special #}
{% endif %}
```


**{{ field.field }}**
> 表单类中的 Field 实例由 BoundField 封装。您可以用它来访问 Field 的属性，比如 {{ char_field.field.max_length }}
