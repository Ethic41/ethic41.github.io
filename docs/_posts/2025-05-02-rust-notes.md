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
