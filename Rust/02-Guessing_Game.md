[TOC]

# Example of Guessing Number

## HelloWorld

### Simple Demo

```rust
fn main() {
    println!("Hello, world!");
}
```

`pritln!`是个宏，如果没有`!`则

```shell
$ rustc main.rs
error[E0423]: expected function, found macro `println`
 --> main.rs:2:5
  |
2 |     println("Hello world!");
  |     ^^^^^^^ not a function
  |
help: use `!` to invoke the macro
  |
2 |     println!("Hello world!");
  |            ^

error: aborting due to previous error

For more information about this error, try `rustc --explain E0423`.
```

### Cargo

```shell
$ cargo new $PROJ_NAME
$ cargo check  # just check syntax, no exec file generated
$ cargo build  # default debug build
$ cargo build --release
$ cargo run
$ tree cargo_proj
cargo_proj
├── Cargo.lock
├── Cargo.toml
├── src
│   └── main.rs
└── target
    ├── CACHEDIR.TAG
    ├── debug
    │   ├── build
    │   ├── cargo_proj
    │   ├── cargo_proj.d
    │   ├── deps
    │   │   ├── cargo_proj-523d6ba4b1c6ec55
    │   │   ├── cargo_proj-523d6ba4b1c6ec55.d
    │   │   ├── cargo_proj-818556e59227c5f6.d
    │   │   └── libcargo_proj-818556e59227c5f6.rmeta
    │   ├── examples
    │   └── incremental
    │       ├── cargo_proj-20l6t1i3m0u1f
    │       │   ├── s-fwkap4xkwr-itp24c-3ebjb4214vn4x
    │       │   │   ├── 2msfsxunytabclg4.o
    │       │   │   ├── 32ybqm5qm73v11o.o
    │       │   │   ├── 341birz21ie6e7ch.o
    │       │   │   ├── 3gqxy0ltujttbp3k.o
    │       │   │   ├── 3snxnn7s0yi4xb1r.o
    │       │   │   ├── 53vgom94bs3mhybx.o
    │       │   │   ├── 6ii0y8i05gdnwur.o
    │       │   │   ├── dep-graph.bin
    │       │   │   ├── lw78yw3auyt0to9.o
    │       │   │   ├── query-cache.bin
    │       │   │   └── work-products.bin
    │       │   └── s-fwkap4xkwr-itp24c.lock
    │       └── cargo_proj-3xms3fs7iz6q
    │           ├── s-fwkaq0s37z-1fb00jl-3l3ipedjhhkgc
    │           │   ├── dep-graph.bin
    │           │   ├── query-cache.bin
    │           │   └── work-products.bin
    │           └── s-fwkaq0s37z-1fb00jl.lock
    └── release
        ├── build
        ├── cargo_proj
        ├── cargo_proj.d
        ├── deps
        │   ├── cargo_proj-1e56d3d5ca85ac92
        │   └── cargo_proj-1e56d3d5ca85ac92.d
        ├── examples
        └── incremental

16 directories, 30 files
```

### Result Type

```rust
use std::io;
fn main() {
    println!("Guess the number!");
    println!("Please input your guess.");
    let mut guess = String::new();
    io::stdin()
        .read_line(&mut guess)  // Where return type is `Result` 
        .expect("Failed to read line");
    println!("You guessed: {}", guess);
}
```

如果不使用`expect`，则会

```shell
$ cargo check
    Checking guessing_game v0.1.0 (/mnt/c/Users/Ulysses/OneDrive/Jun_B/Rust/Code/guessing_game)
warning: unused `std::result::Result` that must be used
 --> src/main.rs:6:5
  |
6 | /     io::stdin()
7 | |         .read_line(&mut guess);
  | |_______________________________^
  |
  = note: `#[warn(unused_must_use)]` on by default
  = note: this `Result` may be an `Err` variant, which should be handled

warning: 1 warning emitted

    Finished dev [unoptimized + debuginfo] target(s) in 0.95s
```

### {} in println

### Add Dep

Cargo.toml

```toml
[dependencies]

