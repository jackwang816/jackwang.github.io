---
title: Markdown语法
slug: markdown-grammer
date: 2020-12-18
categories:
- 2020-12
tags:
- Markdown
# thumbnailImagePosition: left
# thumbnailImage: //d1u9biwaxjngwg.cloudfront.net/chinese-test-post/vintage-140.jpg
# comments: false
# draft: true
---

<!--more-->

<!-- toc -->

# Markdown 语法

## 1. 分级标题

一级标题: ===

二级标题: ---

(H1-H6)：# H1, ## H2, ### H3，#### H4...

示例：

```
一级标题
============================

二级标题
--------------------------------------------------

### 三级标题
```

## 2. 斜体和粗体

使用 * 和 ** 表示斜体和粗体。

示例：

这是 *斜体*，这是 **粗体**。

## 3. 文字引用

使用 > 表示文字引用。

示例：

    > 北冥有鱼，其名为鲲。

> 北冥有鱼，其名为鲲。

## 4. 外链接

使用 \[描述](链接地址) 为文字增加外链接。

示例：

    [blog](https://jackwang816.github.io)

[blog](https://jackwang816.github.io)

## 5.  插入图像

使用 \!\[描述](图片链接地址) 插入图像。

示例：

    ![头像](https://jackwang816.github.io/images/profilePortrait.jpg)

![头像](https://jackwang816.github.io/images/profilePortrait.jpg)

## 6 列表

### 无序列表

使用 *，+，- 表示无序列表。

示例：

    - 无序列表 一
    * 无序列表 二
    + 无序列表 三

- 无序列表 一
* 无序列表 二
+ 无序列表 三

### 有序列表

使用数字和点表示有序列表。

示例：

    1. 有序列表 一
    2. 有序列表 二
    3. 有序列表 三

1. 有序列表 一
2. 有序列表 二
3. 有序列表 三

### Todo 列表

使用带有 [ ] 或 [x] （未完成或已完成）项的列表语法撰写一个待办事宜列表，并且支持子列表嵌套以及混用Markdown语法，例如：

    - [ ] **Markdown 语法**
        - [x] html 标签
        - [x] LaTex
            - [x] 学习 latex 语法
            - [x] 练习 latex 公式
    - [ ] **成都旅行**
        - [x] 机票
        - [ ] 酒店
        - [ ] 旅游景点门票
        
对应显示如下待办事宜 Todo 列表：
        
- [ ] **Markdown 语法**
    - [x] html 标签
    - [x] LaTex
        - [x] 学习 latex 语法
        - [x] 练习 latex 公式
- [ ] **成都旅行**
    - [x] 机票
    - [ ] 酒店
    - [ ] 旅游景点门票

## 7. 代码块

### 行内代码块

使用 \`代码` 表示行内代码块。

示例：

    `C++`

`C++`

### 整段代码块

使用 四个缩进空格 表示代码块。

示例：

    这是一个代码块，此行左侧有四个不可见的空格。

### 加强的代码块

示例 1：

```
$ git status
```

Python 示例：

``` python
def _dp_find_backtrace(self, state: list, n: int, m: int) -> list:
    i, j = n, m
    backtrace = []
    while (not (i==0 and j==0)):
        i, j, move = self._find_min_index(state, i, j)
        backtrace.append((i, j, move))
    return backtrace
```

JavaScript 示例：

``` javascript
/**
* nth element in the fibonacci series.
* @param n >= 0
* @return the nth element, >= 0.
*/
function fib(n) {
  var a = 1, b = 1;
  var tmp;
  while (--n >= 0) {
    tmp = a;
    a += b;
    b = tmp;
  }
  return a;
}

document.write(fib(10));
```

## 8. 删除线

使用 ~~ 表示删除线。

~~这是一段错误的文本。~~

## 9. 注脚

使用 \[^keyword\] 表示注脚。

这是一个注脚[^footnote]的样例。

这个第二个注脚[^keyword]引用文章链接

## 10. LaTeX 公式

`$` 表示行内公式： 

质能守恒方程可以用一个很简洁的方程式 $E=mc^2$ 来表达。

`$$` 表示整行公式：

$$\sum_{i=1}^n a_i=0$$

$$f(x_1,x_x,\ldots,x_n) = x_1^2 + x_2^2 + \cdots + x_n^2 $$

$$\sum_{k=0}^{j-1}{\widehat{\gamma}{_k}{_j}}$$

访问 [MathJax](http://meta.math.stackexchange.com/questions/5020/mathjax-basic-tutorial-and-quick-reference) 参考更多使用方法。

## 11. 表格

示例：

    |项目|价格|数量|
    |--------|-----:|:----:|
    |计算机|$1600|5|
    |手机|$12|12|
    |管线|$1|234|

|项目|价格|数量|
|--------|-----:|:----:|
|计算机|$1600|5|
|手机|$12|12|
|管线|$1|234|

## 12. Html 标签

示例：

    <table>
        <tr>
            <th rowspan="2">代办事项</th>
            <th>一号</th>
            <th>二号</th>
            <th>三号</th>
        </tr>
        <tr>
            <td>aa</td>
            <td>bb</td>
            <td>cc</td>
        </tr>
    </table>


<table>
    <tr>
        <th rowspan="2">代办事项</th>
        <th>一号</th>
        <th>二号</th>
        <th>三号</th>
    </tr>
    <tr>
        <td>aa</td>
        <td>bb</td>
        <td>cc</td>
    </tr>
</table>     
        
[^footnote]: 这是第一个 *注脚* 的 **文本**。

[^keyword]: 引用 [最小子数组问题Kadane算法](/2020/12/kadane-algorithm/)

