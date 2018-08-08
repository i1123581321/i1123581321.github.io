---
layout: post
title: "HTML表单"
description: "表单及表单元素"
categories: [笔记]
tags: [计算机,HTML]
redirect_from:
  -/2018/08/08/
---

# HTML 表单元素

表单[^1]在网页中表示了文档中的一个区域，这个区域包含有 **交互控制元件**，用来向 web 服务器提交信息。表单采用`<form></form>`标签定义。

* Kramdown table of contents
{:toc .toc}

常用的表单属性有`action`和`method`。`action`定义了表单提交时执行的动作，一般是将表单提交至 web 服务器上的网页，也可指定服务器上的脚本（如 php 文件）来处理提交的表单，不设置的话默认为当前页面。`method`定义了提交表单时采用的 HTTP 方法（GET 或 POST）。

e.g.

```html
<form action="action_page.php" method="GET"></form>
```

## 表单元素

与表单相关的元素有

```html
<button>
<fieldset>
<input>
<keygen>
<label>
<meter>
<object>
<output>
<progress>
<select>
<textarea>
```

其中较为常用的有`<button>, <fieldset>, <input>, <label>, <select>, <textarea>`等。

需注意 **可提交的元素（包括 `<button>，<input>，<keygen>，<object>，<select> 和 <textarea>`）需设置`name`属性，只有正确设置`name`属性的才会被提交。**

### Button

`<button>`[^2]元素定义了可点击的按钮

e.g.

```html
<button type="button">Click here!</button>
```

实际效果如下（显示效果视浏览器不同而异）

<button type="button">Click here!</button>

### Fieldset

`<fieldset>`[^3]元素可以为表单中的数据分组，加入可选的`<legend>`标签后可以为其添加一个标题

e.g.

```html
<form>
    <fieldset>
        <legend>My Form</legend>
        <input type="text" name="text">
        <input type="button" value="Click here!">
    </fieldset>
</form>
```

实际效果如下（显示效果视浏览器不同而异）

<form>
    <fieldset>
        <legend>My Form</legend>
        <input type="text" name="text">
        <input type="button" value="Click here!">
    </fieldset>
</form>

### Input

`<input>`[^4]是表单中最重要的元素，它用于处理用户的输入，**根据不同的`type`属性，`<input>`可以处理多种多样的输入**（正确提交需为每个输入字段指定`name`属性）

`<input>`常用的属性有（带有*的为HTML5新增的属性）

|type|作用|e.g.|
|:-|:-|
text|供文本输入的单行输入字段|<input type="text" value="text">
password|用于输入密码，输入的内容会显示为掩码（星号或圆点）|<input type="password" value="123456">
button|生成基本的按钮|<input type="button" value="button">
submit|生成提交表单数据至表单处理程序的按钮|<input type="submit">
reset|生成将表单所内容设置为缺省值的按钮|<input type="reset">
radio|单选按钮（同一组选项的`name`属性要相同，必须使用`value`属性定义此控件被提交时的值）|<input type="radio" name="radio" value="value1">value1<br><input type="radio" name="radio" value="value2">value2<br><input type="radio" name="radio" value="value3">value3
checkbox|复选框（同一组选项的`name`属性要相同,必须使用`value`属性定义此控件被提交时的值）|<input type="checkbox" name="checkbox" value="value1">value1<br><input type="checkbox" name="checkbox" value="value2">value2<br><input type="checkbox" name="checkbox" value="value3">value3
date*| 用于输入日期（年，月，日，不包括时间）|<input type="date">
datetime*| 输入基于 UTC 时区的日期时间（时，分，秒及几分之一秒）|<input type="datetime">
datetime-local*| 用于输入日期时间，不包含时区|<input type="datetime-local">
time*| 用于输入不含时区的时间|<input type="time">
month*| 用于输入年月的控件，不带时区|<input type="month">
week*| 用于输入一个由星期 - 年组成的日期，日期不包括时区。|<input type="week">
color*| 用于指定颜色|<input type="color">
email*| 用于编辑 e-mail 的字段|<input type="email">
number*|用于输入浮点数|<input type="number">
range*| 用于输入不精确值|<input type="range">
search*| 用于输入搜索字符串的单行文本字段，换行会被从输入的值中自动移除|<input type="search">
tel*| 用于输入电话号码|<input type="tel">
url*| 用于输入 URL|<input type="url">

### Label

`<label>`[^5]元素可为输入字段显示标题，一般有两种用法，直接包围在元素两边或是根据元素的id绑定到其上

e.g.

```html
<!--1-->
<label>Name: <input type="text" id="User" name="Name" /></label>
<!--2-->
<label for="User">Name: </label>
<input type="text" id="User" name="Name" />
```

两种不同的方式实际的显示效果是相同的（显示效果视浏览器不同而异）

<label for="User">Name: </label>
<input type="text" id="User" name="Name" />

### Select

`<select>`[^6]元素可与`<option>`元素相结合生成下拉列表

e.g.

```html
<select name="select">
  <option value="value1">Value 1</option> 
  <option value="value2" selected>Value 2</option>
  <option value="value3">Value 3</option>
</select>
```

其中包含`selected`属性的选项将被默认选择（若没有被指定为`selected`的元素的话默认选择第一个选项）

实际效果如下（显示效果视浏览器不同而异）

<select name="select">
  <option value="value1">Value 1</option> 
  <option value="value2" selected>Value 2</option>
  <option value="value3">Value 3</option>
</select>

### Textarea

`<textarea>`[^7]可生成用于多行输入的文本框，并可指定其所占的空间大小。

e.g.

```html
<textarea name="textarea" rows="10" cols="50">Write something here</textarea>
```

实际效果如下（显示效果视浏览器不同而异）

<textarea name="textarea" rows="10" cols="50">Write something here</textarea>

## 表单元素属性

在表单的元素中有一些常用的属性（带有*的为HTML5新增的属性）

|attributes|作用|e.g.|
|:-|:-|:-:|
value|规定输入字段的初始值|<input type="text" value="text">
name|规定元素名称|/
readonly|规定输入字段为只读（不能修改）|<input type="text" value="text" readonly>
disabled|规定输入字段是禁用的，被禁用的元素不可用，不可点击且不会被提交|<input type="text" value="text" disabled>
size|规定输入字段框的尺寸（以字符计）|<input type="text" value="text" size="10">
maxlength/minlength*|规定输入字段允许的最大/最小长度|/
placeholder*|向用户提示可以在控件中输入的内容|<input type="text" placeholder="text">
required*|带有required属性的输入为必填|<input type="text" value="text" required>

## 参考

* [MDN Web 文档](https://developer.mozilla.org/zh-CN/)
* [w3school 在线教程](http://www.w3school.com.cn/index.html)

[^1]:["form - HTML（超文本标记语言）\| MDN"](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/form)
[^2]:[&lt;button&gt; - HTML（超文本标记语言）\| MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/button)
[^3]:[&lt;fieldset&gt; - HTML（超文本标记语言）\| MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/fieldset)
[^4]:[&lt;input&gt;：输入（表单输入）元素 - HTML（超文本标记语言）\| MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/input)
[^5]:[&lt;label&gt; - HTML（超文本标记语言）\| MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/label)
[^6]:[&lt;select&gt; - HTML（超文本标记语言）\| MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/select)
[^7]:[&lt;textarea&gt; - HTML（超文本标记语言）\| MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/textarea)