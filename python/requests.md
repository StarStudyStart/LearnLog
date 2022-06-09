# POST请求发送相同字段名的多个值

我们一般发送POST请求时一般是将字典转的key-value映射为form表单的字段名和值；但是如果一个form字段对应多个值，该怎样构建POST请求的body体呢？

1、字典中定义多个相同的key，只有后一个值是有效的；所以考虑将字典中的值改为列表形式

2、字典中的列表形式值，无法使用`urlencode`直接转换为对应请求头

3、`urlencode`除了接受字典值意外，还可以接受一个由两元素的元组组成的序列 `a sequence of two-elementes tuples`；

4、考虑编写一个中将函数将字典转换成一个两元素的元组序列，如果字典中元素的值为列表，则遍历列表拆分成多个相同key的两元素的元组序列。

```
def encode_param(data: dict):
    result = []
    for k, vs in data.items():
        if isinstance(vs, str) or not hasattr(vs, '__iter__'):
            vs = [vs]
        for v in vs:
            if v is not None:
                result.append((k.encode('utf-8') if isinstance(k, str) else k,
                               v.encode('utf8') if isinstance(v, str) else v))
    return urlencode(result, doseq=True)


if __name__ == '__main__':
    print(encode_param({'id': 1, 'status': [201, 202]}))
```

# 使用requests.POST上传文件时，不要指定请求头中的Content-Type字段

Content-Type中的boundary字段使用来分割请求体中的多个字段的**分隔符**

发送请求时利用boundary分隔符字段对body进行编码；服务器接受到multipart/form-data的请求时，首先会获取Content-Type中的boundary分隔符字段，依据boundary的值对请求body中的form表单进行解码；

使用requests构造上传文件的POST请求时，为了保持Content-Type中的boundary字段、实际请求体中的boundary字段一致。

***构造上传文件的post请求时，请求头中要手动移除“Content-Type”的键值；***

以下摘取部分源码，进行原因说明

```
      ...
      else:
            # Multi-part file uploads.
            if files:
                (body, content_type) = self._encode_files(files, data)
            else:
                if data:
                    body = self._encode_params(data)
                    if isinstance(data, basestring) or hasattr(data, 'read'):
                        content_type = None
                    else:
                        content_type = 'application/x-www-form-urlencoded'

            self.prepare_content_length(body)

            # Add content-type if it wasn't explicitly provided.
            if content_type and ('content-type' not in self.headers):
                self.headers['Content-Type'] = content_type
```

如果post请求指定了files字段， 则第5行代码`_encode_files`方法就会将 `files`中的文件字段、`data`中的form表单字段编码成`body`，并且使用随机的boundary进行分割；该方法返回请求`body`的二进制形式、`content_type`字段（字段中包含用于编码body的boundary值）。

`18`行代码则是将`headers`中的`Content-Type`替换成`_encode_files()`返回的`content_type`，这样能够保证body中的分隔符字段与content-type中指定的分割符是一致的，此时服务器才能正常解析到上传的文件数据。

如果用户构造的请求头headers中已经包含了`‘content-type’`字段，请求`body`中的实际分隔符值和用户指定的分割符是就不可能没都一致，服务器自然无法解析body

```
def upload_file(url, file_path):
    multiple_files = {
        'filed_name': ('file_name', open(file_path, 'rb'), 'image/png')
    }
    headers = {
        "Accept": "*/*",
        "Accept-Encoding": "gzip, deflate",
        "Content-Length": "31670",
        "Host": "httpbin.org",
        "Content-Type": "multipart/form-data; boundary=45fde6696f27dacd0987fe7dd41bfb82",
        "User-Agent": "python-requests/2.27.1",
        "X-Amzn-Trace-Id": "Root=1-62a1fbfc-03e8d38d69c6c09c03893cfb"
    }
    data = {
        'form_name1': "form_name",
        'form_name2': "form_name",
    }
    # headers.pop("Content-Type") 
    resp = requests.request('POST', url, data=data, files=multiple_files, headers=headers)
    return resp.text
```

