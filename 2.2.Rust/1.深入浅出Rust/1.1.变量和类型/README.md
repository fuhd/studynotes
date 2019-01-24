变量声明介绍
================================================================================
**Rust的变量必须先声明后使用**。对于 **局部变量**，最常见的声明语法为：
```rust
let variable: i32 = 100;
```
与传统的`C/C++`语言相比，Rust的变量声明语法不同。这样设计主要有以下几个方面的考虑。
1. 语法分析更容易：**局部变量声明一定是以关键字`let`开头，类型一定是跟在冒号（`:`）的后面**。
2. 方便引入类型推导功能：要声明的变量前置，对它的类型描述后置。因为在变量声明语句中，最重要的是
变量本身，而类型其实是个附属的额外描述，并非必不可少的部分。**如果我们可以通过上下文环境由编译器
自动分析出这个变量的类型，那么这个类型描述完全可以省略不写。Rust一开始的设计就考虑了类型自动推导
功能，因此类型后置的语法更合适**。
3. 模式解构：**`let`语句不光是局部变量声明语句，而且具有`pattern destructure`（模式解构）的
功能**。

**Rust中声明变量缺省是“只读”的**，比如如下程序：
```rust
fn main() {
    let x = 5;
    x = 10;
}
```
会得到这样的编译错误：
```
error[E0384]: cannot assign twice to immutable variable `x`
 --> src/main.rs:3:5
  |
2 |     let x = 5;
  |         -
  |         |
  |         first assignment to `x`
  |         help: make this binding mutable: `mut x`
3 |     x = 10;
  |     ^^^^^^ cannot assign twice to immutable variable

error: aborting due to previous error
```
**如果我们需要让变量是可写的，那么需要使用`mut`关键字**：
```rust
fn main() {
    let mut x = 5;
    x = x * 2;
    println!("{}", x)
}
```
此时，变量`x`才是可读写的。

**实际上，`let`语句在此处引入了一个模式解构，我们不能把`let mut`视为一个组合，而应该将`mut x`
视为一个组合**。

**`mut x`是一个“模式”**，我们还可以用这种方式同时声明多个变量：
```rust
let (mut a, mut b) = (1, 2);
let Point(x: ref a, y: ref b) = p;
```
其中，**赋值号左边的部分是一个“模式”，第一行代码是对`tuple`的模式解构，第二行代码是对结构体的模
式解构**。所以，**在Rust中，一般把声明的局部变量并被始化的语句称为“变量绑定”，强调的是“绑定”的
含义**，与`C/C++`中的“赋值初始化”语句有所区别。

**Rust中，每个变量必须被合理初始化之后才能被使用。使用未初始化变量这样的错误，在Rust中是不可能
出现的（利用`unsafe`做`hack`除外）**。如下示例是不能编译通过的：
```rust
fn main() {
    let x: i32;
    println!("{}", x);
}
```
错误信息为：
```
error[E0381]: borrow of possibly uninitialized variable: `x`
 --> src/main.rs:3:20
  |
3 |     println!("{}", x);
  |                    ^ use of possibly uninitialized `x`

error: aborting due to previous error
```
**编译器会帮我们做一个执行路径的静态分析，确保变量在使用前一定被初始化**。
```rust
fn test(condition: bool) {
    //声明x，不必使用mut修饰
    let x: i32;
    if condition {
        //初始化x，不需要x是mut的，因为这是初始化，不是修改
        x = 1;
        println!("{}", x);
    }
    //如果条件不满足，x没有被初始化
    //但是没有关系，只要这里不使用x就没事
}
```
**类型没有“默认构造函数”，变量没有”默认值“。对于`let x: i32;`，如果没有显式赋值，它就没有被
始化，不要想当然地以为它的值是0**。

**Rust里的合法标识符（包括变量名、函数名、`trait`名等）必须由数字、字母、下划线组成，且不能以
数字开头**。这个规定和许多现有的编程语言是一样的。Rust将来会允许其他`Unicode`字符做标识符，只
是目前这个功能的优先级不高，还没有最终定下来。**另外还有一个`raw identifier`功能，可以提供一
个特殊语法，如`r#self`，让用户可以以关键字作为普通标识符**。这只是为了应付某些特殊情况时迫不得
已的做法。

































dd
