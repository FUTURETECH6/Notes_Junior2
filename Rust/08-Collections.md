[TOC]

## [vector](https://kaisery.github.io/trpl-zh-cn/ch08-01-vectors.html#vector-用来储存一系列的值)

```rust
let v: Vec<i32> = Vec::new();

let v = vec![1, 2, 3];  // 宏

// 放入其中的所有值都是 i32 类型的，而且 Rust 也根据数据做出如此判断，所以不需要 Vec<i32> 注解。
let mut v = Vec::new();
v.push(5);
v.push(6);
v.push(7);
v.push(8);
```

### [读取 vector 的元素](https://kaisery.github.io/trpl-zh-cn/ch08-01-vectors.html#读取-vector-的元素)

```rust
fn main() {
    let v = vec![1, 2, 3, 4, 5];

    let third: &i32 = &v[2];
    println!("The third element is {}", third);  // 也可以直接v[2]或&v[2]

    // 使用 get 方法以索引作为参数来返回一个 Option<&T>，避免 v[2] 不存在
    match v.get(2) {
        Some(third) => println!("The third element is {}", third),
        None => println!("There is no third element."),
    }
}
```



```rust
fn main() {
    let v = vec![1, 2, 3, 4, 5];

    let does_not_exist = &v[100]; // panic
    let does_not_exist = v.get(100); // None
}
```

