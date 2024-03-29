# 常见的编解码概念

- `unicdoe`: 囊括了几乎所有的国家不同语言的一套字符编码集

- `UTF-8`:可变长的字符编码集(节省空间)，将`unicode`的抽象码映射成一个8位（单个字节的）字符集, `utf8`编码通常由【1~4】个字节组成

- `UTF-16`: 将`unicode`的抽象码映射成一个16位(2个字节）的字符集， `utf16`编码通常由2个或4个字节组成

- `BOM`： `Bytes order mark`字节编码顺序标志；分为大端字节序，小端字节序

    

    超过两位的字节编码在进行网络、文件传输时，不同的`cpu`编解码字节顺序可能不同。为了能够在不同的环境下都能正确的解码字符，超过两位字节的编码格式必须要携带`BOM`标记。如：`UTF-16`、`UTF-32`

    而`UTF-8`由于其特殊的编解码条件，不需要带`BOM`标记，`python`也提供了带`BOM`标记的`utf-8`编码格式：`utf-8-sig`(微软系基本上所有的软件都是带`BOM`标记，`utf-8-sig`常用于微软系的软件编解码)

    

    传输过程中，解析端按照`BOM`的编码序列解码，才能得到正确的字符，否则就会乱码

    ```
    >>> '中'.encode('utf-16_le').decode('utf-16_be') 
    'ⵎ'								 # 以小端序列编码,大端序列解码,则出现乱码
    >>>
    ```

    `BOM`的两种排序方式

    - 大端序列(`big endian`): `BOM` 中记作`FEFF`，低位字节在高内存地址
    - 小端序列(`littel endian`):  `BOM` 中记作`FFFE`，低位字节在低内存地址

    ```
    >>> '中'.encode('utf-16')
    b'\xff\xfe-N'			# FFFE 小端序列，‘-N’自动转码成unicode
    >>> hex(ord('-'))
    '0x2d'
    >>> hex(ord('N'))
    '0x4e'					# 小端序列下：`中`的utf-16编码，即为`FFFE2D4E`
    >>> '中'.encode('utf-16_be') # 大端序列下：`中`的utf-16编码，即为`FFFE 4E2D` 
    b'N-'
    >>> '中'.encode('utf-16_le')	
    b'-N'
    >>>
    ```

    小端序列下：中的`utf-16`编码 为`FFFE 2D4E`，大端序列则正好将字节顺序翻转：`FFFE 4E2D`

    ## `UTF8`

    以`utf8`编码的字符集，根据编码首位含有多少位`1`即可确认，该字符集由几个字节组成。方式如下：
    
    ```
    0xxxxxxx
    110xxxxx 10xxxxxx
    1110xxxx 10xxxxxx 10xxxxxx
    11110xxx 10xxxxxx 10xxxxxx 10xxxxxx
    111110xx 10xxxxxx 10xxxxxx 10xxxxxx 10xxxxxx
    1111110x 10xxxxxx 10xxxxxx 10xxxxxx 10xxxxxx 10xxxxxx
    ```
    
    