请求头中指定了Content-Type,查看服务器返回结果如下, 所有form表单中的字段均未得到正确解析：

```
{
  "args": {}, 
  "data": "", 
  "files": {}, 
  "form": {}, 
  "headers": {
    "Accept": "*/*", 
    "Accept-Encoding": "gzip, deflate", 
    "Content-Length": "31670", 
    "Content-Type": "multipart/form-data; boundary=45fde6696f27dacd0987fe7dd41bfb82", 
    "Host": "httpbin.org", 
    "User-Agent": "python-requests/2.27.1", 
    "X-Amzn-Trace-Id": "Self=1-62a201e2-0eb441db0e3547f66a3ceb1d;Root=1-62a1fbfc-03e8d38d69c6c09c03893cfb"
  }, 
  "json": null, 
  "origin": "112.10.237.12", 
  "url": "https://httpbin.org/post"
}
```

如果将第19行代码的注释移除，则返回结果如下，正常得到解析

```
{
  "args": {}, 
  "data": "", 
  "files": {
    "filed_name": "data:image/png;base64,AAABAAEAO4AAAAEAIAAoegAAFgAAA....etc"
  }, 
  "form": {
    "form_name1": "form_name", 
    "form_name2": "form_name"
  }, 
  "headers": {
    "Accept": "*/*", 
    "Accept-Encoding": "gzip, deflate", 
    "Content-Length": "31670", 
    "Content-Type": "multipart/form-data; boundary=4e4fa5c75d4b4abf7a2941690dfd9892", 
    "Host": "httpbin.org", 
    "User-Agent": "python-requests/2.27.1", 
    "X-Amzn-Trace-Id": "Self=1-62a20937-50c28608677908d12e94fe78;Root=1-62a1fbfc-03e8d38d69c6c09c03893cfb"
  }, 
  "json": null, 
  "origin": "112.10.237.12", 
  "url": "https://httpbin.org/post"
}
```

这里提供第二种思路（也是我最先从网上找到的答案，后面自己尝试才慢慢知道了问题出在content-type）就是反过来利用自己定义的boundary构造请求body

```
import random
import string
from requests_toolbelt.multipart.encoder import MultipartEncoder

def upload_file_with_boundary(url, file_path):
    """
     上传文件时自定义boundary
    """

    body = {
        'form_name1': "form_name",
        'form_name2': "form_name",
        'filed_name': ('file_name', open(file_path, 'rb'), 'image/png')
    }
    boundary = "----WebKitFormBoundary" + ''.join(random.sample(string.ascii_letters + string.digits, k=16))
    m = MultipartEncoder(body, boundary=boundary)  # 利用boundary自己编码请求体
    resp = requests.request('POST', url, data=m, headers={"Content-Type": m.content_type})
    return resp.text
    
```

服务器返回结果如下：

```
{
  "args": {}, 
  "data": "", 
  "files": {
    "filed_name": "data:image/png;base64,AAABAAEAO4AAAAEAIAAoegAAFgAAACgAAAA7AAA...etc"
  }, 
  "form": {
    "form_name1": "form_name", 
    "form_name2": "form_name"
  }, 
  "headers": {
    "Accept": "*/*", 
    "Accept-Encoding": "gzip, deflate", 
    "Content-Length": "31694", 
    "Content-Type": "multipart/form-data; boundary=----WebKitFormBoundary4lhQBczTZeqPJHjG", 
    "Host": "httpbin.org", 
    "User-Agent": "python-requests/2.27.1", 
    "X-Amzn-Trace-Id": "Root=1-62a20a7f-6a5f8f2326e61b393884faf0"
  }, 
  "json": null, 
  "origin": "112.10.237.12", 
  "url": "https://httpbin.org/post"
}
```

可以看到请求body 仍然得到了正常解析