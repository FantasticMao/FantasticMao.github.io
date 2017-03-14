---
title: Google Java编程风格指南
date: 2016-12-08 23:04:45
categories: Java
---
转载并翻译自 [https://google.github.io/styleguide/javaguide.html](https://google.github.io/styleguide/javaguide.html)。个人英语水平有限，应以原文档为标准，以原文档为标准，以原文档为标准。<!-- more -->

## 简介
本文档是 Google Java 编程规范的 **完整** 定义。一个 Java 源文件当且仅当遵守本规范时，才可被称为 Google 风格。

与其它编程规范类似，本文档讨论的不仅是代码对齐的美观问题，同时也包含其它类型约定和编码标准。然而，本文档侧重于我们普遍遵循的准则，也避免建议那些还 **存在争议的规则**。

### 术语说明
在本文档中，除非另有说明：

1. *class* 类 可表示一个 *ordinary class* 普通的类、*enum class* 枚举类、*interface* 接口或 *annotation* 注解类型。
2. *member* 成员 可表示一个 *nested class* 嵌套类、*field* 属性、*method* 方法或 *constructor* 者构造方法，即除初始化方法和注释，类的所有最顶层内容。
3. *comment* 注释 只表示 *implementation comments* 实现注释（/\* \*/）。我们不使用 *documentation comments* 文档注释，而是 *javadoc*。

其它出现在本文档中的术语将另作说明。

### 指南说明
本文档中的示例代不是完全规范的。也就是说，虽然示例代码是属于 Google 风格，但并不意味着这是实现代码的唯一优雅方式。示例中代码的风格不应被作为执行的准则。

---

## 源文件准则

### 文件名
源文件名由其 [唯一的](#有且仅有一个顶级类的声明) 顶级类的类名，区分大小写，再加上 `.java` 扩展名组成。

### 文件编码：UTF-8
源文件使用 **UTF-8** 编码。

### 特殊字符

#### 空格字符
除换行符，**ASCII水平空格字符（0x20）** 是源文件中唯一允许出现的空格字符，这意味着：
1. 所有其它字符串和字符文字中的空格字符都要进行转义。
2. 不允许使用制表符缩进。

#### 特殊转义字符
对任何需转义表示（`\b`、`\t`、`\n`、`\f`、`\r`、`\"`、`\'`、`\\`）的 [特殊字符](http://docs.oracle.com/javase/tutorial/java/data/characters.html)，推荐使用转义字符，而非对应八进制（例如`\012`）或者 Unicode 转义（例如`\u000a`）。

#### 非ASCII字符
对剩余的非 ASCII 字符，取决于 **更容易阅读和理解** 的方式，选择 Unicode 字符或等价的 Unicode 转义。强烈反对在字符串和注释之外使用 Unicode 转义。
	
> **小记**：在 Unicode 转义或使用实际的 Unicode 字符时，添加注释是非常友好的。
	
例如：

Example | Discussion
--- | ---
String unitAbbrev = "μs"; | 最好：没有注释也十分清晰
String unitAbbrev = "\u03bcs"; // "μs" | 允许：但没理由这么做
String unitAbbrev = "\u03bcs"; // Greek letter mu, "s" | 允许：但比较笨拙和易出错
String unitAbbrev = "\u03bcs"; | 最差：可读性太差
return '\ufeff' + content; // byte order mark | 一般：对非打印字符使用转义，和必要的注释

> **小记**：不要因为一些程序可能不能正确地处理非 ASCII 字符，而使你的代码可读性变差。如果那真的发生了，程序会直接报错，并需要被修复。（与你代码的可读性无关）

---

## 源文件结构
一个 Java 源文件由以下 **顺序** 组成：

1. License 或 Copyright，可选的
2. package 语句
3. import 语句
4. class 声明，每个源文件仅能有一个顶级类

以上部分之间，间隔 **一个空行**。

### 许可证或版权信息
如果文件中包含许可证和版权信息，应当至于此处。

### package语句
package 语句不允许换行。单行字符限制（ 4.4 [单行字符限制](#单行字符限制：100) 章节）不适用于 package 语句。

### import语句

#### 不允许通配符
**不允许** 使用静态或其它方式的 **通配符导入**。

#### 不允许换行
import 语句 **不允许** 换行。单行字符限制（ 4.4 [单行字符限制](#单行字符限制：100) 章节）不适用于 import 语句。

#### 顺序和间隔
import 语句应按如下顺序分组：

1. 所有静态导入归一组。
2. 所有非静态导入归一组。

如果同时存在静态导入和非静态导入，则应使用空行分隔它们，且在这两组导入语句中，不允许存在其它空行。

每组中 import 语句按 ASCII 码排序。（**注意**：因 `.` 排在 `;` 之前，所以这与纯  ASCII码排序略有不同）

#### 不允许类的静态导入
静态嵌套类以其常规方式导入，而不是使用静态导入。

### Class定义

#### 有且仅有一个顶级类的声明
每个源文件仅能有一个顶级类。

#### 类内容顺序
类的成员或者初始化方法顺序对代码可读性有着很重要的影响，然而对此却没有一个统一正确的标准。不同的类可能有不同的排序方式。
重要的是，每个类都应遵从维护者可解释清楚的 **逻辑顺序** 排序。例如，新方法可能不是简单地放置在类的最后，因为如此产生的「按时间顺序添加」不遵从逻辑顺序。

##### 方法重载：不应被分离
当一个类拥有同名的多个构造器或者方法时，这些构造器或者方法不应被其它代码所分隔，甚至是私有方法。
	
---

## 格式
**术语说明**：*block-like construct* 块状结构 指类的实体、成员或构造器。注意，在后续 4.8.3.1 [数组初始化](#数组初始化：可以写成块状结构) 章节中，所有数组的初始化可被认为是一个块状结构。

### 花括号

#### 花括号在被需要的地方使用
在 `if`、`else`、`for`、`do`、`while` 语句使用花括号，即使他们的实体是空的或者仅包含一条语句。

#### 非空语句块：K&R风格
对于非空语句块和块状结构，花括号遵循 Kernighan & Ritchie 风格（[Egyptian brackets](http://www.codinghorror.com/blog/2012/07/new-programming-jargon.html)）：
* 左花括号之前不能换行。
* 左花括号之后换行。
* 右花括号之前换行。
* 只有右花括号结束了一条语句、一个方法实体、构造器或者命名的类时，右花括号之后才换行。例如 `else` 或 `,` 之后的花括号不允许换行。

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
一些特殊情况，将在 4.8.1 [枚举类](#枚举类) 章节说明。

#### 空语句块：可以简洁
一个空语句块或块状结构可以遵从 K&R 风格（如 [4.1.2 章节](#非空语句块：K-amp-R风格) 描述）。或者，可以在左花括号起始之后，没有任何内容，立即右花括号结束。除非它是 *multi-block statement* 多块语句（一个包含多块的语句，如：`if/else`、`try/catch/finally`）的一部分，否则不允许这样做。

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
每次新写一块或块状结构的代码时，缩进增加 2 个空格。当语句块结束时，缩进返回至上一级别。块缩进规则适用于整个代码块和注释。（请看 4.1.2 [非空语句块：K&R风格](#非空语句块：K-amp-R风格) 章节的例子）

### 一个语句占一行
每个语句后面都有换行符。

### 单行字符限制：100
Java 代码每行限制 100 个字符。除非另有说明，任何超过此限制的都必须被换行。在 4.5 [换行](#换行) 章节中会有具体的解释。

特殊情况：
* 遵循行限制规则无法实现的地方。（例如 Javadoc 中的 URL、JSNI 中的方法引用）
* package 语句和 import 语句。（详情请看 3.2 [package语句](#package语句) 章节和 3.3 [import语句](#import语句) 章节）
* 注释中可能直接被复制粘贴执行的 shell 命令。

### 换行
**术语说明**：将可以合法占据一行的代码被拆分成多行，这种行为称作 *line-wrapping* 换行。

并没有全面的、明确的规范适用于所有场景下的换行，对于同一行代码，通常有多种有效的方法。

> **注意**：换行的典型原因，是为了避免代码超出了单行字符的限制。即使一行代码可以被修正至符合单行字符限制，也可能会由作者的决定而换行。

> **小记**：不换行的情况下，额外的方法或者局部变量可能也可以解决问题。

#### 在何处换行
换行的主要原则是：倾向于在 **更高语法级别** 处换行。同时：
1. 在 *non-assignment* 非赋值运算符之前换行。（注意这与 Google 风格的其它语言不同，如 C++ 和 JavaScript）
	* 这也适用于以下「类似操作符」的符号：
		* 点分隔符`.`
		* 方法引用中的两个冒号`::`
		* 类型约束中的&符`T <extends Foo Foo & Bar>`
		* 异常捕获中的|符`catch (FooException | BarException e)`
2. 在 *assignment* 赋值运算符之后换行，但在之前换行也可以接受。
	* 这也适用于 foreach 语句中「类似赋值操作符」的冒号。
3. 方法或者构造器名与紧随它的开括号 `(` 相连。
4. 逗号 `,` 紧随它之前的内容。
5. 存在 lambda 箭头的一行永不换行，除非 lambda 主体仅有单个表达式，且能在 lambda 箭头后立即出现的情况下。例如：
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
换行时，第一行之后的每一行至少比第一行多缩进 4 个空格。

连续换行时，缩进可能超过 4 个空格。一般来说，当且仅当两行以同一级别的语法元素开头时，它们才会持有相同水平的缩进。

4.6.3 [水平对齐](#水平对齐) 章节介绍不鼓励使用可变数目的空格来对齐上一行的符号。

### 空格

#### 垂直空格
以下情况需使用单个空行：

1. 类中连续的成员或初始化方法之间：字段、构造器、方法、嵌套类、静态初始化和实例初始化。
	* **特殊情况**:两个连续字段（无其它代码）之间的空行是可选的。比如可以使用空行去创建字段间的  *logical groupings* 逻辑分组
	* **特殊情况**:枚举常量之间的空行在 4.8.1 [枚举类](#枚举类) 章节介绍。
2. 逻辑上分组代码的语句之间。
3. 类中第一个成员或初始化方法之前，或者最后一个成员或始化方法之后。（不鼓励也不反对）
4. 本文档其它章节中所要求的（如 3 [源文件结构](#源文件结构) 章节和 3.3 [import语句](#import语句) 章节）

多个连续的空行是允许的，但这是不必要也不鼓励的。

#### 水平空格
除语言、其它规则要求、语法分隔、注释和 Javadoc 的需要之外，单个 ASCII 空格 **仅** 出现在以下位置：

1. 任何保留关键字和它之后的开阔号 `(` 之间，如 `if`、`for`、`catch`。
2. 任何保留关键字和它之前的右花括号 `}` 之间，如 `else`、`catch`。
3. 任何左花括号 `{` 之前。除了：
	* `@SomeAnnotation({a, b})` 不使用空格
	* <code>String[][] x = &#123;&#123;&quot;foo&quot;&#125;&#125;;</code> <code>&#123;&#123;</code>之间没有空格
4. 二进制或三元操作符的两侧。这也适用于以下「类似操作符」的符号：
	* 类型约束中的&符：`<T extends Foo & Bar>`
	* 多异常捕获中的|符：`catch (FooException | BarException e)`
	* 增强版 `for` 语句foreach中的`:`
	* lambda表达式中的箭头：`(String str) -> str.length()`
	但不是：
	* 写法类似 `Object::toString` 方法引用中的 `::`
	* 写法类似 `Object.toString()` 点分隔符中的 `.`
5. `,:;` 或者闭括号 `)` 之后。
6. 行尾的注释 `//` 的两侧。此处多个空格也是允许的，但不是必须的。
7. 类型和变量的定义之间：`List<String> list`。
8. 数组初始化时，花括号的两侧。这是可选的。
	* `new int[] {5, 6}` 和 `new int[] { 5, 6 }` 都是可行的

本规则不影响行开始或结束时的空格，只针对行内的空格。

#### 水平对齐
**术语说明**：*Horizontal alignment* 水平对齐 指为对齐本行与上一行的某些符号，而添加额外可变数目空格的做法。

这种做法是允许的，但在 Google 风格中不是必须的。甚至不需要在已经对齐的地方保持水平对齐。

这是一个先无对齐后有的例子：
```java
private int x; // this is fine
private Color color; // this too

private int   x;      // permitted, but future edits
private Color color;  // may leave it unaligned
```

> 水平对齐有助阅读代码，但难以日后维护。已经考虑日后仅需调整一行代码的可能性，却依旧能保持美观对齐的做法，是 **允许** 的。但更频繁的情况是编辑器（或者你自己）会提示你调整附近行中的空格。而这一行为可能会触发级联式的格式化，从而导致了「大爆炸」。在最坏的情况下会导致大量无意义的工作，最好的情况下依然会影响版本控制、减慢代码评审速度、引起代码合并冲突。

### 分组小括号：推荐使用
只有当开发人员和评审人员都同意没有小括号也不会误解代码原意，且不会影响代码可读性的情况下，才可以省略分组小括号。假设所有 Java 开发者都熟知运算符的优先级是不合理的。

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
每个变量声明（不论字段或局部变量）只声明一个变量：如变量声明 `int a, b;` 是不允许的。

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

##### 拒绝C语言式的声明
中括号是类型而非变量的一部分： `String[] args` 合法，`String args[]` 非法。

#### switch语句
**术语说明**： switch 语句的花括号里是一个或多个 *statement groups* 语句组。每个语句组包含一个或多个 switch 标签（`case Foo:` 或 `default:`）,且标签之后是一个或多个常规语句（最后一个语句组可包含零个或多个 switch 标签）。

##### 缩进
与其它所有语句块一样，switch 语句块中的内容缩进2个空格。

开始 switch 标签之后应换行并增加 2 个缩进级，如语句块刚开始一样。结束 switch 标签之后应返回上级缩进，如语句块结束一样。

##### 下降执行的注释
在 switch 语句中，每个语句组都应突然终止（使用 `break`、`continue`、`return`、或者抛出异常），或者以注释形式标记指出此处可能会执行到下一个语句组。所有能传达下降执行意思的注释都是可行的（如典型的 `// fall through`）。在最后一个分组中，这个特殊的注释不是必须的。例如：
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
注意 `case 1:` 之后没有注释，只有在语句组的结尾时才可这样。

##### default语句组是必须的
每个switch语句都必须包含 `default` 语句组，即使它不包含任何代码。

**注意**：如果 `enum` 类型的 switch 语句组覆盖了所有的可能性，那么 `default` 语句组可以省略。IDE 和大多数语法分析器都支持这种提示。

#### 注解
注解适用于紧随 Javadoc 之后的类、方法和构造器，且每个注解占有一行。这些行不构成换行（ 4.5 [换行](#换行) 章节），所以无需增加缩进。例如：
```java
@Override
@Nullable
public String getNameIfPresent() { ... }
```
特殊情况：单个无参数的注解可以和行首的修饰符放在一起，例如：
```java
@Override public int hashCode() { ... }
```
注解也适用于紧随 Javadoc 之后的字段，且这种情况下，多个注解（允许有参数）可以写在同一行。例如：
```java
@Partial @Mock DataLoader loader;
```
注解、局部变量或类型没有具体的格式化要求。

#### 注释
本章节介绍 *implementation comments* 实现注释。Javadoc 在 7 [Javadoc](#Javadoc) 章节单独介绍。

换行符之前可以有任意空格，之后是实现注释。这样的注释应使用非空行。

##### 注释块风格
注释块与它关联的代码缩进相同。可以是 `/* ... */` 风格或 `// ...` 风格。对于多行的 `/* ... */` 注释，每行必须以 `*` 开始且与上一行的 `*` 对齐。
```java
/*
 * This is          // And so           /* Or you can
 * okay.            // is this.          * even do this. */
 */
```
注释不应被星号或者其它字符绘制的框所包围。

> 写多行注释的时候，如果你想在必要时能自动对齐代码，那么应使用 `/* ... */`。绝大多数编辑器不会自动对齐 `// ...` 风格的注释。

#### 修饰符
当类和成员拥有修饰符时，应以 Java 语言规范建议的顺序出现：
`public protected private abstract default static final transient volatile synchronized native strictfp`

#### 字面量数值
`long` 长整型字面量带有 `L` 后缀，且永远不为小写（避免混淆数字 `1`）。例如 `3000000000L` 而不是 `3000000000l`。

---

## 命名

### 标识符通用规则
标识符只允许使用 ASCII 字母和数字，且在少数情况中可以使用下划线。因此，每个有效的标识符都可以由正则表达式 `\w+` 匹配。

在 Google 风格中特殊的前缀和后缀，如 `name_`、`mName`、`s_name`、`kName` 是不允许的。

### 标识符类型规则

#### 包名
包名全小写，连续的单词直接拼接，不使用下划线。例如 `com.example.deepspace`，而不是 `com.example.deepSpace` 或 `com.example.deep_space`。

#### 类名
类以 [大骆峰](#骆驼峰式命名) 式命名。

类名通常是名词或名词短语，如 `Character` 和 `ImmutableList`。接口名可能是名词或名词短语，如 `List`，也可能是形容词或形容词短语，如 `Readable`。

注解类型命名，没有具体的规则和成熟的约定。

测试类名以被测类的类名，加上 `Test` 结尾组成。例如 `HashTest` 或 `HashIntegrationTest`。

#### 方法名
方法以 [小骆峰](#骆驼峰式命名) 式命名。

方法名通常是动词或动词短语。例如 `sendMessage` 和 `stop`。

下划线可能为了逻辑上的分隔，而出现在 Junit 测试方法名中。一个典型的模式是 `test<MethodUnderTest>_<state>`，例如 `testPop_emptyStack`。对测试方法的命名不存在唯一正确的方式。

#### 常量名
常量使用 `CONSTANT_CASE`（全大写，以下划线分隔）命名。但 Java 中的常量意味着什么？

常量是 static final 修饰的字段，是不可变的且其方法是无副作用的。包括基本元素、字符串、不可变类型和不可变类型的不可变集合。一个实例的外部状态如果是可变的，就不属于常量。但仅仅不改变实例的外部状态，又是不够的。例如：
```java
// Constants
static final int NUMBER = 5;
static final ImmutableList<String> NAMES = ImmutableList.of("Ed", "Ann");
static final ImmutableMap<String, Integer> AGES = ImmutableMap.of("Ed", 35, "Ann", 32);
static final Joiner COMMA_JOINER = Joiner.on(','); // because Joiner is immutable
static final SomeMutableType[] EMPTY_ARRAY = {};
enum SomeEnum { ENUM_CONSTANT }

// Not constants
static String nonFinal = "non-final";
final String nonStatic = "non-static";
static final Set<String> mutableCollection = new HashSet<String>();
static final ImmutableSet<SomeMutableType> mutableElements = ImmutableSet.of(mutable);
static final ImmutableMap<String, SomeMutableType> mutableValues =
    ImmutableMap.of("Ed", mutableInstance, "Ann", mutableInstance2);
static final Logger logger = Logger.getLogger(MyClass.getName());
static final String[] nonEmptyArray = {"these", "can", "change"};
```
常量名通常是名词或名词短语。

#### 非常量字段名
非常量字段（静态或者非静态）以 [小骆峰](#骆驼峰式命名) 式命名。

非常量字段名通常是名词或名词短语。例如：`computedValues` 或 `index`。

#### 参数名
参数以 [小骆峰](#骆驼峰式命名) 式命名。

避免在 public 方法中使用单个字符作为参数名。

#### 局部变量名
局部变量以 [小骆峰](#骆驼峰式命名) 式命名。

局部变量即使是 final 不可变的，也不被认为是常量，且不应该以常量形式命名。

#### 类型变量名
类型变量以以下两种风格之一命名：
1. 单个大写字母，后跟可选的单个数字（如 `E`，`T`，`X`，`T2`）。
2. 以类形式的命名（ 5.2.2 [类名](#类名) 章节），后跟大写字母 T（例如：`RequestT`，`FooBarT`）。

### 骆驼峰式命名
有多种情况下需要将英语短语转换为驼峰形式，如缩写的单词 iOS 或不寻常的结构 IPv6 。为了提高可预测性， Google 风格指定了以下几种明确的方案。

以散文形式的命名开始：
1. 将英语短语转换成纯 ASCII 编码，并删除所有单引号。例如，Müller's algorithm 可以转换成 Muellers algorithm。
2. 以空格或其它任何标点符号（通常为连字符）分隔单词。
	* 推荐：如果已有单词是驼峰式拼写的，则分隔它的组成部分（如 AdWords 分隔成 ad words）。注意如 iOS 这样的单词并不是真正的驼峰式拼写，它违反了所有惯例，因此不适用于本条建议。
3. 将所有字母转换为小写（包括缩写），然后将...的首字母转换为大写：
	* ... 每个单词，产生大驼峰式命名。
	* ... 除去第一个单词的其它单词，产生小驼峰式命名。
4. 最后，连接所有字母成为一个标识符。

注意，原单词的大小写应被忽略。例如：

Prose form | Correct | Incorrect
--- | --- |---
"XML HTTP request" | `XmlHttpRequest` | `XMLHTTPRequest`
"new customer ID" | `newCustomerId` | `newCustomerID`
"inner stopwatch" | `innerStopwatch` | `innerStopWatch`
"supports IPv6 on iOS?" | supportsIpv6OnIos | supportsIPv6OnIOS
"YouTube importer" | YouTubeImporter<br>YoutubeImporter* | 

*是可以接受，但不推荐的。

> 在英语中，一些模糊的连字，如 nonempty 和 non-empty 都是正确的，所以方法名 `checkNonempty` 和 `checkNonEmpty` 也都是正确的。

---

## 编程实战

### @Override：尽量使用
只要是合法的方法，就应被标记 `@Override` 注解。合法的方法包括重写父类方法的类方法，实现接口方法的类方法，和重定义父接口方法的接口方法。

**特殊情况**：当父类方法为 `@Deprecated` 时，`@Override` 可以省略。

### 异常捕获：不能忽略
排除下述例子，在异常捕获中不做任何处理的做法是极少正确的。（典型的做法是打印日志，或重新抛出 `AssertionError` 异常）

当在 catch 块中确实不需要做任何处理时，应以注释说明理由。
```java
try {
  int i = Integer.parseInt(response);
  return handleNumericResponse(i);
} catch (NumberFormatException ok) {
  // it's not numeric; that's fine, just continue
}
return handleTextResponse(response);
```

**特殊情况**：在测试代码中，如果捕获的异常是以 `expected` 命名的，那么可以忽略注释。如下是一个非常常见的方法，为确保代码在测试时抛出预期​​的异常，因此可以不需要注释。
```java
try {
  emptyStack.pop();
  fail();
} catch (NoSuchElementException expected) {
}
```

### 静态成员：规范使用
应以类名引用类的静态成员，而不是以类的引用实例或者表达式。
```java
Foo aFoo = ...;
Foo.aStaticMethod(); // good
aFoo.aStaticMethod(); // bad
somethingThatYieldsAFoo().aStaticMethod(); // very bad
```

### finalize：禁用
覆盖 `Object.finalize()` 是 **非常罕见** 的行为。

> 小记：不要使用 finalize()。如果真的有必要，请先仔细阅读并深刻理解 [Effective Java 第七条](http://books.google.com/books?isbn=8131726592) Avoid Finalizers，之后也不要使用 finalize()。

---

## Javadoc

### 格式

#### 一般形式
Javadoc 的基本形式如下所示：
```java
/**
 * Multiple lines of Javadoc text are written here,
 * wrapped normally...
 */
public int method(String p1) { ... }
```
... 或者是单行形式的：
```java
/** An especially short bit of Javadoc. */
```
基本形式的 Javadoc 总是可以接受的。只有一行内容的 Javadoc 语句（包括注释标记）允许以单行形式替换。注意这种情况只能在 Javadoc 中没有任何如 `@return` 之类的标签时才可发生。

#### 段落
空行（仅包含左侧对齐 * 号的行）出现在段落之间。若存在 @ 标记，空行则应出现在 @ 标记之前。除去第一个段落，每个段落的第一个单词之前都应有 `<p>` 标签，且 `<p>` 标签与段落的第一个单词之间没有任何空格。

#### 块标签
标准的块标签按以下顺序出现 `@param`、`@return`、`@throws`、`@deprecated`，且这四种类型不会出现在描述为空的 Javadoc 中。当无法在一行中容纳块标签时，下一行需要再缩进至少4个空格。

### 摘要片段
每个 Javadoc 块以一个简短的 **摘要片段** 开始。这个片段非常重要，它是在某些情况下唯一可以出现的文字，如在类和方法的索引中。

摘要片段可以是名词短语或者动词短语，但却不是一个完整的句子。它不以 `A {@code Foo} is a...` 或者 `This method returns...` 开始，也不会形成如 `Save the record.` 这样的祈使句。不过，摘要片段应是区分大小写且带有标点符号的，就好似它是个完整的句子。

> **小记**：一个常见的错误是把简短的 Javadoc 写成 `/** @return the customer ID */`。这是不正确的，应被修正为 `/** Returns the customer ID. */`。

### 尽量使用Javadoc
Javadoc 应在每个 `public` 修饰的类，及其 `public` 和 `protected` 修饰的成员中存在，但除了下述的几个例外。

Javadoc 中也可以附加额外内容，如 7.3.4 [非必需的Javadoc](#非必需的Javadoc) 章节所示。

#### 特殊情况：自解释的方法
对简单明了的方法，Javadoc 是可选的，如 `getFoo`。这种情况下，注释除了 Returns the foo 也确实没什么好写的了。

> 注意：不应使用本规则作为省略关键注释的理由。例如，一个名为 `getCanonicalName` 的方法，读者可能不知道方法名的含义，因此不能省略它的 Javadoc。

#### 特殊情况：重写方法
Javadoc 并不总是出现在重写方法中。

#### 非必需的Javadoc
类或成员应根据实际需要编写 Javadoc。

当 *implementation comments* 实现注释需要定义类或成员的目的和行为时，可以将注释 `// ...` 替换成 Javadoc `/** ... */`。

本规则不一定遵循 7.1.2、7.1.3、7.2 章节。
