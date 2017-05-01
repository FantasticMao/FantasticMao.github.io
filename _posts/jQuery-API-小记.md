---
title: jQuery API 小记
date: 2017-04-21 15:44:01
categories: JavaScript
---
摘自《JavaScript 权威指南》第 19 章 jQuery 类库。<!-- more -->

# jQuery 基础
JavaScript 的核心 API 设计的很简单，但由于不同浏览器之间的严重不兼容，导致客户端的 API 过于复杂。使用 JavaScript 框架或工具类库能简化通用操作，能隐藏浏览器之间的差异，这让很多程序员在开发 Web 应用时变得更简单。

jQuery 类库定义了一个全局函数：`jQuery()`。该函数使用频繁，因此在类库中还给它定义了一个快捷别名：$。这是 jQuery 在全局命名空间中定义的唯一两个变量。

在 jQuery 类库中，最重要的方法是 `jQuery()` 方法，也就是 `$()`。它的功能很强大，有4中不同的调用方式：

1. 传递 CSS 选择器（字符串）给 `$()` 方法。当通过这种方式调用时，`$()` 方法返回当前文档中匹配该选择器的元素集。
2. 传递一个 Element、Document 或 Window 对象给 `$()` 方法。在这种情况下，`$()` 方法只须简单地将 Element、Document、Window 对象封装成 jQuery 对象并返回。
3. 传递 HTML 文本字符串给 `$()` 方法。在这种调用方式下 jQuery 会根据传入的文本创建好 HTML 元素并封装成 jQuery 对象返回。
4. 传入一个函数给 `$()` 方法。此时，文档加载完毕且 DOM 可操作时，传入的函数将被调用。
```javascript
jQuery(function () { // 文档加载完毕时调用
    // 所有 jQuery 代码放在这里
});
```

传递 CSS 选择器字符串给 `$()`，它返回的 jQuery 对象表示匹配的元素集。**jQuery 对象是类数组，它们拥有 `length` 属性和介于 0 - length - 1 之间的数值属性。这意味着可以使用标准的数组标识方括号来访问 jQuery 对象的内容。**如果不想把数组标识用在 jQuery 对象上，可以使用 `size()` 方法来替代 `length` 属性，用 `get()` 方法来替代方括号索引。可以使用 `toArray()` 方法将 jQuery 对象转化为真实数组。

除了 `length` 属性，jQuery 对象还有其它三个属性：`selector` 属性是创建 jQuery 对象时的选择器字符串。`context` 属性是上下文对象，是传递给 `$()` 方法的第二个参数，如果没有的话，默认是 Document 对象。`jquery` 属性值是 jQuery 版本号。

可以使用 `each(`) 方法来代替 for 循环遍历 jQuery 对象中的所有元素。`each()` 方法还会将索引值和该元素作为第一个和第二个参数传递给回调函数。**与 `forEach()` 不同，`each()` 方法中，如果回调函数在任一个元素傻瓜返回 false，遍历将在该元素后中止。**jQuery 方法通常隐式遍历匹配的元素并操作它们。

jQuery 的 `map()` 方法和 `Array.prototype.map()` 方法相近。它将接收回调函数作为参数，并为 jQuery 对象中的每一个元素都调用回调函数，同事将回调函数的返回值收集起来，并封装成一个新的 jQuery 对象返回。`map()` 调用回调函数的方式和 `each()` 方法相同：元素作为 this 值和第二个数传入，元素的索引值作为第一个参数传入。

jQuery 的另一个基础方法是 `index()`。该方法接收一个元素作为参数，返回值是该元素在 jQuery 对象中的索引值，如果找不到的话，返回 -1。如果传递一个 jQuery 对象作为参数，`index()` 方法会对该对象的第一个元素进行搜索。如果传入的是字符串，`index()` 方法会把它当成 CSS 选择器，并返回 jQuery 对象中匹配该选择器的一组元素中第一个元素的索引值。如果什么都不传入，`index()` 方法会返回该 jquery 对象中的第一个毗邻元素的索引值。

