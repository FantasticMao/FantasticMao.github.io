---
title: Google Java编程风格指南
date: 2016-12-08 23:04:45
categories: Java
---
转载并翻译自 [https://google.github.io/styleguide/javaguide.html](https://google.github.io/styleguide/javaguide.html) 。个人英语水平有限，应以原文档为标准......<!-- more -->

## 简介
本文档是Google Java编程规范的**完整**定义。一个Java源文件当且仅当遵守本规范时，才可被称为Google风格。

与其他编程规范类似，本文档讨论的不仅是代码对齐的美观问题，同时也包含其他类型约定和编码标准。然而，本文档侧重于我们普遍遵循的准则，也避免建议那些还**存在争议的规则**。

### 术语说明
在本文档中，除非另有说明：

1. *class*类 可表示一个*ordinary class*普通的类、* enum class*枚举类、*interface*接口或*annotation*注解类型。
2. *member*成员 可表示一个*nested class*嵌套类、*field*属性、*method*方法或*constructor*者构造方法，即除初始化方法和注释，类的所有最顶层内容。
3. *comment*注释 只表示*implementation comments*实现注释（/\* \*/）。我们不使用*documentation comments*文档注释，而是*javadoc*。

其他出现在本文档中的术语将另作说明。

### 指南说明
本文档中的示例代不是完全规范的。也就是说，虽然示例代码是属于Google风格，但并不意味着这是实现代码的唯一优雅方式。示例中代码的风格不应被作为执行的准则。

---

## 源文件准则

### 文件名
源文件名由其[唯一的](#有且仅有一个顶级类的声明)顶级类的类名，区分大小写，再加上`.java`扩展名组成。

### 文件编码：UTF-8
源文件使用**UTF-8**编码。

### 特殊字符

#### 空格字符
除换行符，**ASCII水平空格字符（0x20）**是源文件中唯一允许出现的空格字符，这意味着：
1. 所有其它字符串和字符文字中的空格字符都要进行转义。
2. 不允许使用制表符缩进。

#### 特殊转义字符
对任何需转义表示（`\b`、`\t`、`\n`、`\f`、`\r`、`\"`、`\'`、`\\`）的[特殊字符](http://docs.oracle.com/javase/tutorial/java/data/characters.html)，推荐使用转义字符，而非对应八进制（例如`\012`）或者Unicode转义（例如`\u000a`）。

#### 非ASCII字符
对剩余的非ASCII字符，取决于**更容易阅读和理解**的方式，选择Unicode字符或等价的Unicode转义。强烈反对在字符串和注释之外使用Unicode转义。
	
> **小记**：在Unicode转义或使用实际的Unicode字符时，添加注释是非常友好的。
	
例如：

Example | Discussion
--- | ---
String unitAbbrev = "μs"; | 最好：没有注释也十分清晰
String unitAbbrev = "\u03bcs"; // "μs" | 允许：但没理由这么做
String unitAbbrev = "\u03bcs"; // Greek letter mu, "s" | 允许：但比较笨拙和易出错
String unitAbbrev = "\u03bcs"; | 最差：可读性太差
return '\ufeff' + content; // byte order mark | 一般：对非打印字符使用转义，和必要的注释

> **小记**：不要因为一些程序可能不能正确地处理非ASCII字符，而使你的代码可读性变差。如果那真的发生了，程序会直接报错，并需要被修复。（与你代码的可读性无关）

---

## 源文件结构
一个Java源文件由以下**顺序**组成：

1. License或Copyright，可选的
2. package语句
3. import语句
4. class声明，每个源文件仅能有一个顶级类

以上部分之间，间隔**一个空行**。

### 许可证或版权信息
如果文件中包含许可证和版权信息，应当至于此处。

### package语句
package语句不允许换行。单行字符限制（4.4[单行字符限制](#单行字符限制：100)章节）不适用于package语句。

### import语句

#### 不允许通配符
**不允许**使用静态或其它方式的**通配符导入**。

#### 不允许换行
import语句**不允许**换行。单行字符限制（4.4[单行字符限制](#单行字符限制：100)章节）不适用于import语句。

#### 顺序和间隔
import语句应按如下顺序分组：

1. 所有静态导入归一组。
2. 所有非静态导入归一组。

如果同时存在静态导入和非静态导入，则应使用空行分隔它们，且在这两组导入语句中，不允许存在其它空行。

每组中import语句按ASCII码排序。（**注意**：因`.`排在`;`之前，所以这与纯ASCII码排序略有不同）

#### 不允许类的静态导入
静态嵌套类以其常规方式导入，而不是使用静态导入。

### Class定义

#### 有且仅有一个顶级类的声明
每个源文件仅能有一个顶级类

#### 类内容顺序
类的成员或者初始化方法顺序对代码可读性有着很重要的影响，然而对此却没有一个统一正确的标准。不同的类可能有不同的排序方式。
重要的是，每个类都应遵从维护者可解释清楚的**逻辑顺序**排序。例如，新方法可能不是简单地放置在类的最后，因为如此产生的“按时间顺序添加”不遵从逻辑顺序。

##### 方法重载：不应被分离
当一个类拥有同名的多个构造器或者方法时，这些构造器或者方法不应被其他代码所分隔，甚至是私有方法。
	
---

## 格式
**术语说明**：*block-like construct*块状结构 指类的实体、成员或构造器。注意，在后续4.8.3.1[数组](#数组)章节中，任何数组的初始化可被认为是一个块状结构。

### 花括号

#### 花括号在被需要的地方使用
在`if`、`else`、`for`、`do`、`while`语句使用花括号，即使他们的实体是空的或者仅包含一条语句。

#### 非空语句块：K&R风格
对于非空语句块和块状结构，花括号遵循Kernighan & Ritchie风格（[Egyptian brackets](http://www.codinghorror.com/blog/2012/07/new-programming-jargon.html)）：
* 左花括号之前不能换行。
* 左花括号之后换行。
* 右花括号之前换行。
* 只有右花括号结束了一条语句、一个方法实体、构造器或者命名的类时，右花括号之后才换行。例如`else`或`,`之后的花括号不允许换行。

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
一些特殊情况，将在4.8.1[枚举类](#枚举类)章节说明。

#### 空语句块：可以简洁
一个空语句块或块状结构可以遵从K&R风格（如[4.1.2章节](#非空语句块：K&R风格)描述）。或者，可以在左花括号起始之后，没有任何字符和换行，立即右花括号结束。除非它是*multi-block statement*多块语句（一个包含多块的语句，如：`if/else`、`try/catch/finally`）的一部分，否则不允许这样做。

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
每次新写一块或块状结构的代码时，缩进增加2个空格。当语句块结束时，缩进返回至上一级别。块缩进规则适用于整个代码块和注释。（请看4.1.2[非空语句块：K&R风格](#非空语句块：K&R风格)章节的例子）

### 一个语句占一行
每个语句后面都有换行符。

### 单行字符限制：100
Java代码每行限制100个字符。除非另有说明，任何超过此限制的都必须被换行。在4.5[换行](#换行)章节中会有具体的解释。

特殊情况：
* 遵循行限制规则无法实现的地方。（例如Javadoc中的URL、JSNI中的方法引用）
* package语句和import语句。（详情请看3.2[package语句](#package语句)章节和3.3[import语句](#import语句)章节）
* 注释中可能直接被复制粘贴执行的shell命令。

### 换行
**术语说明**：将可以合法占据一行的代码被拆分成多行，这种行为称作 *line-wrapping*换行。

这儿并没有全面的、明确的规范适用于所有场景下的换行。对于同一行代码，通常有多种有效的方法。

> **注意**：换行的典型原因，是为了避免代码超出了单行字符的限制。即使一行代码可以被修正至符合单行字符限制，也可能会由作者的决定而换行。

> **小记**：不换行的情况下，额外的方法或者局部变量可能也可以解决问题。

#### 在何处换行
换行的主要原则是：倾向于在**更高语法级别**处换行。同时：
1. 在 *non-assignment*非赋值运算符之前换行。（注意这与Google风格的其它语言不同，如C++和JavaScript。）
	* 这也适用于以下“类似操作符”的符号：
		* 点分隔符（`.`）
		* 方法引用中的两个冒号（`::`）
		* 类型约束中的&符（`T <extends Foo Foo & Bar>`）
		* 异常捕获中的|符（`catch (FooException | BarException e)`）。
2. 在 *assignment*赋值运算符之后换行，但在之前换行也可以接受。
	* 这也适用于foreach语句中“类似赋值操作符”的冒号。
3. 方法或者构造器名与紧随它的开括号（`(`）相连。
4. 逗号（`,`）紧随它之前的内容。
5. 存在lambda箭头的一行永不换行，除非lambda主体仅有单个表达式，且能在lambda箭头后立即出现的情况下。例如：
	```java
	MyLambda<String, Long, Object> lambda =
	    (String label, Long value, Object obj) -> {
		...
	    };

	Predicate<String> predicate = str ->
	    longExpressionInvolving(str);
	```

>  **注意**：换行的主要目的是为具有清晰的代码，不一定每行代码越少越合适。

#### 换行缩进至少4个空格
换行时，第一行之后的每一行至少比第一行多缩进4个空格。

连续换行时，缩进可能超过4个空格。一般来说，当且仅当两行以同一级别的语法元素开头时，它们才会持有相同水平的缩进。

4.6.3[水平对齐](#水平对齐)章节介绍不鼓励使用可变数目的空格来对齐上一行的符号。

### 空格

#### 垂直空格
以下情况需使用单个空行：

1. 类中连续的成员或初始化方法之间：字段、构造器、方法、嵌套类、静态初始化和实例初始化。
	* **特殊情况**:两个连续字段（无其它代码）之间的空行是可选的。比如可以使用空行去创建字段间的 *logical groupings*逻辑分组
	* **特殊情况**:枚举常量之间的空行在4.8.1[枚举类](#枚举类)章节介绍。
2. 逻辑上分组代码的语句之间。
3. 类中第一个成员或初始化方法之前，或者最后一个成员或始化方法之后。（不鼓励也不反对）
4. 本文档其他章节中所要求的（如3[源文件结构](#源文件结构)章节和3.3[import语句](#import语句)章节）

多个连续的空行是允许的，但这是不必要也不鼓励的。

#### 水平空格
除语言、其它规则要求、语法分隔、注释和Javadoc的需要之外，单个ASCII空格**仅**出现在以下位置：

1. 任何保留关键字和它之后的开阔号`(`之间，如`if`、`for`、`catch`。
2. 任何保留关键字和它之前的右花括号`}`之间，如`else`、`catch`。
3. 任何左花括号`{`之前。除了：
	* `@SomeAnnotation({a, b})` 不使用空格
	* <code>String[][] x = &#123;&#123;&quot;foo&quot;&#125;&#125;;</code> <code>&#123;&#123;</code>之间没有空格
4. 二进制或三元操作符的两侧。这也适用于以下“类似操作符”的符号：
	* 类型约束中的&符：`<T extends Foo & Bar>`
	* 多异常捕获中的|符：`catch (FooException | BarException e)`
	* 增强版`for`语句foreach中的`:`
	* lambda表达式中的箭头：`(String str) -> str.length()`
	但不是：
	* 写法类似`Object::toString`方法引用中的`::`
	* 写法类似`Object.toString()`点分隔符中的`.`
5. `,:;`或者闭括号`)`之后。
6. 行尾的注释`//`的两侧。此处多个空格也是允许的，但不是必须的。
7. 类型和变量的定义之间：`List<String> list`。
8. 数组初始化时，花括号的两侧。这是可选的。
	* `new int[] {5, 6}`和`new int[] { 5, 6 }`都是可行的

本规则不影响行开始或结束时的空格，只针对行内的空格。

#### 水平对齐
**术语说明**：*Horizontal alignment*水平对齐 指为对齐本行与上一行的某些符号，而添加额外可变数目空格的做法。

这种做法是允许的，但在Google风格中不是必须的。甚至不需要在已经对齐的地方保持水平对齐。

这是一个先无对齐后有的例子：
```java
private int x; // this is fine
private Color color; // this too

private int   x;      // permitted, but future edits
private Color color;  // may leave it unaligned
```

> 水平对齐有助阅读代码，但难以日后维护。已经考虑日后仅需调整一行代码的可能性，却依旧能保持美观对齐的做法，是**允许**的。但更频繁的情况是编辑器（或者你自己）会提示你调整附近行中的空格。而这一行为可能会触发级联式的格式化，从而导致了“大爆炸”。在最坏的情况下会导致大量无意义的工作，最好的情况下依然会影响版本控制、减慢代码评审速度、引起代码合并冲突。

### 分组小括号：推荐使用
只有当开发人员和评审人员都同意没有小括号也不会误解代码原意，且不会影响代码可读性的情况下，才可以省略分组小括号。假设所有Java开发者都熟知运算符的优先级是不合理的。

### 特殊结构

#### 枚举类
枚举常量的逗号之后，是可换行的。额外的空行（通常只有一个）也是允许的。下面是举例了一种可能性：
```java
private enum Answer {
  YES {
    @Override public String toString() {
      return "yes";
    }
  },

  NO,
  MAYBE
}
```
枚举类没有方法和注释的话，其常量的初始化可以写成数组格式。
```java
private enum Suit { CLUBS, HEARTS, SPADES, DIAMONDS }
```
枚举类也是类，所以对类的所有规则都适用枚举类。

#### 变量声明

##### 每次仅声明一个变量
每个变量声明（不论字段或局部变量）只声明一个变量：如变量声明`int a, b;`是不允许的。

##### 当被需要时声明
局部变量一般不在它们所属的代码块或者块状结构的起始位置声明。相反，在第一次使用局部变量时（合理情况下），以最小作用域声明。局部变量声明通常具有初始值，或应在声明之后立即初始化。

#### 数组

##### 数组初始化：可以写成块状结构
数组可以像块状结构一样初始化。例如，下面做法都是合法的（不全）：
```java
new int[] {           new int[] {
  0, 1, 2, 3            0,
}                       1,
                        2,
new int[] {             3,
  0, 1,               }
  2, 3
}                     new int[]
                          {0, 1, 2, 3}
```

##### 拒绝C语言风格的声明
中括号是类型而非变量的一部分：`String[] args`合法，`String args[]`非法。

#### switch语句
**术语说明**：

##### 缩进
与其它所有块结构一样，switch语句块中的内容缩进2个空格。

开始switch标签之后应换行并增加2个缩进级，如语句块刚开始一样。结束switch标签之后应返回上级缩进，如语句块结束一样。

##### 下降执行的注释
在switch语句中，每个语句组都应突然终止（使用`break`、`continue`、`return`、或者抛出异常），或者以注释形式标记指出此处可能会执行到下一个语句组。能传达下降执行意思的所有注释都是可行的（如典型的`// fall through`）。在最后一个分组中，这个特殊的注释不是必须的。例如：
```java
switch (input) {
  case 1:
  case 2:
    prepareOneOrTwo();
    // fall through
  case 3:
    handleOneTwoOrThree();
    break;
  default:
    handleLargeNumber(input);
}
```
注意`case 1:`之后没有注释，只有在语句组的结尾时才可这样。

##### `default`语句组是必须的
每个switch语句都必须包含`default`语句组，即使它不包含任何代码。

#### 注解
注解适用于紧随Javadoc之后的类、方法和构造器，且每个注解占有一行。这些行不构成换行（4.5[换行](#换行)章节），所以无需增加缩进。例如：
```java
@Override
@Nullable
public String getNameIfPresent() { ... }
```
特殊情况：单个无参数的注解可以和行首个修饰附放在一起，例如：
```java
@Override public int hashCode() { ... }
```
注释也适用于紧随Javadoc之后的字段，且这种情况下，多个注释（允许有参数）可以写在同一行。例如：
```java
@Partial @Mock DataLoader loader;
```
注解、局部变量或者类型没有具体的格式化要求。

#### 注释
本章节介绍 *implementation comments*实现注释。Javadoc在7[Javadoc](#Javadoc)章节单独介绍。

换行符之前可以有任意空格，然后是实现注释。这样的注释应使用非空行。

##### 注释块风格
注释块与它关联的代码缩进相同。可以是`/* ... */`风格或`// ...`风格。对于多行的`/* ... */`注释，每行必须以`*`开始且与上一行的`*`对齐。
```java
/*
 * This is          // And so           /* Or you can
 * okay.            // is this.          * even do this. */
 */
```
注释不要被包围在星号或者其它字符绘制的框中。

> 当写多行注释的时候，如果你想在必要时能自动对齐代码，那么应使用`/* ... */`。绝大多数编辑器不会自动对齐`// ...`风格的注释。

#### 修饰符
当类和成员存在修饰符时，应以Java语言规范建议的顺序出现：
`public protected private abstract default static final transient volatile synchronized native strictfp`

#### 字面量数值
`long`长整型字面量数值带有`L`后缀，且永远不为小写（为了避免混淆数字`1`）。例如`3000000000L`而不是`3000000000l`。

---

## 命名

### 标识符通用规则

### 标识符类型规则

### 骆驼峰式命名
---

## 编程实战

### @Override：尽量使用

### exception捕获：不能忽视

### static成员：规范使用

### finalize：禁用
---

## Javadoc

### 格式

### 摘要片段

### 尽量使用Javadoc
---
