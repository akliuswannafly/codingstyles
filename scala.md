#编码规范1.0


此编码规范旨在提供一种团队开发一致而灵活的操作建议，进而增进代码开发维护效率，减少不必要的冲突。

大部分代码格式使用scalafmt插件在代码编译的时候自动规范，一些比较复杂，插件无能为力的地方，建议编码时遵循本规范。

这个规范并非绝对版本，非常欢迎使用者提出意见和更好的建议来协助持续优化该规范。


## 基本风格 (空格, 花括号, 其他)

### 命名

* 对class/trait/object使用帕斯卡命名法（大驼峰命名法）。

* 变量、方法使用小驼峰命名法，并尽量不使用单字母而是单词组合，做到见名知意。

* 常量使用大写字母。

* 长整型字面量使用L而不是l作为后缀，避免与数字1混淆

### 制表符, 换行, 文件结束符

* 文件不允许包含制表符 (tab)
* 换行采用UNIX风格 (\n)
* 文件结尾保留一个空行
* 文件最后的空行不要有空格

### 行宽

行宽不允许超过120字符。但是在一些情况下，如果换行极大影响可读性，也可以超过120字符的限制。

缘由：方便在GitHub上阅读代码，GitHub每行最多仅显示120字符。

#### 在哪里换行

当一行超长时建议在`,`或者`(`处换行，或者在二元操作符后换行。

### 缩进

一般而言使用2个空格进行缩进，对于超过120字符限制的连续的语句的换行使用4个空格。

特别地，对于过长的`extends`子句，以及构造函数和方法的小括号里形参和实参超过长度限制的，使用4个空格进行缩进。

### 空行

* 不要使用连续两个空行
* 在类中的声明之间使用一个空行
* 如果`case`里的内容超过一行，在内容第一行前加一个空行
* 方法中的声明可以按照逻辑块使用空行进行划分

如果类中有一系列强相关的私有声明，则声明之间可以不使用空行，否则请总是使用一个空行。

### 大括号

对于只有一行的方法不要使用大括号。

类似的，对于只有一行语句的`while`/`if`/`for`也不要使用大括号。

```scala
def plus(x: Int, y: Int): Int =
  x + y

def clamp(v: Int, min: Int, max: Int): Int = {
  if (v < min) min
  else if (v > max) max
  else v
}
```

对于超过一行的表达式，应该放入大括号中。
例如，下面的方式是错误的：

```scala
val iterator =
  new Iterator[A] {
    ...
  }
```

应该使用如下方式：

```scala
val iterator = {
  new Iterator[A] {
    ...
  }
}
```

#### 两行的 if/else 语句

对于上述方法的特例，当两行的`if/else`语句被作为定义或者赋值语句的右值时，例如在`=`之后, 那么大括号可以并且应该被省略不用。

例如：

```scala
def abs(x: Int): Int =
  if (x >= 0) x
  else -x
```

注意如下的语句并不算为两行的 `if/else`，因此是有效的。

```scala
def abs(x: Int): Int =
  if (x >= 0)
    x
  else
    -x
```

#### 带有二元运算符的长表达式

带有二元运算符的超级长的表达式可以使用大括号。

通常带有`&&`,`||`, 或者字符串链接符。
例如：

```scala
val isValidIdent = {
  ident != "" &&
  ident.charAt(0).isUnicodeIdentifierStart &&
  ident.tail.forall(_.isUnicodeIdentifierPart)
}
```

#### lambda函数中的大括号

在lambda函数（匿名函数）中, 前大括号必须置于形参之前，不要置于`=>`之后:

```scala
val f = { (x: Int) =>
  body
}
```

如果第一行过长， 参数列表应该放在下一行，内容放在更下一行，并再缩进两个空格：

```scala
val someLongIdentifierWithHighIdentation = {
  (x: Int, ys: List[Traversable[String]]) =>
    body
}
```

### 空格

下列符号之前不应该使用空格: `:` `,` `;` `)`

下列符号之后必须使用一个空格: `:` `,` `;` `if` `for` `while`

在`{`和`}`之前和之后必须使用一个空格。

部分导入除外，我们建议在`{`之后使用一个空格，在`}`之前使用一个空格。

二元运算符，例如`=>`，两边都应保留一个空格。
有时为了更清晰的看出运算优先级，可以省略一些空格。
比如相比于`x < len - 1`最好写成`x < len-1`, 强调`-`比`<`有更高的优先级。

一元运算符之后不要使用空格。

### 方法调用格式

通常情况下，调用方法的时候应该加上小括号。
如果参数列表只有一个匿名函数，并且该匿名函数不适合内联一行，则应该使用大括号。

通常，点符号应该用于非符号方法，中缀符号应该用于符号方法。
如果唯一的参数是括号lambda，也会使用中缀表示法。

例如:

```scala
// inline lambda, hence (), hence dot-notation
list.map(x => x * 2)

// long lambda, hence braces, hence infix notation
list map { x =>
  if (x < 5) x
  else x * 2
}

// symbolic operator, hence infix notation
value :: list
```

带有大括号的lambda使用点符号可以强制优先级。
如果将调用链接到无参数方法调用，则通常就是这种情况，例如：

```scala
list.map { x =>
  // complicated stuff
}.toMap
```
当调用带有空括号声明的方法时，使用`()`，除非该方法是Java定义的*并且*没有副作用。

### 方法定义

所有公共和受保护方法都必须具有显式结果类型。

不得使用过程语法。
必须使用`: Unit =`替代。

没有形参的无副作用方法声明时应该不使用`()`，除非a）它重载了一个定义时带有`()`的方法（例如`toString()`），或者b）它实现了一个Java库中的方法。


