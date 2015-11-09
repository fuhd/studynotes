atom的markdown语法
===================
### 1. 区块元素
#### 1.1 标题
markdown支持两种标题语法，类setext和类atx形式。

类setext形式是用底线的形式，利用 = （最高阶标题）和 - （第二阶标题）。任何数量的 = 和 - 都可以
有效果。显示效果是标题下都有一条分隔线！

    测试最高阶标题
    ===========
显示为：
测试最高阶标题
===========
    测试第二阶标题
    ------------
显示为：
测试第二阶标题
------------

类atx形式则是在行首插入1到6个 # ,对应到Html的H1到H6。

    # H1
    ## H2
    ### H3
    #### H4
    ##### H5
    ###### H6

显示为：
# H1
## H2
### H3
#### H4
##### H5
###### H6

#### 1.2 区块引用
    >在markdown文件中建立一个区块引用，
    >那会看起来像是你自己先断好行，
    >然后在每行的最前面加上 >，有点像email信件中的引言部分。
    >引用的区块内也可以使用其他的markdown语法：包括标题，列表，代码区块等。
    >##### 区块内的标题示例
    >+ 区块内列表示例1
    >+ 区块内列表示例2

显示为：
>在markdown文件中建立一个区块引用，
>那会看起来像是你自己先断好行，
>然后在每行的最前面加上 >，有点像email信件中的引言部分。
>引用的区块内也可以使用其他的markdown语法：包括标题，列表，代码区块等。
>##### 区块内的标题示例
>+ 区块内列表示例1
>+ 区块内列表示例2


#### 1.3 列表
markdown支持有序列表和无序列表。

无序列表用星号（*）,加号（+）或是减号（-）作为列表标记。

    * 示例1
    + 示例2
    - 示例3

显示为：

* 示例1
+ 示例2
- 示例3

有序列表则使用数字接着一个英文句点。

    1. 示例1
    2. 示例2
    3. 示例3

显示为：

1. 示例1
2. 示例2
3. 示例3

#### 1.4 代码区块
要在markdown中建立代码区块很简单，只要简单地缩进4个空格或是一个制表符就可以。示例：

    javascript,html,java,ruby,python3,rust,haskell

#### 1.5 分隔线
你可以在一行中用三个以上的星号，减号，下划线来建立一个分隔线。

    ***
    ---
    ___

显示为：

***

---
___
### 2. 区段元素
#### 2.1 链接
markdown支持两种形式的链接语法：行内式和参考式。链接文字用方括号[]来标记。

要建立一个行内式的链接，只要在方括号后面紧接着圆括号并插入网址链接即可：

    This is [an example](http://www.baidu.com) inline link!

显示为：

This is [an example](http://www.baidu.com) inline link!

如果你是要链接到同样主机的资源，你可以使用相对路径：

    See my [About](/About/) page for details.

显示为：

See my [About](/About/) page for details.

参考式的链接是在链接文字的括号后面再接上另一个方括号，而在第二个方括号里面要填入用以辨识链接的标记：

    This is [an example][id] reference-style link.
    [id]: http://www.baidu.com/

显示为：

This is [an example][id] reference-style link.
[id]: http://www.baidu.com/

#### 2.2 强调
用两个星号或两个下划线包起来表示粗体，用一个星号或一个下划线包起来表示斜体。

    **这个示例表示粗体**

显示为：

**这个示例表示粗体**

    _这个示例表示斜体_

显示为：

_这个示例表示斜体_

#### 2.3 代码
如果要标记一小段行内代码，你可以反引号把它包起来（｀），例如：

    User the `printf()` function.

显示为：

User the `printf()` function.

如果要在代码区段内插入反引号，你可以用多个反引号来开启和结束代码区段：

    ``There is a literal backtick (`) here.``

显示为：

``There is a literal backtick (`) here.``

代码区段的起始和结束端都可以放入一个空白，起始端后面一个，结束端前面一个，这样你就可以在区段的一开
始就插入反引号：

    `` ` ``

显示为：

`` ` ``

    `` `javascript` ``

显示为：

`` `javascript` ``

在代码区段内，& 和 尖括号都会被自动地转成HTML实体，这使得插入HTML原始码变得很容易：

Please don´t use any `<blink>` tags.

#### 2.4 图片
markdown使用两种方式标记图片：行内式和参考式。

**行内式：**

    ![示例图片1](images/img1.jpg)

显示为：

![示例图片1](images/img1.jpg)

一个惊叹号!；接着一个方括号[]，里面放上图片的替代文字；接着一个圆括号（），里面放上图片地址。

**参考式：**

    ![示例图片2][imgid1]
    [imgid1]: images/img2.jpg

显示为：

![示例图片2][imgid1]
[imgid1]: images/img2.jpg

到目前为止，markdown还没有办法指定图片的宽高。如果你需要的话，你可以使用普通`<img>`标签。

### 3. 与标准markdown的区别
#### 3.1 删除线

    ~~Mistaken text.~~

显示为：

~~Mistaken text.~~

#### 3.2 代码块

如果有一整块代码需要包围，可以使用两个```包括代码，例如：

    ```
    x = 0
    x = 2 + 2
    what is x
    ``｀

显示为：

```
x = 0
x = 2 + 2
what is x
``｀

#### 3.2 语法高亮
代码块可以使用语法高亮！！在你的代码块中添加一个可选的语言标识符。示例：

    ```ruby
    require ´redcarpet´
    markdown = Redcarpet.new(¨Hello World!¨)
    puts markdown.to_html
    ```

显示为：

```ruby
require ´redcarpet´
markdown = Redcarpet.new(¨Hello World!¨)
puts markdown.to_html
```

能够支持的语法可以从 [这里](https://github.com/github/linguist/blob/master/lib/linguist/languages.yml) 这里获得。

#### 3.3 表格
你可以创建表格，通过符号（-）分开第一行和其他行，通过（|）分开各个列：

    First Header|second Header
    ------------|-------------
    Content Cell|Content Cell
    Content Cell|Content Cell

显示为：

First Header|second Header
------------|-------------
Content Cell|Content Cell
Content Cell|Content Cell