rand = "0.3.14"
rand = ""
```

**通过Cargo.lock文件确保我们的构建是可重现的**

Cargo提供了一套机制来确保构建结果是可以重现的，任何人在任何时候重新编译我们的代码都会生成相同的产物：Cargo会一直使用某个特定版本的依赖直到你手动指定了其他版本。打个比方，假如我们使用的rand包在下周发布了0.3.15版本，它修复了一个重要的bug，但这个修复会破坏我们现有的代码，这时，我们重新构建项目会发生什么呢？

回答这个问题的关键就是之前一直被忽略的Cargo.lock文件，它在我们第一次使用cargobuild时便在当前的项目目录guessing_game下生成了。当你第一次构建项目时，Cargo会依次遍历我们声明的依赖及其对应的语义化版本，找到符合要求的具体版本号，并将它们写入Cargo.lock文件中。随后再次构建项目时，Cargo就会优先检索Cargo.lock，假如文件中存在已经指明具体版本的依赖库，那么它就会跳过计算版本号的过程，并直接使用文件中指明的版本。这使得我们拥有了一个自动化的、可重现的构建系统。<u>换句话说，在Cargo.lock文件的帮助下，当前的项目将会一直使用0.3.14版本的rand包，直到我们手动升级至其他版本。</u>

**升级依赖包**

当你确实想要升级某个依赖包时，Cargo提供了一个专用命令：update，它会强制Cargo忽略Cargo.lock文件，并重新计算出所有依赖包中符合Cargo.toml声明的最新版本。假如命令运行成功，Cargo就会将更新后的版本号写入Cargo.lock文件，并覆盖之前的内容。

## Core

### Rand

```rust
use rand::Rng;
use std::io;
fn main() {
    println!("Guess the number!");
    let secret_number = rand::thread_rng().gen_range(1, 101);
    println!("The secret number is: {}", secret_number);
    println!("Please input your guess.");
    let mut guess = String::new();
    io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line");
    println!("You guessed: {}", guess);
}
```

首先，我们额外增加了一行use语句：`use rand::Rng`。这里的Rng是一个trait（特征），它定义了随机数生成器需要实现的方法集合。为了使用这些方法，我们需要显式地将它引入当前的作用域中。第10章会详细介绍有关trait的诸多细节。

### Compare

```rust
use rand::Rng;
use std::cmp::Ordering;
use std::io;

fn main() {
    println!("Guess the number!");
    let secret_number = rand::thread_rng().gen_range(1, 101);
    println!("The secret number is: {}", secret_number);
    println!("Please input your guess.");
    let mut guess = String::new();
    io::stdin()
        .read_line(&mut guess)
        .expect("Failed to read line");
    let guess: u32 = guess.trim().parse().expect("Please type a number!"); // `trim`去掉首位空白字符，`parse`解析字符串为数值
    println!("You guessed: {}", guess);
    match guess.cmp(&secret_number) /* `secret_number` must have same type as `guess` */ {
        Ordering::Less => println!("Too small!"),
        Ordering::Greater => println!("Too big!"),
        Ordering::Equal => println!("You win!"),
    }
}
```

但Rust允许使用同名的新变量`guess`来隐藏（shadow）旧变量的值。这一特性通常被用在需要转换值类型的场景中，它在本例中允许我们重用`guess`这个变量名，而无须强行创造出`guess_str`之类的不同的名字。

parse的输入字符串必须是可转换的，不然parse的返回值的Result就是Err型的

### Loop w/o exit

```rust
use rand::Rng;
use std::cmp::Ordering;
use std::io;

fn main() {
    println!("Guess the number!");
    let secret_number = rand::thread_rng().gen_range(1, 101);
    println!("The secret number is: {}", secret_number);
    loop {
        println!("Please input your guess.");
        let mut guess = String::new();
        io::stdin()
            .read_line(&mut guess)
            .expect("Failed to read line");
        let guess: u32 = guess.trim().parse().expect("Please type a number!");
        println!("You guessed: {}", guess);
        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => println!("You win!"),
        }
    }
}
```

### Loop with exit

```rust
use rand::Rng;
use std::cmp::Ordering;
use std::io;

fn main() {
    println!("Guess the number!");
    let secret_number = rand::thread_rng().gen_range(1, 101);
    println!("The secret number is: {}", secret_number);
    loop {
        println!("Please input your guess.");
        let mut guess = String::new();
        io::stdin()
            .read_line(&mut guess)
            .expect("Failed to read line");
        let guess: u32 = guess.trim().parse().expect("Please type a number!");
        println!("You guessed: {}", guess);
        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => {
                println!("Too big!")
            }
            Ordering::Equal => {
                println!("You win!");
                break;
            }
        }
    }
}
```

match里面有了bracket就可以不用逗号分隔了

## Improvement

### Handle illegal input

```rust
use rand::Rng;
use std::cmp::Ordering;
use std::io;

fn main() {
    println!("Guess the number!");
    let secret_number = rand::thread_rng().gen_range(1, 101);

    loop {
        println!("Please input your guess.");
        let mut guess = String::new();
        io::stdin()
            .read_line(&mut guess)
            .expect("Failed to read line");

        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => continue,
        };

        println!("You guessed: {}", guess);
        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Greater => println!("Too big!"),
            Ordering::Equal => {
                println!("You win!");
                break;
            }
        }
    }
}
```

假如`parse`没能将字符串解析为数字，那么它将返回一个包含了具体错误信息的Err值。这个值会因为无法匹配`Ok(num)`模式而跳过`match`表达式的第一个分支，并匹配上第二个分支中的`Err(_)`模式。==这里的下画线`_`是一个通配符，它可以在本例中匹配所有可能的Err值，而不管其中究竟有何种错误信息。==因此，程序会继续执行第二个分支中的代码：`continue`，这条语句会使程序直接跳转至下一次循环，并再次请求玩家猜测保密数字。