### `for` 表达式

`for` 表达式中仅有一个非`if`的生成器时候，应该使用 `()`，例如：

```scala
for (i <- 0 until n)
  doStuff(i)
```

否则,必须使用`{}`,并且每个生成器独占一行。

```scala
for {
  i <- 0 until n
  j <- 0 until i
} {
  doStuff(i, j)
}
```

关键字`yield`应该置于`for`所在行的结尾，并紧跟着大括号:

```scala
for (x <- xs) yield {
  x * 2
}
```

对于短的`yield`代码块,可以使用如下方式替代:

```scala
for (x <- xs)
  yield x * 2
```

对于多行的`for`,关键字`yield`必须按如下方式放置:

```scala
for {
  i <- 0 until n
  j <- 0 until i
} yield {
  thing(i, j)
}
```

### 导入

导入必须遵循以下格式：

```scala
import scala.language.implicitConversions

import scala.collection.mutable

import java.{util => ju}

import org.scalajs.linker._
import org.scalajs.linker.standard._
```

语言导入必须始终排在第一位，并且必须始终位于文件的顶部（紧跟在`package`声明之后）。

如果从命名空间导入3个以上的项目，请使用通配符导入。


### Scala文档

单行的Scala文档注释必须使用如下形式：

```scala
/** Returns the maximum of a and b. */
def max(a: Int, b: Int): Int = ???
```

多行的Scala文档注释则必须使用如下形式：

```scala
/** Returns the maximum of a and b.
 *
 *  If a > b, returns a. Otherwise returns b.
 */
def max(a: Int, b: Int): Int = ???
```

### 非Scala文档注释

普通的单行注释使用`//`。
多行注释采用如下格式：

```scala
/* This complicated algorithm computes the maximum of two integer values a
 * and b. If a > b, it computes a, otherwise it computes b.
 */
```


## 类的声明

类声明及其构造函数参数，其`extends`子句及其自身类型在技术上是单行。
例如:

```scala
class Foo(val x: Int) extends Bar with Foobar { self =>
```

然而，在许多情况下，这往往变得太长。

如果声明不适合一行，则自身类型必须独占一行，仅缩进2个空格，然后是空行：

```scala
class Foo(val x: Int) extends Bar with Foobar {
  self =>

  // declarations start here
```

如果本身太长，构造函数参数列表应该与方法的形式参数类似地换行，即缩进4个空格，然后是空行：

```scala
class Foo(val x: Int, val y: Int,
    val z: Int) extends Bar with Foobar {

  // declarations start here
```

作为例外，如果构造函数参数是“配置”参数的（长）列表，则应使用以下格式：

```scala
class Foo(
    val width: Int = 1,
    val height: Int = 1,
    val depthOfField: Int = 3
) extends Bar with Foobar {
```

请注意，不论是类型还是默认值都不要垂直对齐。
如果存在多个参数列表（例如，具有隐式参数列表），则每个参数列表独立于其他参数列表遵循其规则，即，垂直地组织一个参数列表并不意味着另外的列表也应该垂直组织。
例如：

```scala
class Foo[A](
    val width: Int = 1,
    val height: Int = 1,
    val depthOfField: Int = 3
)(implicit ct: ClassTag[A]) extends Bar with Foobar {
```

如果太长，`extends`子句本身应该转到下一行，缩进4个空格，然后是一个空行：

```scala
class Foo(val x: Int)
    extends Bar with Foobar with AnotherTrait {

  // declarations start here
```

如果有必要，可以在`with`之前进一步对`extends`子句换行。
额外的行也缩进了4个空格。

```scala
class Foo(val x: Int)
    extends Bar with Foobar with AnotherTrait with YetAnotherTrait
    with HowManyTraitsAreThere with TooManyTraits {

  // declarations start here
```


## if/else

表达式位置中的`if / else`对可以写成

```scala
val x =
  if (condition) someExpr
  else anotherExpr
```

假设两行的每一行都符合长度限制。
我们称这种格式为双行“if / else”。

否则，两个表达式都应放在单独的行上，并缩进2个空格。
另外，`=`的右侧必须用大括号括起来：

```scala
val x = {
  if (condition)
    someExpr
  else
    anotherExpr
}
```

如果其中一个分支需要大括号，则在两个分支上都使用大括号：

```scala
val x = {
  if (condition) {
    val x = someExpr
    x + 5
  } else {
    anotherExpr
  }
}
```

声明部分的`if`和`if/else`应该总是让其分支独占一行。

```scala
if (index >= size)
  throw new IndexOutOfBoundsException
```

如果`if`（或`while`）的`condition`太长，它最多可以被换行*一次*并且使用4个空格进行缩进。
在这种情况下，if和else部分必须用大括号括起来，即使它们是单行的。
因为显然，它们无法应用双行`if / else`格式。

如果条件太长以至于两行不够，那么应该把它提取出来放到一个`def`中，例如：

```scala
val ident: String = ???

def isValidIdent = {
  ident != "" &&
  ident.charAt(0).isUnicodeIdentifierStart &&
  ident.tail.forall(_.isUnicodeIdentifierPart)
}

if (isValidIdent)
  doSomething()
else
  doSomethingElse()
```


## 模式匹配

单行情况应写在一行上，箭头对齐：

```scala
x match {
  case Foo(a, b) => a + b
  case Bar(y)    => 2 * y
}
```

如果内容部分过长不能写在同一行，则将内容放到下一行，用2个空格进行缩进，不使用大括号。
在这种情况下，还要在`case`周围使用一个空白行，不要将箭头对齐。

```scala
x match {
  case Foo(a, b) => a + b

  case Bar(y) =>
    if (y < 5) y
    else y * 2
}
```
