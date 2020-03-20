## 文档类型声明
<!DOCTPE html>
## 元素
一个html元素，从标签开始到标签结束
## 标签
### 闭合标签
    例：<html><html>
### 自闭合标签
    例：<meta /> <br /> <hr />
    
## head标签（对整个文本文件进行解释标注）
### meta标签 
标签提供了元数据.元数据也不显示在页面上，但会被浏览器解析。META 元素通常用于指定网页的描述，关键词，文件的最后修改时间，作者，和其他元数据

根据属性值的不同，后面对应不同的属性内容
主要分为两类
第一类关键词及主要描述：
```
<meta name="keywords"content="人工智能培训,Python培训,机器学习"/>
<meta name="description" content="人工智能时代来临黑马程序员率先推出Python+人工智能培训班, 提供python培训，人工智能培训，机器学习培训，python爬虫培训，数据挖掘培训，数据分析培训等人工智能相关培训服务。"/>
```
第二类：http操作,30秒后自动跳转
```
<meta http-equiv="refresh" content="2;URL=//www.baidu.com"
```
### link 
定义了一个文档和外部资源之间的关系,引用外部文件。

```
<link rel="fluid-icon" href="https://github.com/fluidicon.png" title="GitHub">
```

### script标签
定义了客户端的脚本文件,用于加载script脚本

### style
定义了HTML文档的样式文件


