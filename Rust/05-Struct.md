[TOC]

## [定义并实例化结构体](https://kaisery.github.io/trpl-zh-cn/ch05-01-defining-structs.html#定义并实例化结构体)

```rust
struct User {
    username: String,
    email: String,
    sign_in_count: u64,
    active: bool,
}

// 键值对，顺序不用一样
let user1 = User {
    email: String::from("someone@example.com"),
    username: String::from("someusername123"),
    active: true,
    sign_in_count: 1,
};

// mutable
let mut user1 = User {
    email: String::from("someone@example.com"),
    username: String::from("someusername123"),
    active: true,
    sign_in_count: 1,
};
user1.email = String::from("anotheremail@example.com");
```

注意整个实例必须是可变的；<u>Rust 并不允许只将某个字段标记为可变。</u>（可变结构套不可变结构呢）另外需要注意同其他任何表达式一样，我们可以在函数体的最后一个表达式中构造一个结构体的新实例，来隐式地返回这个实例。

### [变量与字段同名时的字段初始化简写语法](https://kaisery.github.io/trpl-zh-cn/ch05-01-defining-structs.html#变量与字段同名时的字段初始化简写语法)

```rust
fn build_user(email: String, username: String) -> User {
    User {
        email,
        username,
        active: true,
        sign_in_count: 1,
    }
}
```

### [使用结构体更新语法从其他实例创建实例](https://kaisery.github.io/trpl-zh-cn/ch05-01-defining-structs.html#使用结构体更新语法从其他实例创建实例)

```rust
let user2 = User {
    email: String::from("another@example.com"),
    username: String::from("anotherusername567"),
    ..user1
};
```

### [使用没有命名字段的**元组结构体**来创建不同的类型](https://kaisery.github.io/trpl-zh-cn/ch05-01-defining-structs.html#使用没有命名字段的元组结构体来创建不同的类型)

类似于tuple，但是有名字

```rust
struct Color(i32, i32, i32);

let black = Color(0, 0, 0);

let Color(x, y, z) = black;  // 解构
```

### [没有任何字段的类单元结构体](https://kaisery.github.io/trpl-zh-cn/ch05-01-defining-structs.html#没有任何字段的类单元结构体)

我们也可以定义一个没有任何字段的结构体！它们被称为 **类单元结构体**（*unit-like structs*）因为它们类似于 `()`，即 unit 类型。类单元结构体常常在你想要在某个类型上实现 trait 但不需要在类型中存储数据的时候发挥作用。我们将在第十章介绍 trait。

### [结构体数据的所有权](https://kaisery.github.io/trpl-zh-cn/ch05-01-defining-structs.html#结构体数据的所有权)

在示例 5-1 中的 `User` 结构体的定义中，我们使用了自身拥有所有权的 `String` 类型而不是 `&str` 字符串 slice 类型。这是一个有意而为之的选择，因为我们想要这个结构体拥有它所有的数据，为此只要整个结构体是有效的话其数据也是有效的。

可以使结构体存储被其他对象拥有的数据的引用，不过这么做的话需要用上 **生命周期**（*lifetimes*），这是一个第十章会讨论的 Rust 功能。生命周期确保结构体引用的数据有效性跟结构体本身保持一致。如果你尝试在结构体中存储一个引用而不指定生命周期将是无效的，比如这样：

文件名: src/main.rs

```rust
struct User {
    username: &str,
    email: &str,
    sign_in_count: u64,
    active: bool,
}

fn main() {
    let user1 = User {
        email: "someone@example.com",
        username: "someusername123",
        active: true,
        sign_in_count: 1,
    };
}
```

编译器会抱怨它需要生命周期标识符：

```text
error[E0106]: missing lifetime specifier
 -->
  |
2 |     username: &str,
  |               ^ expected lifetime parameter

error[E0106]: missing lifetime specifier
 -->
  |
3 |     email: &str,
  |            ^ expected lifetime parameter
```

第十章会讲到如何修复这个问题以便在结构体中存储引用，不过现在，我们会使用像 `String` 这类拥有所有权的类型来替代 `&str` 这样的引用以修正这个错误。

## [一个使用结构体的示例程序](https://kaisery.github.io/trpl-zh-cn/ch05-02-example-structs.html#一个使用结构体的示例程序)

```rust
// main.rs
mod rectangle;

use rectangle::area;
// use rectangle::Rectangle;

fn main() {
    let rect1 = rectangle::Rectangle {
        width: 30,
        height: 50,
    };

    println!(
        "The area of the rectangle is {} square pixels.",
        area(&rect1)
    );
}

// rectangle.rs
pub struct Rectangle {
    pub width: u32,
    pub height: u32,
}

pub fn area(rectangle: &Rectangle) -> u32 {
    rectangle.width * rectangle.height
}
```

如何输出结构？

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

fn main() {
    let rect1 = Rectangle {
        width: 30,
        height: 50,
    };

    println!("rect1 is {:#?}", rect1);
}
```

## [方法语法](https://kaisery.github.io/trpl-zh-cn/ch05-03-method-syntax.html#方法语法)

第一个参数需要为`&self`

```rust
#[derive(Debug)]
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }

    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
    // 关联函数：在 impl 块中定义不以 self 作为参数的函数
    // 因为它们与结构体相关联。它们仍是函数而不是方法，因为它们并不作用于一个结构体的实例。
    // 例如 String::from
    fn square(size: u32) -> Rectangle {
        Rectangle {
            width: size,
            height: size,
        }
    }
}

fn main() {
    let sq = Rectangle::square(3);  // 关联函数的用法是::不是.
    println!("{:#?}", sq);
    println!("{:#?}", sq.area());
    println!(
        "{:#?}",
        sq.can_hold(&Rectangle {
            width: 1,
            height: 2,
        })
    );
}
```



### **自动引用和解引用**（*automatic referencing and dereferencing*）

Rust 并没有一个与C中 `->` 等效的运算符。当使用 `object.something()` 调用方法时，Rust 会自动为 `object` 添加 `&`、`&mut` 或 `*` 以便使 `object` 与方法签名匹配。因此以下代码是等价的：

```rust
p1.distance(&p2);
(&p1).distance(&p2);
```

