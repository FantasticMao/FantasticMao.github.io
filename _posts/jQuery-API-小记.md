---
title: jQuery API 小记
date: 2017-04-21 15:44:01
categories: 开发语言
tags:
- JavaScript
- jQuery
---
摘自《JavaScript 权威指南》第 19 章 jQuery 类库。

JavaScript 的核心 API 设计的很简单，但由于不同浏览器之间的严重不兼容，导致客户端的 API 过于复杂。使用 JavaScript 框架或工具类库能简化通用操作，能隐藏浏览器之间的差异，这让很多程序员在开发 Web 应用时变得更简单。撰写本书时，最流行和广泛采用的类库之一就是 jQuery。<!-- more -->

# jQuery 基础
jQuery 类库定义了一个全局函数：`jQuery()`。该函数使用频繁，因此在类库中还给它定义了一个快捷别名：`$()`。这是 jQuery 在全局命名空间中定义的唯一两个变量。

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

可以使用 `each(`) 方法来代替 for 循环遍历 jQuery 对象中的所有元素。`each()` 方法还会将索引值和该元素作为第一个和第二个参数传递给回调函数。**与 `forEach()` 不同，`each()` 方法中，如果回调函数在任一个元素上返回 false，遍历将在该元素后中止。**jQuery 方法通常隐式遍历匹配的元素并操作它们。

jQuery 的 `map()` 方法和 `Array.prototype.map()` 方法相近。它将接收回调函数作为参数，并为 jQuery 对象中的每一个元素都调用回调函数，同时将回调函数的返回值收集起来，并封装成一个新的 jQuery 对象返回。`map()` 调用回调函数的方式和 `each()` 方法相同：元素作为 this 值和第二个数传入，元素的索引值作为第一个参数传入。

jQuery 的另一个基础方法是 `index()`。该方法接收一个元素作为参数，返回值是该元素在 jQuery 对象中的索引值，如果找不到的话，返回 -1。如果传递一个 jQuery 对象作为参数，`index()` 方法会对该对象的第一个元素进行搜索。如果传入的是字符串，`index()` 方法会把它当成 CSS 选择器，并返回 jQuery 对象中匹配该选择器的一组元素中第一个元素的索引值。如果什么都不传入，`index()` 方法会返回该 jquery 对象中的第一个毗邻元素的索引值。

还有一个通用的 jQuery 方法是 `is()`。它接收一个选择器作为参数，如果选中元素至少由一个匹配该选择器时，则返回 true。可以在 `each()` 回调函数中使用它，例如：
```javascript
$('div').each(function () { // 对于每一个 <div> 元素
    if ($(this).is(':hidden')) return ; // 跳过隐藏元素
    // 对可见元素做点什么
});
```

# getter 和 setter
jQuery 对象上最简单、最常见的操作是获取（get）或设置（set）HTML 属性、CSS 样式、元素内容和位置高宽的值。jQuery 中的 getter 和 setter：
* **jQuery 使用同一个方法即当 getter 用又当 setter 用，而不是定义对方法。**如果传入一个新值给该方法，则它设置此值；如果没有指定值，则它返回当前值。
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
`css()` 方法和 `attr()` 方法很类似，只是 `css()` 方法作用于元素的 CSS 样式，而不是元素的 HTML 属性。`css()` 返回元素的当前的样式，返回值可能来自 style 属性也可能来自样式表。`css()` 方法允许在 CSS 样式名中使用连字符或驼峰格式的 JavaScript 样式名。
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

`position()` 方法很像 `offset()` 方法，但它只能用作 getter，返回的元素是相对于其偏移父元素的，而不是相对于文档的。

用于获取元素宽度和高度的 getter/setter 由 3 个。`width()` 和 `height()` 方法返回基本的宽度和高度，不包含内边距、边框、外边距。`innerWidth()` 和 `innerHeight()` 返回元素的宽度和高度，包含内边距的宽度和高度。`outerWidth()` 和 `outerHeight()` 通常返回的是包含元素内边距和边框的尺寸。

