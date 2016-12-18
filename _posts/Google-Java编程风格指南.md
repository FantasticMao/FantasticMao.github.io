---
title: Google Java编程风格指南
date: 2016-12-08 23:04:45
categories: Java
---
转载并翻译自 [https://google.github.io/styleguide/javaguide.html](https://google.github.io/styleguide/javaguide.html) 。个人英语水平有限，应以原文档为标准......<!-- more -->

## [简介](#简介)
本文档是Google Java编程规范的完整定义。一个Java源文件当且仅当遵守本规范时，才可被称为Google风格。

与其他编程规范类似，本文档讨论的不仅是代码对齐的美观问题，同时也包含其他类型约定和编码标准。然而，本文档侧重于我们普遍遵循的准则，也避免建议那些还存在争议的规则。

### 术语说明
在本文档中，除非另有说明：

1. *class*类 可表示一个*ordinary class*普通的类、* enum class*枚举类、*interface*接口或*annotation*注解类型。
2. *member*成 员可表示一个*nested class*嵌套类、*field*属性、*method*方法或*constructor*者构造方法，即除初始化和注释，类的所有最顶层内容。
3. *comment*注释 只表示*implementation comments*实现注释（/\* \*/）。我们不使用*documentation comments*文档注释，而是*javadoc*。

其他出现在本文档中的术语将另作说明。

### 指南说明
本文档中的示例代不是完全规范的。也就是说，虽然示例代码是属于Google风格，但并不意味着这是实现代码的唯一优雅方式。示例中代码的风格不应被作为执行的准则。

---

## [源文件准则](#源文件准则)

### 文件名
源文件名由其顶级类的类名，区分大小写，再加上`.java`扩展名组成。

### 文件编码：UTF-8
源文件使用**UTF-8**编码。

### 特殊字符
1. 空格字符
	除换行符，**ASCII水平空格字符（0x20）**是源文件中唯一允许出现的空格字符，这意味着：
	1. 所有其它字符串和字符文字中的空格字符都要进行转义。
	2. 不允许使用制表符缩进。
2. 特殊转义字符
	对任何需转义表示（`\b`、`\t`、`\n`、`\f`、`\r`、`\"`、`\'`、`\\`）的字符，推荐使用转义字符，而非对应八进制（例如`\012`）或者Unicode转义（例如`\u000a`）。
3. 非ASCII字符
	对剩余的非ASCII字符，取决于**更容易阅读和理解**的方式，选择Unicode字符或等价的Unicode转义。强烈反对在字符串和注释之外使用Unicode转义。
	
	> 在Unicode转义或使用实际的Unicode字符时，添加注释是非常友好的。
	
	例如：
	<table><thead><tr><th>Example</th><th>Discussion</th></tr></thead><tbody><tr><td>String unitAbbrev = "μs";</td><td>最好：没有注释也十分清晰</td></tr><tr><td>String unitAbbrev = "\u03bcs"; // "μs"</td><td>允许：但没理由这么做</td></tr><tr><td>String unitAbbrev = "\u03bcs"; // Greek letter mu, "s"</td><td>允许：但比较笨拙和易出错</td></tr><tr><td>String unitAbbrev = "\u03bcs";</td><td>最差：可读性太差</td></tr><tr><td>return '\ufeff' + content; // byte order mark</td><td>一般：对非打印字符使用转义，和必要的注释</td></tr></tbody></table>

	> 不要因为一些程序可能不能正确地处理非ASCII字符，而使你的代码可读性变差。如果那真的发生了，程序会直接报错，并需要被修复。（与你代码的可读性无关）
---

## [源文件结构](#源文件结构)
一个Java源（代码）文件包含以下几点：

1. License或Copyright，可选的
2. package语句
3. import语句
4. class声明，每个源文件仅能有一个顶级类

以上部分之间，只间隔一个空行

### 许可证或版权信息
如果文件中包含许可证和版权信息，应当至于此处。

### package语句
package语句不允许换行。单行字符限制（4.4单行字符限制章节）不适用于package语句。

### import语句
1. 不允许通配符
	不允许使用静态或其它方式的通配符导入。
2. 不允许换行
	import语句不允许换行。单行字符限制（4.4单行字符限制章节）不适用于import语句。
3. 顺序和间隔
	import语句应按如下顺序分组：<br><ol><li>所有静态导入归一组。</li><li>所有非静态导入归一组。</li></ol>如果同时存在静态导入和非静态导入，则应使用空行分隔它们，且在这两组导入语句中，不允许存在其它空行。<br>每组中import语句按ASCII码排序。（**注意**：因`.`排在`;`之前，所以这与纯ASCII码排序略有不同）
4. 不允许类的静态导入
	静态嵌套类以其常规方式导入，而不是使用静态导入。

### Class定义
1. 有且仅有一个顶级类的声明
	每个源文件仅能有一个顶级类
2. 类内容顺序
	类的成员或者初始化方法顺序对代码可读性有着很重要的影响。然而对此却没有一个统一正确的标准。不同的类可能有不同的排序方式。
	重要的是，每个类都应遵从维护者可解释清楚的**逻辑顺序**排序。例如，新方法可能不是简单地放置在类的最后，因为如此产生的“按时间顺序添加”不遵从逻辑顺序。
	* 方法重载：不应被分离
	当一个类拥有同名的多个构造器或者方法时，这些构造器或者方法不应被其他代码所分隔，甚至是私有方法。
	
---

## [格式](#格式)
术语说明：*block-like construct*块状结构 指类的实体、成员或构造器。注意，在后续4.8.3.1数组章节中，任何数组的初始化可被认为是一个块状结构。

### 花括号
1. 花括号在被需要的地方使用
	花括号在`if`、`else`、`for`、`do`、`while`语句使用，即使他们的实体是空的或者仅包含一条语句。
2. 非空语句块：K&R风格
	对于非空语句块和块状结构，花括号遵循K&R风格：<ul><li>左花括号之前不能换行。</li><li>左花括号之后换行。</li><li>右花括号之前换行。</li><li>只有右花括号结束了一条语句、一个方法实体、构造器或者命名的类时，右花括号之后才换行。例如`else`或`,`之后的花括号不允许换行。</li></ul>
	例如：
	```java
	return () -> {
	  while (condition()) {
	    method();
	  }
	};

	return new MyClass() {
	  @Override public void method() {
	    if (condition()) {
	      try {
		something();
	      } catch (ProblemException e) {
		recover();
	      }
	    } else if (otherCondition()) {
	      somethingElse();
	    } else {
	      lastThing();
	    }
	  }
	};
	```
	一些特殊情况，将在4.8.1枚举类章节说明。
3. 空语句块：可以简洁
	一个空语句块或块状结构可以遵从K&R风格。或者，可以在左花括号起始之后，没有任何字符和换行，立即右花括号结束。除非它是*multi-block statement*多块语句（一个包含多块的语句，如：`if/else`、`try/catch/finally`）的一部分，否则不允许这样做。
	例如：
	```java
	// This is acceptable
	void doNothing() {}

	// This is equally acceptable
	void doNothingElse() {
	}
	```
	```java
	// This is not acceptable: No concise empty blocks in a multi-block statement
	try {
	  doSomething();
	} catch (Exception e) {}
	```

### 块缩进：+2个空格
每次新写一块或块状结构的代码时，缩进增加两个空格。当语句块结束时，缩进返回至上一级别。块缩进规则适用于整个代码块和注释。

### 一个语句占一行
每个语句后面都有换行符。

### 单行字符限制：100
Java代码每行限制100个字符。除非另有说明，任何超过此限制的都必须被换行。在4.5换行章节中会有具体的解释。
特殊情况：
* 遵循行限制规则无法实现的地方。（例如Javadoc中的URL、JSNI中的方法引用）
* package语句和import语句。（详情请看3.2package语句章节和3.3import语句章节）
* 注释中可能直接被复制粘贴执行的shell命令。

### 换行
术语说明：将可以合法占据一行的代码被拆分成多行，这种行为称作 *line-wrapping*换行。

这儿并没有全面的、明确的规范适用于所有场景下的换行。对于同一行代码，通常有多种有效的方法。

> 注意：换行的典型原因，是为了避免代码超出了单行字符的限制。即使一行代码可以被修正至符合单行字符限制，也可能会由作者自行决定而换行。

> 小记：不换行的情况下，额外的方法或者局部变量可能也可以解决问题。

### 空格

### 分组括号：推荐

### 特殊结构
---

## [命名](#命名)

### 标识符通用规则

### 标识符类型规则

### 骆驼峰式命名
---

## [编程实战](#编程实战)

### @Override：尽量使用

### exception捕获：不能忽视

### static成员：规范使用

### finalize：禁用
---

## [Javadoc](#Javadoc)

### 格式

### 摘要片段

### 尽量使用Javadoc
---
