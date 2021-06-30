[TOC]

## [什么是所有权？](https://kaisery.github.io/trpl-zh-cn/ch04-01-what-is-ownership.html#什么是所有权)

### [内存与分配](https://kaisery.github.io/trpl-zh-cn/ch04-01-what-is-ownership.html#内存与分配)

```rust
let s1 = String::from("hello");
let s2 = s1;
```

当我们将 `s1` 赋值给 `s2`，`String` 的数据被复制了，这意味着我们从栈上拷贝了它的指针、长度和容量。我们并没有复制指针指向的堆上数据。

之前我们提到过当变量离开作用域后，Rust 自动调用 `drop` 函数并清理变量的堆内存。不过图 4-2 展示了两个数据指针指向了同一位置。这就有了一个问题：当 `s2` 和 `s1` 离开作用域，他们都会尝试释放相同的内存。这是一个叫做 **二次释放**（*double free*）的错误，也是之前提到过的内存安全性 bug 之一。两次释放（相同）内存会导致内存污染，它可能会导致潜在的安全漏洞。

为了确保内存安全，这种场景下 Rust 的处理有另一个细节值得注意。==与其尝试拷贝被分配的内存，Rust 则认为 `s1` 不再有效==，因此 Rust 不需要在 `s1` 离开作用域后清理任何东西。看看在 `s2` 被创建之后尝试使用 `s1` 会发生什么；这段代码不能运行：

```rust
let s1 = String::from("hello");
let s2 = s1;

println!("{}, world!", s1);
```

#### [变量与数据交互的方式（二）：克隆](https://kaisery.github.io/trpl-zh-cn/ch04-01-what-is-ownership.html#变量与数据交互的方式二克隆)

如果我们 **确实** 需要深度复制 `String` 中堆上的数据，而不仅仅是栈上的数据，可以使用一个叫做 `clone` 的通用函数。第五章会讨论方法语法，不过因为方法在很多语言中是一个常见功能，所以之前你可能已经见过了。

这是一个实际使用 `clone` 方法的例子：

```rust
let s1 = String::from("hello");
let s2 = s1.clone();

println!("s1 = {}, s2 = {}", s1, s2);
```

这段代码能正常运行，并且明确产生图 4-3 中行为，这里堆上的数据 **确实** 被复制了。

当出现 `clone` 调用时，你知道一些特定的代码被执行而且这些代码可能相当消耗资源。你很容易察觉到一些不寻常的事情正在发生。

#### [只在栈上的数据：拷贝](https://kaisery.github.io/trpl-zh-cn/ch04-01-what-is-ownership.html#只在栈上的数据拷贝)

这里还有一个没有提到的小窍门。这些代码使用了整型并且是有效的，他们是示例 4-2 中的一部分：

```rust
let x = 5;
let y = x;

println!("x = {}, y = {}", x, y);
```

但这段代码似乎与我们刚刚学到的内容相矛盾：没有调用 `clone`，不过 `x` 依然有效且没有被移动到 `y` 中。

原因是==像整型这样的在编译时已知大小的类型被整个存储在栈上，所以拷贝其实际的值是快速的。==这意味着没有理由在创建变量 `y` 后使 `x` 无效。换句话说，这里没有深浅拷贝的区别，所以这里调用 `clone` 并不会与通常的浅拷贝有什么不同，我们可以不用管它。

### [所有权与函数](https://kaisery.github.io/trpl-zh-cn/ch04-01-what-is-ownership.html#所有权与函数)

将值传递给函数在语义上与给变量赋值相似。向函数传递值可能会移动或者复制，就像赋值语句一样。示例 4-3 使用注释展示变量何时进入和离开作用域：

文件名: src/main.rs

```rust
fn main() {
    let s = String::from("hello");  // s 进入作用域

    takes_ownership(s);             // s 的值移动到函数里 ...
                                    // ... 所以到这里不再有效

    let x = 5;                      // x 进入作用域

    makes_copy(x);                  // x 应该移动函数里，
                                    // 但 i32 是 Copy 的，所以在后面可继续使用 x

} // 这里, x 先移出了作用域，然后是 s。但因为 s 的值已被移走，
  // 所以不会有特殊操作

fn takes_ownership(some_string: String) { // some_string 进入作用域
    println!("{}", some_string);
} // 这里，some_string 移出作用域并调用 `drop` 方法。占用的内存被释放

fn makes_copy(some_integer: i32) { // some_integer 进入作用域
    println!("{}", some_integer);
} // 这里，some_integer 移出作用域。不会有特殊操作
```

示例 4-3：带有所有权和作用域注释的函数

当尝试在调用 `takes_ownership` 后使用 `s` 时，Rust 会抛出一个编译时错误。这些静态检查使我们免于犯错。试试在 `main` 函数中添加使用 `s` 和 `x` 的代码来看看哪里能使用他们，以及所有权规则会在哪里阻止我们这么做。

