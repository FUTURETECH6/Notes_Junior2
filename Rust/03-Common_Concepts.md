[TOC]

## [变量与可变性](https://rust-lang.tw/book-tw/ch03-01-variables-and-mutability.html#變數與可變性)

### [变量和常量的区别](https://kaisery.github.io/trpl-zh-cn/ch03-01-variables-and-mutability.html#变量和常量的区别)

不允许改变值的变量，可能会使你想起另一个大部分编程语言都有的概念：**常量**（*constants*）。类似于不可变变量，常量是绑定到一个名称的不允许改变的值，不过常量与变量还是有一些区别。

首先，不允许对常量使用 `mut`。常量不光默认不能变，它总是不能变。

声明常量使用 `const` 关键字而不是 `let`，<u>并且 *必须* 注明值的类型。</u>在下一部分，[“数据类型”](https://kaisery.github.io/trpl-zh-cn/ch03-02-data-types.html#data-types) 中会介绍类型和类型注解，现在无需关心这些细节，记住总是标注类型即可。

常量可以在任何作用域中声明，包括全局作用域，这在一个值需要被很多部分的代码用到时很有用。

最后一个区别是，常量只能被设置为常量表达式，而不能是函数调用的结果，或任何其他只能在运行时计算出的值。

这是一个声明常量的例子，它的名称是 `MAX_POINTS`，值是 100,000。（Rust 常量的命名规范是使用下划线分隔的大写字母单词，并且可以在数字字面值中插入下划线来提升可读性）：

```rust
const MAX_POINTS: u32 = 100_000;
```

在声明它的作用域之中，常量在整个程序生命周期中都有效，这使得常量可以作为多处代码使用的全局范围的值，例如一个游戏中所有玩家可以获取的最高分或者光速。

将遍布于应用程序中的硬编码值声明为常量，能帮助后来的代码维护人员了解值的意图。如果将来需要修改硬编码值，也只需修改汇聚于一处的硬编码值。

### [隐藏（Shadowing）](https://kaisery.github.io/trpl-zh-cn/ch03-01-variables-and-mutability.html#隐藏shadowing)

正如在第二章猜猜看游戏的 [“比较猜测的数字和秘密数字”](https://kaisery.github.io/trpl-zh-cn/ch02-00-guessing-game-tutorial.html#comparing-the-guess-to-the-secret-number) 中所讲，我们可以定义一个与之前变量同名的新变量，而新变量会 **隐藏** 之前的变量。Rustacean 们称之为第一个变量被第二个 **隐藏** 了，这意味着使用这个变量时会看到第二个值。可以用相同变量名称来隐藏一个变量，以及重复使用 `let` 关键字来多次隐藏

## [数据类型](https://kaisery.github.io/trpl-zh-cn/ch03-02-data-types.html#数据类型)

在 Rust 中，每一个值都属于某一个 **数据类型**（*data type*），这告诉 Rust 它被指定为何种数据，以便明确数据处理方式。我们将看到两类数据类型子集：标量（scalar）和复合（compound）。

记住，Rust 是 **静态类型**（*statically typed*）语言，也就是说在编译时就必须知道所有变量的类型。根据值及其使用方式，编译器通常可以推断出我们想要用的类型。当多种类型均有可能时，比如第二章的 [“比较猜测的数字和秘密数字”](https://kaisery.github.io/trpl-zh-cn/ch02-00-guessing-game-tutorial.html#comparing-the-guess-to-the-secret-number) 使用 `parse` 将 `String` 转换为数字时，必须增加类型注解，像这样：

```rust
let guess: u32 = "42".parse().expect("Not a number!");
```

这里如果不添加类型注解，Rust 会显示如下错误，这说明编译器需要我们提供更多信息，来了解我们想要的类型：

```text
error[E0282]: type annotations needed
 --> src/main.rs:2:9
  |
2 |     let guess = "42".parse().expect("Not a number!");
  |         ^^^^^
  |         |
  |         cannot infer type for `_`
  |         consider giving `guess` a type
```

你会看到其它数据类型的各种类型注解。

### [标量类型](https://kaisery.github.io/trpl-zh-cn/ch03-02-data-types.html#标量类型)

**标量**（*scalar*）类型代表一个单独的值。Rust 有四种基本的标量类型：整型、浮点型、布尔类型和字符类型。你可能在其他语言中见过它们。让我们深入了解它们在 Rust 中是如何工作的。

#### [整型](https://kaisery.github.io/trpl-zh-cn/ch03-02-data-types.html#整型)

**整数** 是一个没有小数部分的数字。我们在第二章使用过 `u32` 整数类型。该类型声明表明，它关联的值应该是一个占据 32 比特位的无符号整数（有符号整数类型以 `i` 开头而不是 `u`）。表格 3-1 展示了 Rust 内建的整数类型。在有符号列和无符号列中的每一个变体（例如，`i16`）都可以用来声明整数值的类型。

表格 3-1: Rust 中的整型

| 长度    | 有符号  | 无符号  |
| ------- | ------- | ------- |
| 8-bit   | `i8`    | `u8`    |
| 16-bit  | `i16`   | `u16`   |
| 32-bit  | `i32`   | `u32`   |
| 64-bit  | `i64`   | `u64`   |
| 128-bit | `i128`  | `u128`  |
| arch    | `isize` | `usize` |

#### [浮点型](https://kaisery.github.io/trpl-zh-cn/ch03-02-data-types.html#浮点型)

Rust 也有两个原生的 **浮点数**（*floating-point numbers*）类型，它们是带小数点的数字。Rust 的浮点数类型是 `f32` 和 `f64`，分别占 32 位和 64 位。默认类型是 `f64`，因为在现代 CPU 中，它与 `f32` 速度几乎一样，不过精度更高。

#### [布尔型](https://kaisery.github.io/trpl-zh-cn/ch03-02-data-types.html#布尔型)

正如其他大部分编程语言一样，Rust 中的布尔类型有两个可能的值：`true` 和 `false`。Rust 中的布尔类型使用 `bool` 表示。例如：

文件名: src/main.rs

```rust
fn main() {
    let t = true;

    let f: bool = false; // 显式指定类型注解
}
```

使用布尔值的主要场景是条件表达式，例如 `if` 表达式。在 [“控制流”（“Control Flow”）](https://kaisery.github.io/trpl-zh-cn/ch03-05-control-flow.html#control-flow) 部分将介绍 `if` 表达式在 Rust 中如何工作。

#### [字符类型](https://kaisery.github.io/trpl-zh-cn/ch03-02-data-types.html#字符类型)

目前为止只使用到了数字，不过 Rust 也支持字母。Rust 的 `char` 类型是语言中最原生的字母类型，如下代码展示了如何使用它。（注意 `char` 由单引号指定，不同于字符串使用双引号。）

文件名: src/main.rs

```rust
fn main() {
    let c = 'z';
    let z = 'ℤ';
    let heart_eyed_cat = '😻';
}
```

<u>Rust 的 `char` 类型的大小为四个字节(four bytes)</u>，并代表了一个 Unicode 标量值（Unicode Scalar Value），这意味着它可以比 ASCII 表示更多内容。在 Rust 中，拼音字母（Accented letters），中文、日文、韩文等字符，emoji（绘文字）以及零长度的空白字符都是有效的 `char` 值。Unicode 标量值包含从 `U+0000` 到 `U+D7FF` 和 `U+E000` 到 `U+10FFFF` 在内的值。不过，“字符” 并不是一个 Unicode 中的概念，所以人直觉上的 “字符” 可能与 Rust 中的 `char` 并不符合。第八章的 [“使用字符串存储 UTF-8 编码的文本”](https://kaisery.github.io/trpl-zh-cn/ch08-02-strings.html#storing-utf-8-encoded-text-with-strings) 中将详细讨论这个主题。

### [复合类型](https://kaisery.github.io/trpl-zh-cn/ch03-02-data-types.html#复合类型)

**复合类型**（*Compound types*）可以将多个值组合成一个类型。Rust 有两个原生的复合类型：元组（tuple）和数组（array）。

#### [元组类型](https://kaisery.github.io/trpl-zh-cn/ch03-02-data-types.html#元组类型)

元组是一个将多个其他类型的值组合进一个复合类型的主要方式。元组长度固定：一旦声明，其长度不会增大或缩小。

我们使用包含在圆括号中的逗号分隔的值列表来创建一个元组。元组中的每一个位置都有一个类型，而且这些不同值的类型也不必是相同的。这个例子中使用了可选的类型注解：

文件名: src/main.rs

```rust
fn main() {
    let tup: (i32, f64, u8) = (500, 6.4, 1);
}
```

`tup` 变量绑定到整个元组上，因为元组是一个单独的复合元素。为了从元组中获取单个值，可以使用模式匹配（pattern matching）来解构（destructure）元组值，像这样：

文件名: src/main.rs

```rust
fn main() {
    let tup = (500, 6.4, 1);

    let (x, y, z) = tup;

    println!("The value of y is: {}", y);
}
```

程序首先创建了一个元组并绑定到 `tup` 变量上。接着使用了 `let` 和一个模式将 `tup` 分成了三个不同的变量，`x`、`y` 和 `z`。这叫做 **解构**（*destructuring*），因为它将一个元组拆成了三个部分。最后，程序打印出了 `y` 的值，也就是 `6.4`。

除了使用模式匹配解构外，也可以<u>使用点号（`.`）后跟值的索引来直接访问它们</u>。例如：

文件名: src/main.rs

```rust
fn main() {
    let x: (i32, f64, u8) = (500, 6.4, 1);

    let five_hundred = x.0;

    let six_point_four = x.1;

    let one = x.2;
}
```

这个程序创建了一个元组，`x`，并接着使用索引为每个元素创建新变量。跟大多数编程语言一样，元组的第一个索引值是 0。

#### [数组类型](https://kaisery.github.io/trpl-zh-cn/ch03-02-data-types.html#数组类型)

另一个包含多个值的方式是 **数组**（*array*）。与元组不同，数组中的每个元素的类型必须相同。Rust 中的数组与一些其他语言中的数组不同，因为 Rust 中的数组是固定长度的：一旦声明，它们的长度不能增长或缩小。

Rust 中，数组中的值位于中括号内的逗号分隔的列表中：

文件名: src/main.rs

```rust
fn main() {
    let a = [1, 2, 3, 4, 5];
}
```

当你想要在栈（stack）而不是在堆（heap）上为数据分配空间（第四章将讨论栈与堆的更多内容），或者是想要确保总是有固定数量的元素时，数组非常有用。但是数组并不如 vector 类型灵活。vector 类型是标准库提供的一个 **允许** 增长和缩小长度的类似数组的集合类型。当不确定是应该使用数组还是 vector 的时候，你可能应该使用 vector。第八章会详细讨论 vector。

一个你可能想要使用数组而不是 vector 的例子是，当程序需要知道一年中月份的名字时。程序不大可能会去增加或减少月份。这时你可以使用数组，因为我们知道它总是包含 12 个元素：

```rust
let months = ["January", "February", "March", "April", "May", "June", "July",
              "August", "September", "October", "November", "December"];
```

可以像这样编写数组的类型：在方括号中包含每个元素的类型，后跟分号，再后跟数组元素的数量。

```rust
let a: [i32; 5] = [1, 2, 3, 4, 5];
```

这里，`i32` 是每个元素的类型。分号之后，数字 `5` 表明该数组包含五个元素。

以这种方式编写数组的类型看起来类似于初始化数组的另一种语法：如果要为每个元素创建包含相同值的数组，可以指定初始值，后跟分号，然后在方括号中指定数组的长度，如下所示：

```rust
let a = [3; 5];
```

变量名为 `a` 的数组将包含 `5` 个元素，这些元素的值最初都将被设置为 `3`。这种写法与 `let a = [3, 3, 3, 3, 3];` 效果相同，但更简洁。

##### [访问数组元素](https://kaisery.github.io/trpl-zh-cn/ch03-02-data-types.html#访问数组元素)

数组是一整块分配在栈上的内存。可以使用索引来访问数组的元素，像这样：

文件名: src/main.rs

```rust
fn main() {
    let a = [1, 2, 3, 4, 5];

    let first = a[0];
    let second = a[1];
}
```

在这个例子中，叫做 `first` 的变量的值是 `1`，因为它是数组索引 `[0]` 的值。变量 `second` 将会是数组索引 `[1]` 的值 `2`。

##### [无效的数组元素访问](https://kaisery.github.io/trpl-zh-cn/ch03-02-data-types.html#无效的数组元素访问)

如果我们访问数组结尾之后的元素会发生什么呢？比如你将上面的例子改成下面这样，这可以编译通过，不过在运行时会因错误而退出：

文件名: src/main.rs

```rust
fn main() {
    let a = [1, 2, 3, 4, 5];
    let index = 10;

    let element = a[index];

    println!("The value of element is: {}", element);
}
```

使用 `cargo run` 运行代码后会产生如下结果：

```text
$ cargo run
   Compiling arrays v0.1.0 (file:///projects/arrays)
    Finished dev [unoptimized + debuginfo] target(s) in 0.31 secs
     Running `target/debug/arrays`
thread 'main' panicked at 'index out of bounds: the len is 5 but the index is
 10', src/main.rs:5:19
note: Run with `RUST_BACKTRACE=1` for a backtrace.
```

~~编译并没有产生任何错误，不过程序会出现一个 **运行时**（*runtime*）错误并且不会成功退出。当尝试用索引访问一个元素时，Rust 会检查指定的索引是否小于数组的长度。如果索引超出了数组长度，Rust 会 *panic*，这是 Rust 术语，它用于程序因为错误而退出的情况。~~

~~这是第一个在实战中遇到的 Rust 安全原则的例子。在很多底层语言中，并没有进行这类检查，这样当提供了一个不正确的索引时，就会访问无效的内存。通过立即退出而不是允许内存访问并继续执行，Rust 让你避开此类错误。第九章会讨论更多 Rust 的错误处理。~~

新版的rust在build的时候就会报错了（不过check倒是没有错的）

## [函数](https://kaisery.github.io/trpl-zh-cn/ch03-03-how-functions-work.html#函数)

### [函数参数](https://kaisery.github.io/trpl-zh-cn/ch03-03-how-functions-work.html#函数参数)

```rust
fn main() {
    another_function(5, 6);
}

fn another_function(x: i32, y: i32) {
    println!("The value of x is: {}", x);
    println!("The value of y is: {}", y);
}
```

### [具有返回值的函数](https://kaisery.github.io/trpl-zh-cn/ch03-03-how-functions-work.html#具有返回值的函数)

```rust
fn main() {
    let x = plus_one(5);

    println!("The value of x is: {}", x);
}

fn plus_one(x: i32) -> i32 {
    x + 1
}
```

运行代码会打印出 `The value of x is: 6`。但如果在包含 `x + 1` 的行尾加上一个分号，把它从表达式变成语句，我们将看到一个错误。

文件名: src/main.rs

```rust
fn main() {
    let x = plus_one(5);

    println!("The value of x is: {}", x);
}

fn plus_one(x: i32) -> i32 {
    x + 1;
}
```

运行代码会产生一个错误，如下：

```text
error[E0308]: mismatched types
 --> src/main.rs:7:28
  |
7 |   fn plus_one(x: i32) -> i32 {
  |  ____________________________^
8 | |     x + 1;
  | |          - help: consider removing this semicolon
9 | | }
  | |_^ expected i32, found ()
  |
  = note: expected type `i32`
             found type `()`
```

主要的错误信息，“mismatched types”（类型不匹配），揭示了代码的核心问题。函数 `plus_one` 的定义说明它要返回一个 `i32` 类型的值，==不过语句并不会返回值==，使用空元组 `()` 表示不返回值。因为不返回值与函数定义相矛盾，从而出现一个错误。在输出中，Rust 提供了一条信息，可能有助于纠正这个错误：它建议删除分号，这会修复这个错误。

## [注释](https://kaisery.github.io/trpl-zh-cn/ch03-04-comments.html#注释)

## [控制流](https://kaisery.github.io/trpl-zh-cn/ch03-05-control-flow.html#控制流)

### [`if` 表达式](https://kaisery.github.io/trpl-zh-cn/ch03-05-control-flow.html#if-表达式)

另外值得注意的是代码中的条件 **必须** 是 `bool` 值。如果条件不是 `bool` 值，我们将得到一个错误。例如，尝试运行以下代码：

文件名: src/main.rs

```rust
fn main() {
    let number = 3;

    if number {
        println!("number was three");
    }
}
```

这里 `if` 条件的值是 `3`，Rust 抛出了一个错误：

```text
error[E0308]: mismatched types
 --> src/main.rs:4:8
  |
4 |     if number {
  |        ^^^^^^ expected bool, found integer
  |
  = note: expected type `bool`
             found type `{integer}`
```

这个错误表明 Rust 期望一个 `bool` 却得到了一个整数。不像 Ruby 或 JavaScript 这样的语言，Rust 并不会尝试自动地将非布尔值转换为布尔值。必须总是显式地使用布尔值作为 `if` 的条件。例如，如果想要 `if` 代码块只在一个数字不等于 `0` 时执行，可以把 `if` 表达式修改成下面这样：

文件名: src/main.rs

```rust
fn main() {
    let number = 3;

    if number != 0 {
        println!("number was something other than zero");
    }
}
```

运行代码会打印出 `number was something other than zero`。

#### [在 `let` 语句中使用 `if`](https://kaisery.github.io/trpl-zh-cn/ch03-05-control-flow.html#在-let-语句中使用-if)

因为 `if` 是一个表达式，我们可以在 `let` 语句的右侧使用它，例如在示例 3-2 中：

文件名: src/main.rs

```rust
fn main() {
    let condition = true;
    let number = if condition {
        5
    } else {
        6
    };

    println!("The value of number is: {}", number);
}
```

示例 3-2：将 `if` 表达式的返回值赋给一个变量

`number` 变量将会绑定到表示 `if` 表达式结果的值上。运行这段代码看看会出现什么：

```text
$ cargo run
   Compiling branches v0.1.0 (file:///projects/branches)
    Finished dev [unoptimized + debuginfo] target(s) in 0.30 secs
     Running `target/debug/branches`
The value of number is: 5
```

记住，代码块的值是其最后一个表达式的值，而数字本身就是一个表达式。在这个例子中，整个 `if` 表达式的值取决于哪个代码块被执行。这意味着 `if` 的每个分支的可能的返回值都必须是相同类型；在示例 3-2 中，`if` 分支和 `else` 分支的结果都是 `i32` 整型。如果它们的类型不匹配，如下面这个例子，则会出现一个错误：

文件名: src/main.rs

```rust
fn main() {
    let condition = true;

    let number = if condition {
        5
    } else {
        "six"
    };

    println!("The value of number is: {}", number);
}
```

当编译这段代码时，会得到一个错误。`if` 和 `else` 分支的值类型是不相容的，同时 Rust 也准确地指出在程序中的何处发现的这个问题：

```text
error[E0308]: if and else have incompatible types
 --> src/main.rs:4:18
  |
4 |       let number = if condition {
  |  __________________^
5 | |         5
6 | |     } else {
7 | |         "six"
8 | |     };
  | |_____^ expected integer, found &str
  |
  = note: expected type `{integer}`
             found type `&str`
```

`if` 代码块中的表达式返回一个整数，而 `else` 代码块中的表达式返回一个字符串。==这不可行，因为变量必须只有一个类型。Rust 需要在编译时就确切的知道 `number` 变量的类型，这样它就可以在编译时验证在每处使用的 `number` 变量的类型是有效的。==Rust 并不能够在 `number` 的类型只能在运行时确定的情况下工作；这样会使编译器变得更复杂而且只能为代码提供更少的保障，因为它不得不记录所有变量的多种可能的类型。

### [使用循环重复执行](https://kaisery.github.io/trpl-zh-cn/ch03-05-control-flow.html#使用循环重复执行)

#### [从循环返回](https://kaisery.github.io/trpl-zh-cn/ch03-05-control-flow.html#从循环返回)

`loop` 的一个用例是重试可能会失败的操作，比如检查线程是否完成了任务。然而你可能会需要将操作的结果传递给其它的代码。如果将返回值加入你用来停止循环的 `break` 表达式，它会被停止的循环返回：

```rust
fn main() {
    let mut counter = 0;

    let result = loop {
        counter += 1;

        if counter == 10 {
            break counter * 2;
        }
    };

    println!("The result is {}", result);
}
```

在循环之前，我们声明了一个名为 `counter` 的变量并初始化为 `0`。接着声明了一个名为 `result` 来存放循环的返回值。在循环的每一次迭代中，我们将 `counter` 变量加 `1`，接着检查计数是否等于 `10`。当相等时，使用 `break` 关键字返回值 `counter * 2`。循环之后，我们通过分号结束赋值给 `result` 的语句。最后打印出 `result` 的值，也就是 20。

#### [`while` 条件循环](https://kaisery.github.io/trpl-zh-cn/ch03-05-control-flow.html#while-条件循环)

```rust
fn main() {
    let mut number = 3;

    while number != 0 {
        println!("{}!", number);

        number = number - 1;
    }

    println!("LIFTOFF!!!");
}
```



#### [使用 `for` 遍历集合](https://kaisery.github.io/trpl-zh-cn/ch03-05-control-flow.html#使用-for-遍历集合)

```rust
fn main() {
    let a = [10, 20, 30, 40, 50];

    for element in a.iter() {
        println!("the value is: {}", element);
    }
}
```

```rust
fn main() {
    for number in 1..4 {
        println!("{}!", number);
    }
    println!("LIFTOFF!!!");
}
```

```rust
fn main() {
    for number in (1..4).rev() {
        println!("{}!", number);
    }
    println!("LIFTOFF!!!");
}
```

