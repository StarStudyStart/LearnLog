# css引入方式
1. 内联方式：*元素内部直接设置style属性*

```
<p style="color:red">内联</p>
```

2. 嵌入式：*head标签中直接引入style元素*
3. 外链式：*link标签直接导入*
4. ~~导入式~~：*style元素中通过@import url（）导入*
# 选择器
- **基础选择器**

id、class、标签、通配符（*）
```
.class{
    color:red
}

#id{
    color black
}
```
- **属性选择器**
```
E[attr]{
    
}

E[attr=val]{
    
}
 
 E[att~=val]    匹配所有att属性具有多个空格分隔的值、其中一个值等于“val”的E元素      td[class~=”name”] { color:#f00; }

 E[attr^=val]    匹配属性值以指定值开头的每个元素                     div[class^="test"]{background:#ffff00;}

 E[attr$=val]    匹配属性值以指定值结尾的每个元素                     div[class$="test"]{background:#ffff00;}

 E[attr*=val]    匹配属性值中包含指定值的每个元素                     div[class*="test"]{background:#ffff00;}
```
- **组合选择器**

```
p,h1{
    color:green
}
```
- **伪类:给选择器添加一些特殊效果**
1. a:link - 正常，未访问过的链接
2. a:visited - 用户已访问过的链接
3. a:hover - 当用户鼠标放在链接上时
4. a:active - 链接被点击的那一刻
```
5. p:before        在每个 <p> 元素的内容之前插入内容                    p:before{content:"hello";color:red}
6. p:after         在每个 <p> 元素的内容之前插入内容                    p:after{ content:"hello"；color:red}
```

```
a.one:link {
    color: green;
}

a.one:visited {
    color: yellow
}

a.one:hover {
    color: red
}
```
> ==NOTICE==:CSS的优先级为：**内联>id>clss>标签**。当出现多个选择器指向同一个元素时，依次统计出现个数
# CSS 常用属性