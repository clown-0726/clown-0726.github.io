---
title: 讲明白编码/解码
date: 2020-10-23 16:20:31
categories:
    - Coding
tags:
    - WEB
    - 后端
    - Python
    - Javascript
---
对于程序中的“编码”和“解码”，尤其是对于初级程序员来说总会有很多的疑问。当面对不同字符集编码之间的转换或者出现乱码时，有时候无从“下笔”，不知道该怎样解决问题。本文在这里尽可能简单的一次性说明白“编码”和“解码”之间的过程以及各个字符集之间的关系。

<!--more-->

![没有绝对的产品](https://lilu-pic-bed.oss-cn-beijing.aliyuncs.com/my-blog/20201023-about-encode-decode/chanpin_tezheng.jpg)

## 各个编码集的由来和关系

我们知道，在计算机中8位二进制位是一个字节，对于英文字符和常用的标点符号来讲，用一个字节表示一个字符完全够用，因此英文中最基础的 ASCII 编码集就是采用这种编码方式。由于计算机兴起于美国，最早期也就只有这一个编码集。

当计算机在整个世界上开始流行的时候，对于世界上的所有文字而言这是远远不够的。因此，每个国家都针对自己国家的文字出了一套字符集编码（比如中国的 GBK，俄文windows-1251），但这些编码集就不是用一个字节表示一个字符了，但是基本都是对 ASCII 的扩展，因此都兼容英文编码，这也就是为什么英文站点不需要考虑“乱码”的问题。

但是这种多编码集的情况对计算机编程是不友好的，因此 UNICODE 也就产生了。UNICODE 统一了所有文字的编码，对于 ASCII 而言，只需要在前面补0就可以在 UNICODE 中表示了。但是 UNICODE 占用的存储空间比较大，对于编程来说比较方便（这也就是为什么 python3 统一使用 UNICODE 编码的原因之一），对于数据的存储和传输会降低效率，随之出现了可变长的 UTF-8 编码，一般用于存储和数据传输，当然还有其他的编码，但是 UFT-8 最为常用。

## UNICODE 基础知识
UNICODE（中文：万国码、国际码、统一码、单一码）是计算机科学领域里的一项业界标准。它对世界上大部分的文字进行了整理、编码。Unicode 使计算机呈现和处理文字变得简单。

Unicode 至今仍在不断增修，每个新版本都加入更多新的字符。目前 Unicode 最新的版本为 2020 年 3 月 10 日公布的 13.0.0，已经收录超过 14 万个字符。

现在的 Unicode 字符分为 17 组编排，每组为一个平面（Plane），而每个平面拥有 65536（即 2 的 16 次方）个码值（Code Point）。然而，目前 Unicode 只用了少数平面，我们用到的绝大多数字符都属于第 0 号平面，即 BMP 平面。除了 BMP 平面之外，其它的平面都被称为补充平面。

![](https://lilu-pic-bed.oss-cn-beijing.aliyuncs.com/my-blog/20201023-about-encode-decode/unicode_bmp.png)

Unicode 标准也在不断发展和完善。目前，使用 4 个字节的编码表示一个字符，就可以表示出全世界所有的字符。

## 编码集之间的转换

![](https://lilu-pic-bed.oss-cn-beijing.aliyuncs.com/my-blog/20201023-about-encode-decode/encode-decode.png)

从上图我们可以看出，可以把 UNICODE 编码作为一种中间编码来看待，其他编码可以通过 UNICODE 进行相互转化。当一种编码转成 UNICODE 编码的过程我们叫 decode，当把 UNICODE 转成一种特定的编码的过程我们叫 encode。由于 UNICODE 是定长的，因此大多数程序编码一般会使用 UNICODE 作为程序的默认编码形式（python2.7 除外）。

但是上图的转换取决于“字符集”是否支持“所转换的文字”。比如俄语和日语既可以使用 GBK 表示也可以使用 windows-1251（俄语常用的字符集）。因此俄语和日语可以通过上图进行转换。但是土耳其语则不能用 GBK表示。

俄语在通过 gbk，gb2312，windows-1251 进行编码

```python
lang = "Привет"

lang.encode("gbk")
# b'\xa7\xb1\xa7\xe2\xa7\xda\xa7\xd3\xa7\xd6\xa7\xe4'

lang.encode("gb2312")
# b'\xa7\xb1\xa7\xe2\xa7\xda\xa7\xd3\xa7\xd6\xa7\xe4'

lang.encode("windows-1251")
b'\xcf\xf0\xe8\xe2\xe5\xf2'

```

土耳其语 gbk 进行编码

```python
land = "Avrupa-lı-laş"
land.encode("gbk")

# 结果如下：
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
UnicodeEncodeError: 'gbk' codec can't encode character '\u0131' in position 8: illegal multibyte sequence
```

## 编码集在数据传输的作用

UNICODE 相当于规定了字符对应的码值，这个码值必须编码成字节的形式去传输和存储。最常见的编码方式是 UTF-8，另外还有 UTF-16，UTF-32 等。UTF-8 之所以能够流行起来，是因为其编码比较巧妙，采用的是变长的方法。也就是一个 UNICODE 字符，在使用 UTF-8 编码表示时占用 1 到 4 个字节不等。最重要的是 UNICODE 兼容 ASCII 编码，在表示纯英文时，并不会占用更多存储空间。而汉字呢，在 UTF-8 中，通常是用三个字节来表示。

```python
lang = "你好"
lang.encode("utf-8")
# b'\xe4\xbd\xa0\xe5\xa5\xbd'
```

## 实例分析

#### 文件编码的存储和程序编码的存储

有时候，我们对于“程序文件编码的存储”和“所写程序中的编码”有时候会混淆。当然现在IDE一般存储文件都用 utf-8 的编码，基本屏蔽了这个问题。但是在早些年，编程文件所使用的编码也会跟着系统编码走的，尤其当我们使用非英文的文字的时候，保存文件后再打开（使用其他编码打开）有可能会出现乱码。

比如 python 文件我们一般会在头部先写上 `# -*- coding: utf-8 -*-` 表示当前文件的存储编码是 utf-8，这个和写的程序无关。而 `sys.setdefaultencoding('utf-8')` 表示我们声明的变量都是用 utf-8 的编码存储和传输的而不关心系统的默认编码。

![](https://lilu-pic-bed.oss-cn-beijing.aliyuncs.com/my-blog/20201023-about-encode-decode/py-file-en-de-coding.png)

#### json.dumps的参数: ensure_ascii=False

json_dumps(dict)时，如果dict包含有汉字，一定加上 ensure_ascii=False。否则按照参数的默认值 True，意思是保证dumps之后的结果里所有的字符都能够被 ASCII 表示，汉字在 ASCII 的字符集里面，因此经过dumps以后的str里，汉字会变成对应的 UNICODE。

虽然在Python3 里面汉字在内存里就是 UNICODE 表示，这里 str 里面的 UNICODE 经过 loads 也能还原成汉字，看起来似乎没什么问题。

但这样的操作只适合 python，你能这么做是因为 python 认识 UNICODE，python在执行 json.loads 的时候知道 UNICODE 是用来表示汉字而不是真的几个 char 拼接在一起，所以会对“\u”开头的这种字符串做转义理解。

不过以上转义理解是 python 中 json.dumps 和 loads 自己所遵守的规则，并不是通用的规则。

如果你脱离了 python，比如把这串字符串以一定的方式送出去（写入文件或者通过网络发送），给另外的应用程序（比如记事本）处理，这个应用看到 “\u45ef” 就会老老实实的认为这是1个包含了6个 char 的字符串，绝不会跟1个汉字扯上联系。

```python
a = {"hello" : "好"}
json.dumps(a)
# '{"hello": "\\u597d"}'
json.dumps(a, ensure_ascii=False)
# '{"hello": "好"}'
```

#### 为什么输入数字或者英语 encode 返回的是它本身，但是中文日文什么的就会返还一些字母 + 数字

先介绍两个函数和进制表示法：
- `ord()` 得到的是 Unicode code point，范围是0x0000到0xFFFF，称为UCS-2编码。utf-8编码在这里是三字节。
- `hex()` hex 可以将二进制转变为十六进制。
- `\u`表示 UNICODE 编码，`\x`表示16进制数。
- 十进制 `110119` 在二进制表示为 `0b11010111000100111`，在十六进制表示为 `0x1ae27`，在八进制表示为 `0o327047`。

`b'abc'` 这种形式就是字节串，而不是二进制串。如何得到一个字符的二进制表示形式呢？可以通过 ord() 和 hex() 得到二进制和十六进制表示。

```
>>> ord('中')
20013
>>> hex(ord('中'))
'0x4e2d'
>>>
>>> '\u4e2d'
'中'
```

由于英文字母在 utf-8，gbk 这些编码中占用一个字节，因此这些编码集兼容 ASCII，也就是说 ASCII 是这些编码集的子集。而像中文日文需要多余一个字节表示。

```
# 中文 encode
>>> '中'.encode('utf-8')
b'\xe4\xb8\xad'
>>>
>>> '中'.encode('gbk')
b'\xd6\xd0'

# 英文 encode
>>> 'abc'.encode('utf-8')
b'abc'
>>>
>>> 'abc'.encode('gbk')
b'abc'
```


也就是说，对于 ASCII 编码，通常很多编码都是相同的。但是涉及到中文、日文、emoji等，往往需要多个字节来表示，这个时候取决于具体编码标准会有不同，比如 GBK、GB2312、UTF-8、UTF-16等等。


## 总结

本文首先介绍各个编码集的来源，并且通过编码集之间的转换阐述各个编码集之间的关系，最后通过实例分析加深对编码的理解。简单总结为以下三点：
- UNICODE 可以容纳所有字符，主要用于各个语言编程使用，但是不适合于字符的存储和传输。
- UTF-8 为可变长的编码，主要用于字符的存储和传输。
- 各个国家有自己的编码集合，比如中国的 GBK，俄国的 windows-1251。

编码问题是程序中的基本问题之一，理解各个编码之间的转换关系以及形成这种关系的原因，这样能更好的解决出现在代码之中的编码问题。

## Reference

[1] ASCII码与汉字的编码有什么区别: https://zhidao.baidu.com/question/1992697642196307547.html  
[2] 【Python】json.dumps的参数:ensure_ascii=False: https://zhuanlan.zhihu.com/p/37504880  
[3] 关于json.dumps中的ensure_ascii: https://blog.csdn.net/djskl/article/details/25225885  
[4] 彻底搞懂编码ASCII、Unicode、GBK 和 UTF8 、UTF-16、UTF-32编码方式（非常经典）: https://blog.csdn.net/lc11535/article/details/100013653  
[5] 字符编码笔记：ASCII，Unicode 和 UTF-8: https://www.ruanyifeng.com/blog/2007/10/ascii_unicode_and_utf-8.html  
[6] python encode ascii_python中，为何输入print（“a”.encode（“ASCii”））出来显示的是b‘a’？: https://blog.csdn.net/weixin_39628063/article/details/110696348