`scrollTop()` 和 `scrollLeft()` 可获取或设置元素的滚动条位置。这些方法可用在 Window 对象以及 Document 元素上。当用在 Document 对象上时，会获取或设置存放该 Document 的 Window 对象的滚动条位置。

## 获取和设置元素数据
jQuery 定义了一个名为 `data()` 的 getter/setter 方法，可用来获取或设置与文档元素、Document、Window 对象相关的数据。

`removeData()` 方法用来从元素中删除数据。如果不带参数调用 `removeData()`，它会删除与元素相关联的所有数据。
```javascript
$('div').data('x', 1); // 设置一些数据
$('div.nodata').removeData('x'); // 删除一些数据
var x = $('#mydiv').data(x); // 获取一些数据
```

# 修改文档结构
HTML 文档表示为一棵节点树，而不是一个字符的线性序列，因此插入、删除、替换操作不会像操作字符串和数组一样简单。

下面演示的每一个方法都接收一个参数，用于指定需要插入文档中的内容。该参数可以是用于指定新内容的纯文本或 HTML 字符串，也可以是 jQuery 对象、元素或文本节点。
```javascript
$('#log').append('<br>' + message); // 在 #log元素结尾处添加内容
$('h1').prepend('§'); // 在每个 <h1> 的起始处添加章节标识
$('h1').before('<hr>'); // 在每个 <h1> 的前面添加水平线
$('h1').after('<hr>'); // 在每个 <h1> 的后面添加水平线
$('hr').replaceWith('<br>'); // 替换 <hr> 元素为 <br>
$('h2').each(function () { // 将 <h2> 替换为 <h1>，内容保持不变
    var h2 = $(this);
    h2.replaceWith('<h1>' + h2.html() + '</h1>');
});
```

`clone()` 创建并返回每一个选中的元素的一个副本，其返回的 jQuery 对象还不是文档的一部分，但可以使用上一节的方法将其插入文档中。

jQuery 定义了 3 种包装函数。`warp()` 包装每一个选中元素，`wrapInner()` 包装每一个选中元素的内容，`warpAll()` 将选中元素作为一组来包装。这些方法通常传入一个新创见的包装元素或用来创建元素的 HTML 字符串。

`empty()` 方法会删除每个选中元素的所有字节点，但不会修改元素自身。`remove()` 方法会从文档中移除选中元素，如果传入一个参数，该参数会被当成选择器，jQeury 对象中只有匹配该选择器的元素才会被移除。`remove()` 方法会移除所有事件处理程序以及可能绑定到被移除元素上的其它数据。`detach()` 和 `remove()` 方法类似，但不会移除事件处理程序和数据。

# 事件处理
jQuery 定义了一个统一事件 API，可兼容 IE（IE9 以下），并在所有浏览器中工作。jQuery API 具有简单的形式，比标准或 IE 的事件 API 更容易使用。jQuery API 还具有更复杂、功能更齐全的形式，比标准 API 更强大。

## 事件处理程序的简单注册
待续

