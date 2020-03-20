# 一、模型表单ModelForm
### 1、class Meta （元类）
> 定义模型与表单的关联性
- fields，列表，表单字段。'__all__'所有字段
- labels，字典，标签字段，与fields一一对应
- model，模型，关联模型
- widgets控件，字典，显示逻辑控制
- 

### 1、 **ModelForm**：save(commit=false),
> 生成一个模型实例，不会立即保存进数据库。commit默认为True

```
# post提交的数据，对数据进行处理
        form = TopicForm(request.POST)
        if form.is_valid():
            new_topic = form.save(commit=False)
            new_topic.owner = request.user
            new_topic.save()
            return HttpResponseRedirect(reverse('learning_logs:topics'))
```