还有一个通用的 jQuery 方法是 `is()`。它接收一个选择器作为参数，如果选中元素至少由一个匹配该选择器时，则返回 true。可以在 `each()` 回调函数中使用它，例如：
```javascript
$('div').each(function () { // 对于每一个 <div> 元素
    if ($(this).is(':hidden')) return ; // 跳过隐藏元素
    // 对可见元素做点什么
});
```

# jQuery 的 getter 和 setter
jQuery 对象上最简单、最常见的操作是获取（get）或设置（set）HTML 属性、CSS 样式、元素内容和位置高宽的值。jQuery 中的 getter 和 setter：
* **jQuery 使用同一个方法即当 getter 用又当 setter 用，而不是定一对方法。**如果传入一个新值给该方法，则它设置此值；如果没有指定值，则它返回当前值。
* 用作 setter 时，这些方法会给 jQuery 对象中的每一个元素设置值，然后返回该 jQuery 对象以方便链式调用。
* 用作 getter 时，这些方法只会查询元素集中的第一个元素，返回单个值。如果需要遍历所有元素，请使用 `map()`。
* 用作 setter 时，这些方法经常接收对象参数。在这种情况下，该对象的每一个属性都指定一个需要设置的键/值对。
* 用作 setter 时，这些方法经常接收函数参数。在这种情况下，会调用该函数来计算需要设置的值。调用该函数传入的 this 值是对应的元素，第一个参数是该元素的索引值，第二个参数是该元素的当前值。

## 获取和设置 HTML 属性
`attr()` 方法是 jQuery 中用于 HTML 属性的 getter/setter。`attr()` 处理浏览器的兼容性和一些特殊情况，还可以让 HTML 属性名和 JavaScript 属性名可以等同使用，如 "for" - "htmlfor"、"class" - "className"。一个相关的函数是 `removeAttr()`。例子：
```javascript
$('form').attr('action'); // 获取第一个 form 元素的 action 属性
$('#icon').attr('src', 'icon.gif'); // 设置 src 属性
$('#banner').attr({src:'banner.gif', alt:'Adverisement', width:720, height:64});
$('a').attr('target', '_blank'); // 使所有链接在新窗口中打开
$('a').attr('target', function () {
    if (this.host == locaction.host) return '_self';
    else return '_blank';
}); // 非站内链接在新窗口中打开
$('a').attr({target:function () {...}}); // 可以像这样传入函数
$('a').removeAttr('target'); // 让所有连接在本窗口中打开
```

## 获取和设置 CSS 属性
`css()` 方法和 `attr()` 方法很类似，只是 `css()` 方法作用于元素的 CSS 样式，而不是元素的 HTML 属性。`css()` 返回元素的当前的样式，返回值可能来自 style 属性也可能来自样式表。`css()` 方法允许在 CSS 样式名中使用连字符或使用驼峰格式的 JavaScript 样式名。
```javascript
('h1').css('font-weight'); // 获取第一个 <h1> 的字体重量
('h1').css('fontWeight'); // 也可以采用驼峰格式
('h1').css('font'); // 错误：不可以获取复合样式
('h1').css('font-variant', 'smallcaps'); // 将样式设置在所有 <h1> 元素上
('div.note').css('board', 'solid black 2px'); // 设置复合样式
('h1').css({backgroundColor:'black', textColor:'white', fontVariant:'small-caps', padding:'10px 2px 4px 20px'. boarder:'dotted black 4px'});
('h1').css('font-size', function (i, curval) {
    return Math.round(1.25 * parseInt(vurval));
}); // 使所有的 <h1> 字体增加 25%
```

