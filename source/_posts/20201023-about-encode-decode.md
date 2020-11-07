---
title: 讲明白编码/解码
categories:
    - Coding
tags:
    - WEB
    - 后端
    - Python
    - Javascript
---
对于程序中的“编码/解码”，尤其是初学者总是会有很多疑问，不同的程序之中问什么会有一些不一样的编码格式，并且有时候出现的乱码很让人头疼，本文想尽可能简单的说明白“编码/解码”之中的一些过程和原理。

<!--more-->

![没有绝对的产品](https://lilu-pic-bed.oss-cn-beijing.aliyuncs.com/my-blog/chanpin_tezheng.jpg)

## 必须了解的知识

- 8位二进制位是一个字节
- 对于英文编码，ASCII 码，一个字节表示一个字符，就够用了
- 对于世界的所有文字而言，这是远远不够的，因此每个国家都有出一套标准，但这就不是用一个字节表示一个字符了，比重中文就会是多个
- unicode 统一了所有文字的编码，对于ASCII而言，只需要在前面补0就可以在 unicode 中表示了
- 但是 unicode 占用的存储空间比较大
- 所以出现可变长的 utf-8 编码，一般用于存储和数据传输，当然还有其他的编码，但是 utf-8 最常用
- unicode 因为长度一样，因此编程会比较简单，因此编程语言在内存中一般使用 unicode

## 分析

话不多说，直接上图：

![各种编码之间的转换是通过unicode作桥梁的](https://s1.ax1x.com/2020/10/15/07Ao11.png)

从上图我们可以看出，可以把 unicode 编码作为一种中间编码来看待，其他编码可以通过 unicode 进行相互转化。当一种编码转成 unicode 编码的过程我们叫 decode，当把 unicode 转成一种特定的编码的过程我们叫 encode。由于 unicode 是定长的，因此在程序编码的时候一般会使用 unicode 作为程序的默认编码形式（python2.7 除外）

## 例子分析

#### 文件编码的存储和程序编码的存储

有时候，我们对于程序文件编码的存储和写的程序中的编码的时候有时候会混淆。当然现在IDE一般存储文件都用 utf-8 的编码，基本屏蔽了这个问题。但是在早些年，编程文件所使用的编码也会跟着系统编码走的，尤其是当我们使用非英文的文字的时候，保存后再打开有可能会出现乱码。

比如 python 文件我们一般会在头部先写上 `# -*- coding: utf-8 -*-`   表示当前文件的存储编码是 utf-8，这个和写的程序无关。而 `sys.setdefaultencoding('utf-8')` 才表示我们声明的变量都是用 utf-8 的编码存储的而不关心系统的默认编码。

[![07eS39.md.png](https://s1.ax1x.com/2020/10/15/07eS39.md.png)](https://imgchr.com/i/07eS39)

#### json.dumps的参数:ensure_ascii=False

json_dumps(dict)时，如果dict包含有汉字，一定加上ensure_ascii=False。否则按参数默认值True，意思是保证dumps之后的结果里所有的字符都能够被ascii表示，汉字在ascii的字符集里面，因此经过dumps以后的str里，汉字会变成对应的unicode。

虽然在Python3 里面汉字在内存里就是unicode表示，这里str里面的unicode经过loads也能还原成汉字，看起来似乎没什么问题。

但这样的操作只适合python，你能这么做事因为python认识unicode，python在执行json.loads的时候知道unicode是用来表示汉字而不是真的几个char拼接在一起，所以会对“\u”开头的这种字符串做转义理解。

不过以上转义理解是python中json.dumps和loads自己所遵守的规则，并不是通用的规则。

如果你脱离了python，比如把这串字符串以一定的方式送出去（写入文件或者通过网络发送），给另外的应用程序（比如记事本）处理，这个应用看到“\u45ef”就会老老实实的认为这是1个包含了6个char的字符串，绝不会跟1个汉字扯上联系。

```python
>>> a = {"hello" : "好"}
>>> json.dumps(a)
    '{"hello": "\\u597d"}'
>>> json.dumps(a, ensure_ascii=False)
    '{"hello": "好"}'
```

## 总结

编码问题是程序中的基本问题之一，主要是要理解各个编码之间的转换关系以及形成这种关系的原因。这样能更好的去解决偶然出现在代码之中的编码问题。

## Reference

[1] ASCII码与汉字的编码有什么区别: https://zhidao.baidu.com/question/1992697642196307547.html
[2] 【Python】json.dumps的参数:ensure_ascii=False: https://zhuanlan.zhihu.com/p/37504880
[3] 关于json.dumps中的ensure_ascii: https://blog.csdn.net/djskl/article/details/25225885
[4] 彻底搞懂编码ASCII、Unicode、GBK 和 UTF8 、UTF-16、UTF-32编码方式（非常经典）:https://blog.csdn.net/lc11535/article/details/100013653