## jQuery 事件处理程序
jQuery 调用每一个事件处理程序时，都传入了一个或多个参数，并且对处理程序的返回值进行了处理。需要知道的最重要的一件事情是，每个事件处理程序都传入了一个 [jQuery 事件对象](#jQuery-事件对象) 作为第一个参数。该对象的字段提供了与该事件相关的详细信息（比如鼠标指针的坐标）。jQuery 模拟标准 Event 对象，即便在不支持的标准事件对象的浏览器中（像 IE8 及其以下），jQuery 事件对象在所有浏览器上拥有一组相同的字段。

jQuery 事件处理程序函数的返回值始终是有意义的。如果处理程序返回 false，与该事件相关联的默认行为，以及该事件接下来的冒泡都会被取消。也就是说，返回 false 等同于调用 Event 对象的 `preventDefault()` 和 `stopPropagation()` 方法。同样，当事件处理程序返回一个值（非 undefined 值）时，jQuery 会将该值存储在 Event 对象的 result 属性中，该属性可以被后续调用的事件处理程序访问。

## jQuery 事件对象
待续

## 事件处理程序的高级注册
待续

## 注销事件处理程序

## 触发事件
待续

## 实时事件
待续

# 动画效果
jQuery 定义了 `fadeIn()` 和 `fadeOut()` 等简单的方法实现常见视觉效果。除了简单动画方法，jQuery 还定义了一个 `animate()` 方法，用来实现更复杂的自定义动画。

**jQuery 动画是异步的。**调用 `fadeIn()` 等动画方法时，它会立刻返回，动画则在「后台」执行。如果一个元素已经在动画过程中，再调用一个动画方法时，新动画是不会立刻执行，而会延迟到当前动画结束后才执行。例如，可以让一个元素在持久显示前先闪烁一会：
```javascript
$('#blinker').fadeIn(100).fadeOut(100).fadeIn(100).fadeOut(100).fadeIn(100);
```

## 简单动画
* `fadeIn()`、`fadeOut()`、`fadeTo()`
这是最简单的动画：`fadeIn()` 和 `fadeOut()` 简单地改变 CSS 的 opacity 属性显示或隐藏元素。两者都接受可选的时长和回调参数。`fadeTo()` 稍有不同，它需要传入一个 opacity 目标值，`fadeTo()` 会将元素的当前 opacity 值变化到目标之。调用 `fadeTo()` 方法时，第一参数必须是时长或选项对象，第二参数是 opacity 目标值，第三参数是回调函数。
* `show()`、`hide()`、`toggle()`
`hide()` 和 `show()` 方法只是简单地立刻隐藏或显示选中元素，带有时长或选项对象时，它们会隐藏或显示有个动画过程。`toggle()` 可以改变在上面调用它的元素的可视状态：如果隐藏，则调用 `show()`，如果显示，则调用 `hide()`。
* `slideDown()`、`slideUp()`、`slideToggle()`
`slideUp()` 会隐藏 jQuery 对象中的元素，方式是将其高度动态变化到 0 ，然后设置 CSS 的 display 属性为 none。`slideDown()` 执行反响操作，来使得隐藏的元素再次可见。`slideToggle()` 使用向上滑动或向下滑动动画来切换元素的可见性。

## 自定义动画
`animate()` 方法可以实现更多通用的动画效果。第一个参数是必需的，它必须是一个对象，该对象的属性指定要变化的 CSS 属性和它们的目标值。第二个参数是可选的，可以传入一个选项对象给 `animate()` 方法。通常，`animate()` 方法接收两个对象参数，第一个指定动画内容，第二个指定如何定制动画。

1. 动画属性对象
该对象的属性名必须是 CSS 属性，这些属性的值必须是动画的目标值。动画只支持数值属性：对于颜色、字体或 display 等枚举属性是无法实现动画效果的。如果属性值是数值，则默认单位是像素。如果属性值是字符串，可以指定单位。还可以指定相对值，用「+=」前缀表示增加，用「-=」前缀表示减少。jQuery 动画属性对象中，还可以使用 hide、show、toggle 这三个属性值。
```javascript
$('p').animate({
    'margin-left': '+=.5in', // 增加段落缩进
    opacity: '-=.1' // 同时减少不透明度
});
$('img').animate({
    width: 'hide',
    borderLeft: 'hide',
    borderRight: 'hide',
    paddingLeft: 'hide',
    paddingRight: 'hide'
});
```
2. 动画选项对象
该选项对象用来指定动画如何执行。duration 属性指定动画持续的毫秒时间，其值可以是 fast、slow 或任何 jQuery.fx.speeds 中定义的名称。complete 属性指定动画完成时的回调函数。setp 属性指定在动画每一步或每一帧调用的回调函数。在回调函数中，this 指向正在连续变化的元素，第一个参数则是正在变化的属性值。queue 属性指定动画是否需要队列化，即是否需要等到所有尚未发生的动画都完成后再执行该动画。默认情况下，所有动画都是队列化的。easing 属性指定缓动函数名，jQuery 默认使用的是命为 swing 的正弦函数。

# Ajax
待续

# 选择器
待续