## 获取和设置 CSS 类
`addClass()` 和 `removeClass()` 用来从选中元素中添加和删除类。`toggleClass()` 在当元素还没有某些类时，给元素添加这些类，反之则删除。`hasClass()` 用来判断某类是否存在。例子：
```javascript
// 添加 CSS 类
$('h1').addClass('hilite');
$('h1+p').addClass('hilite first'); // 给 <h1> 后面的 <p> 添加两个类
$('section').addClass(function (n) { // 传递一个函数用以匹配
    return 'section' + n; // 每一个元素添加自定义类
});

// 删除 CSS 类
$('p').removeClass('hilite'); // 从所有 <p> 元素中删除一个类
$('p').removeClass('hilite first'); // 允许一次删除多个类
$('section ').removeClass(function (n) { // 从元素中删除自定义类
    return 'section' + n;
});
$('div').removeClass(); // 删除所有 <div> 中的所有类

// 切换 CSS 类
$('tr:odd').toggleClass('oddrow'); // 如果该类不存在，则添加。如果存在则删除
$('h1').toggleClass('big bold'); // 一次切换两个类
$('h1').toggleClass(function (n) { // 切换用来函数计算出来的类
    return 'big bold h1-' + n;
});
$('h1').toggleClass('hilite', true); // 类似 addClass
$('h1').toggleClass('hilite', false); // 类似 removeClass

// 检测 CSS 类
$('p').hasClass('first'); // 是否所有 p 元素都有该类
$('#lead').is('.first'); // 功能和上面类似
$('#lead').is('.first.hilite'); // is() 比 hasClass() 更灵活
```

## 获取和设置 HTML 表单值
`val()` 方法用来获取和设置 HTML 表单元素的 value 属性，**还可以用于获取和设置复选框、单选按钮以及 `<select>` 元素的选中状态。**
```javascript
$('#surname').val(); // 获取 surname 文本域的值
$('#usstate').val(); // 从 <select> 中获取单一值
$('select#extras').val(); // 从 <select multiple> 中获取一组值
$('input:radio[name=ship]:checked').val(); // 获取选中的单选按钮的值
$('#email').val('Invalid email address'); // 给文本域设置值
$('input:checkbox').val(['opt1', 'opt2']); // 选中带有这些名字或值的复选框
$('input:text').val(function () { // 重置所有文本域为默认值
    return this.defaultValue;
});
```

## 获取和设置元素的内容
`text()` 和 `html()` 方法用来获取和设置元素的纯文本或 HTML 内容。**当不带参数调用时，`text()` 返回所有匹配元素的子孙文本节点的纯文本内容，`html()` 返回第一个匹配元素的 HTML 内容。**如果传入字符串给 `text()` 或 `html()`，该字符串会用作该元素的纯文本或格式化 HTML 文本内容。
```javascript
var title = $('head title').text(); // 获取文档标题
var headline = $('h1').html(); // 获取第一个 <h1> 元素的 html
$('h1').text(function (n, current) { // 给每一个标题添加章节号
    return '§' + (n + 1) + ':' + current;
});
```

## 获取和设置元素的位置高宽
使用 `offset()` 方法可以获取或设置元素的位置。该方法相对文档来计算位置值，返回一个对象，带有 left 和 top 属性，代表 X 和 Y 坐标。

`position()` 方法很像 `offset()` 方法，但它只能用作 getter，返回的元素是相对于其便宜父元素的，而不是相对于文档的。

用于获取元素宽度和高度的 getter/setter 由 3 个。`width()` 和 `height()` 方法返回基本的宽度和高度，不包含内边距、边框、外边距。`innerWidth()` 和 `innerHeight()` 返回元素的宽度和高度，包含内边距的宽度和高度。`outerWidth()` 和 `outerHeight()` 通常返回的是包含元素内边距和边框的尺寸。

`scrollTop()` 和 `scrollLeft()` 可获取或设置元素的滚动条位置。这些方法可用在 Window 对象以及 Document 元素上。当用在 Document 对象上时，会获取或设置存放该 Document 的 Window 对象的滚动条位置。

## 获取和设置元素数据
jQuery 定义了一个名为 `data()` 的getter/setter 方法，可用来获取或设置与文档元素、Document、Window 对象相关的数据。

`removeData()` 方法用来从元素中删除数据。如果不带参数调用 `removeData()`，它会删除与元素相关联的所有数据。
```javascript
$('div').data('x', 1); // 设置一些数据
$('div.nodata').removeData('x'); // 删除一些数据
var x = $('#mydiv').data(x); // 获取一些数据
```

# 修改文档结构