```rust
fn main() {
    let s = String::from("hello");
    takes_ownership(s);
    let x = 5;
    makes_copy(x);

    println!("{}", s);
    println!("{}", x);
}

error[E0382]: borrow of moved value: `s`
 --> src/main.rs:7:20
  |
2 |     let s = String::from("hello");
  |         - move occurs because `s` has type `String`, which does not implement the `Copy` trait
3 |     takes_ownership(s);
  |                     - value moved here
...
7 |     println!("{}", s);
  |                    ^ value borrowed here after move
```

### [返回值与作用域](https://kaisery.github.io/trpl-zh-cn/ch04-01-what-is-ownership.html#返回值与作用域)

返回值也可以转移所有权。示例 4-4 与示例 4-3 一样带有类似的注释。

文件名: src/main.rs

```rust
fn main() {
    let s1 = gives_ownership(); // gives_ownership将返回值移给s1

    let s2 = String::from("hello2"); // s2进入作用域

    let s3 = takes_and_gives_back(s2); // s2被移动到takes_and_gives_back中,它也将返回值移给s3

    println!("{}", s1);
    println!("{}", s2);
    // error:      ^^ value borrowed here after move
    println!("{}", s3);
} // 这里，s3移出作用域并被丢弃。s2也移出作用域，但已被移走，所以什么也不会发生。s1移出作用域并被丢弃

fn gives_ownership() -> String {
    // gives_ownership 将返回值移动给调用它的函数

    let some_string = String::from("hello1"); // some_string进入作用域.

    some_string // 返回some_string并移出给调用的函数
}

// takes_and_gives_back将传入字符串并返回该值
fn takes_and_gives_back(a_string: String) -> String {
    // a_string进入作用域

    a_string // 返回a_string并移出给调用的函数
}

```

示例 4-4: 转移返回值的所有权

变量的所有权总是遵循相同的模式：将值赋给另一个变量时移动它。当持有堆中数据值的变量离开作用域时，其值将通过 `drop` 被清理掉，除非数据被移动为另一个变量所有。

在每一个函数中都获取所有权并接着返回所有权有些啰嗦。如果我们想要函数使用一个值但不获取所有权该怎么办呢？如果我们还要接着使用它的话，每次都传进去再返回来就有点烦人了，除此之外，我们也可能想返回函数体中产生的一些数据。

## [引用与借用](https://kaisery.github.io/trpl-zh-cn/ch04-02-references-and-borrowing.html#引用与借用)

```rust
fn main() {
    let s = String::from("hello");

    change(&s);
}

fn change(some_string: &String) {
    some_string.push_str(", world");
//  ^^^^^^^^^^^ `some_string` is a `&` reference, so the data it refers to cannot be borrowed as mutable
}
```

正解：

```rust
fn main() {
    let mut s = String::from("hello");

    change(&mut s);
}

fn change(some_string: &mut String) {
    some_string.push_str(", world");
}
```

### Data Race

* ==只能有一个可变引用==
* ==不能同时有不可变引用和可变引用==

```rust
let mut s = String::from("hello");
let r1 = &mut s;
let r2 = &mut s;
//       ^^^^^^ second mutable borrow occurs here
```

同时使用可变引用和不可变引用：

```rust
let r1 = &s; // 没问题
let r2 = &s; // 没问题
let r3 = &mut s; // 大问题
// error[E0502]: cannot borrow `s` as mutable because it is also borrowed as immutable
```

### [悬垂引用（Dangling References）](https://kaisery.github.io/trpl-zh-cn/ch04-02-references-and-borrowing.html#悬垂引用dangling-references)

在具有指针的语言中，很容易通过释放内存时保留指向它的指针而错误地生成一个 **悬垂指针**（*dangling pointer*），所谓悬垂指针是其指向的内存可能已经被分配给其它持有者。相比之下，在 ==Rust 中编译器确保引用永远也不会变成悬垂状态==：当你拥有一些数据的引用，编译器确保数据不会在其引用之前离开作用域。

```rust
fn main() {
    let reference_to_nothing = dangle();
}
fn dangle() -> &String {
    // dangle 返回一个字符串的引用

    let s = String::from("hello"); // s 是一个新字符串

    &s // 返回字符串 s 的引用
} // 这里 s 离开作用域并被丢弃。其内存被释放。危险！


// Solution：不返回引用，直接返回String，交出所有权
fn no_dangle() -> String {
    let s = String::from("hello");

    s
}
```

## [Slice 类型](https://kaisery.github.io/trpl-zh-cn/ch04-03-slices.html#slice-类型)

```rust
fn main() {
    // let s = String::from("hello world");
    let s = "hello world";

    print!("{}", first_word(&s))
}

// fn first_word(s: &String) -> &str {
fn first_word(s: &str) -> &str {
    let bytes = s.as_bytes();

    for (i, &item) in bytes.iter().enumerate() {
        if item == b' ' {
            return &s[0..i];
        }
    }

    &s[..]
}
```

## Str & String

[What are the differences between Rust's `String` and `str`? - Stack Overflow](https://stackoverflow.com/questions/24158114/what-are-the-differences-between-rusts-string-and-str)

