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
