---
layout: post
title:  Rust Learning Notes
date:   2025-05-01 06:21:30
categories: learning-rust
post_id: 13
---

This notes were taken while learning the Rust programming language, most of the notes are from the experimental [Rust Book](https://rust-book.cs.brown.edu/). Other sources maybe added later, the notes are separated by date taken.

## 2025-04-30

### **Rust Book - Chapter 1: Getting Started**

#### **On functions and Macros**

```rust
// This is a comment
fn main() {
    println!("Hello, world!");
}

```

- `fn` is the keyword that defines a function.
- `println!` is a macro that prints to the console.
- `!` indicates that `println` is a macro, not a function.

## 2025-05-02

### **Rust Book - Chapter 1: Getting Started - Cargo**

#### **On Cargo**

```bash

cargo new <project_name> # Create a new Rust project
cargo build # Build the project
cargo run # Build & Run the project
cargo check # Build the project without producing an executable
cargo build --release # Build the project in release mode

```

- Cargo is the Rust package manager and build system.
- It helps manage dependencies, build packages, and run tests.
- The `Cargo.toml` file is where dependencies and package metadata are defined.
- Cargo stores the the resulting build executables in the `target` directory.

## 2025-05-05

### **Rust Book - Chapter 3: Common Programming Concept - Variables and Mutability**

#### **On Variables and Mutability**

```rust
fn main() {
    let x = 5;
    println!("The value of x is: {x}");
    x = 6;
    println!("The value of x is: {x}");
}
```

- In Rust, variables are immutable by default.
- This code will not compile because `x` is immutable by default.
- To make a variable mutable, we use the `mut` keyword:

```rust
fn main() {
    let mut x = 5;
    println!("The value of x is: {x}");
    x = 6;
    println!("The value of x is: {x}");
}
```

- The `mut` keyword makes the variable mutable, allowing us to change its value.

#### Constants

```rust
const THREE_HOURS_IN_SECONDS: u32 = 60 * 60 * 3;
```

- `const` is the keyword used to declare constants instead of `let`
- like immutable variables constants are never allowed to change
- constants can't be made mutable by using the `mut` keyword
- type annotation is required when declaring constants
- constants can only be set to constant expressions and not runtime computed results

#### Shadowing

```rust
fn main(){
    let x = 5;
    let x = x + 1;

    {
        let x = x * 2;
        println!("The value of x in the inner scope is: {x}");
    }

    println!("The value of x is: {x}");

    let spaces = "   ";
    let spaces = spaces.len();

}
```

- rust allows declaring a variable with the name of a previously declared variable this is know as `shadowing`
- when shadowing a previously declared variable, the type can be changed

## 2025-05-08

### **Rust Book - Chapter 3: Common Programming Concept - Data Types**

#### **On Data Types**

```rust
let guess: u32 = "42".parse().expect("Not a number!");

```

converting `String` to a numeric type using `parse` requires explicit type annotation, the compiler can't infer it if not provided and will error at compile time

- Rust is a statically typed language, this means that we must know the type of all variables at compile time
- The compiler can infer the data type, but in cases where type can't be inferred then we must add type annotation like above

### Scalar Types

Rust has for primary scalar types: `integers`, `floats`, `booleans`, and `characters`

#### Scalar Types - Integers

- integer types include `u8`, `i8`, `u16`, `i16`, `u32`, `i32`, `u64`, `i64`, `u128`, `i128` where `u` is for unsigned and `i` is for signed
- type suffix can be used such as `57u8` to represent 8 bit unsigned integer
- visual separators are allowed as well such as `1_525_200` to represent `1525200`
- when compiling in debug mode, rust will check `integer overflow` and `panic` at runtime but in release mode integers will overflow

#### Scalar Types - Floats

```rust

fn main() {
    let x = 2.0; // f64

    let y: f32 = 3.0; // f32
}

```

- in Rust all floating-point types are signed, and there are of two types `f32` and `f64`, the default type is `f64`
- floating point numbers are represented in `IEEE-754` standard. The `f32` type is a single-precision 32-bit float and `f64` is a double-precision 64-bit float

#### Scalar Types - Numeric Operations

Rust supports all basics mathematical operations expected of the number types:

```rust
fn main() {
    // addition
    let sum = 5 + 10;

    // subtraction
    let difference = 95.5 - 4.3;

    // multiplication
    let product = 4 * 30;

    // division
    let quotient = 56.7 / 32.2;
    let truncated = -5 / 3;

    // remainder
    let remainder = 43 % 5;
}

```

#### Scalar Types - Booleans

As in most other programming languages, the boolean type in Rust is `bool` and can be either `true` or `false`

```rust

fn main(){
    let t = true;

    let f: bool = false; // with explicit type annotation
}

```

#### Scalar Types - Character Type

The `char` type is rust most primitive alphabetic type. example:

```rust
fn main(){
    let c = 'z';
    let z: char = 'ℤ'; // with explicit type annotation
    let heart_eyed_cat = '😻';
}
```

- `char` is a 4-byte Unicode scalar value, representing a single character
- `char` can represent more than just ASCII characters
- single quotes are used to represent `char` literals

### **Compound Types**

Rust has two primitive compound types: `tuples` and `arrays`

#### Compound Types - Tuples

```rust

fn main(){
    let tup: (i32, f64, u8) = (500, 6.4, 1);
    let (x, y, z) = tup; // destructuring
    println!("The value of y is: {y}");
    println!("The value of z is: {z}");
    println!("The value of x is: {x}");

    let five_hundred = tup.0;
    let six_point_four = tup.1;
    let one = tup.2;

    // mutable tuple
    let mut x: (i32, i32) = (1, 2);
    x.0 = 0;
    x.1 += 5;
    println!("The value of x is: {x}");
}
```

- tuples are fixed-length and can contain different types
- tuples are created using parentheses `()`
- tuples can be destructured to access their values
- tuples can be mutable

#### Compound Types - Arrays

```rust
fn main(){
    let a = [1, 2, 3, 4, 5];

    let months = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];

    let a: [i32; 5] = [1, 2, 3, 4, 5]; // with explicit type annotation
    let a = [3; 5]; // array of 5 elements, all initialized to 3

    // accessing array elements
    let first = a[0];
    let second = a[1];
}
```

- unlike tuples, must contain the same type
- arrays in rust are fixed-length
- arrays are created using square brackets `[]`
- arrays are useful when you want your data to be allocated on the stack, instead of the heap
- arrays are most useful when you know the size of the data and it will not change

```rust

use std::io;

fn main() {
    let a = [1, 2, 3, 4, 5];

    println!("Enter an array index: ");

    let mut index = String::new();

    io::stdin()
        .read_line(&mut index)
        .expect("Failed to read line");

    let index: usize = index
        .trim()
        .parse()
        .expect("Index entered was not a number");
    
    let element = a[index];
    println!("The value of the element at index {index} is: {element}");
}