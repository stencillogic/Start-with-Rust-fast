# Learn Rust fast

This is a shorter version of the official [Rust tutorial](https://doc.rust-lang.org/book/title-page.html) (The Rust Programming Language book). It may come in handy if you want to start with Rust quickly.

## Table of contents.

- [Installation, update, and uninstallation](#installation-update-and-uninstallation)
- [Creating and building a project](#creating-and-building-a-project)
- [Basic control structures](#basic-control-structures)
  * [Function definition](#function-definition)
  * [Varibale declaration](#varibale-declaration)
- [Control flow](#control-flow)
  * [Conditional expressions](#conditional-expressions)
  * [Loops](#loops)
- [Ownership](#ownership)
  * [References and borrowing](#references-and-borrowing)
  * [Slices](#slices)
- [Structures](#structures)
  * [Named tuples](#named-tuples)
  * [Structure methods](#structure-methods)
- [Enumerations and pattern matching](#enumerations-and-pattern-matching)
  * [Pattern matching](#pattern-matching)
- [Project organization](#project-organization)
- [Basic collections](#basic-collections)
- [Error handling](#error-handling)
- [Generic data types, traits, and lifetimes](#generic-data-types-traits-and-lifetimes)
  * [Generic types](#generic-types)
  * [Traits](#traits)
  * [Trait bound](#trait-bound)
  * [Lifetimes](#lifetimes)
- [Testing](#testing)
- [Closures, iterators, and functional features](#closures-iterators-and-functional-features)
  * [Iterators](#iterators)
- [Project documentation](#project-documentation)
- [Workspaces](#workspaces)
- [Smart pointers](#smart-pointers)
  * [Box](#box)
  * [Deref trait](#deref-trait)
  * [Drop trait](#drop-trait)
  * [Reference counting with Rc<T>](#reference-counting-with-rc)
  * [Interior mutability with RefCell<T>](#interior-mutability-with-refcell)
  * [Avoiding reference cycles with Weak references](#avoiding-reference-cycles-with-weak-references)
- [Concurrency (parallelism)](#concurrency-parallelism)
- [Obect-oriented features](#obect-oriented-features)
  * [Dynamic polimorphism](#dynamic-polimorphism)
- [Patterns and matching](#patterns-and-matching)
- [Advanced Rust](#advance-rust)
  * [Unsafe Rust](#unsafe-rust)
  * [Advanced traits](#advanced-traits)
  * [Advanced types](#advanced-types)
  * [Advanced functions and closures](#advanced-functions-and-closures)
  * [Macros](#macros)

## Installation, update, and uninstallation

To install Rust on Linux:

> curl https://sh.rustup.rs -sSf | sh

> source $HOME/.cargo/env

Update:

> rustup update

Uninstall:

> rustup self uninstall

## Creating and building a project

To create a new project in command prompt call:

> cargo new <project_name>

The command creates directory tree:

```
<project_name>
  |_Cargo.toml          (project description and dependensiec)
  |_.git                (if there is git available)
  |_.gitignore          (if there is git available)
  |_src
    |_main.rs           (stub "Hello world" app)
```

Cargo.toml describes project and has structure:

```
[package]
name = "project_name"
version = "0.1.0"
authors = ["Your Name <you@example.com>"]
edition = "2018"

[dependencies]
<dependency1> = "<version1>"
<dependency2> = "<version2>"
...
```

Optionally it can contain profile sections. By default there is dev and release profiles. They can be overwritten or new profiles can be added, e.g.:

```
...
[profile.dev]
opt-level = 1

[profile.release]
opt-level = 3
...
``` 

To build the project cd to <project_name> directory and call:

> cargo build

Note, linker is not included as part of Rust. External linker is used to build output.
The command creates `target/debug` directory where output (it can be executable or library) is saved.
The build command also creates `Cargo.lock` which containes exact version of dependencies used to build the project. Cargo needs to lock versions because versions of dependencies in `Cargo.toml` are not exact but rather specify expected "compatibility version" while `Cargo.lock` specifies exact version.

Also you can call:

> cargo run                 # build and run the resulting application

> cargo check               # quick build without making output

> cargo build --release     # build the release version of app and put it in `target/release` dir

> cargo doc --open          # generate html documentation from code

> cargo fmt                 # format code in your project 

## Basic control structures

### Function definition

In general:

``` 
"fn" <function_name> "(" [ <arg1_name>: <datatype>, <arg2_name>: <datatype>, ... ] ")" "{"
    <body>
"}"
```

Function body consists of statements and expressions. Statement ends with ";".
Function can return value as expression without ";" at the end.

Examples:

``` rust

// function without arguments and return value
fn some_func1() {
    println!("Hello");   // statement
}

// function with arguments and without return value
fn some_func2(x: u32, y: i8) {
    println!("x = {}, y = {}", x, y);   // print x and y
    return;                             // return from function without return value
}

// function with arguments and return value
fn some_func3(x: u32, y: u32) -> u32 {	// return value datatype
    x + y                              // return value as expression
}

fn some_func3(x: u32, y: u32) -> u32 {
    return x + y;                      // return statement with return keyword
}

```

Functions can be nested, and contain nested blocks e.g.:

``` rust
fn fun_outer() {
    fn fun_inner() {
        ...
    }
    ...

    let x = {        // assigning an expression calculated inside "{" and "}"
        let a = 1;
        x + a        // expression return value
    };               // end of statement
}
```

### Varibale declaration

In general:

```
"let" [ "mut" ] <var_name> [ ":" <datatype> ] [ "=" <expression> ] ";"
```

Examples:

``` rust
// immutable variable x with value 5 and inferred datatype
let x = 5;

// mutable variable, value can be changed
let mut x = 5;

// immutable variable x with value 5 and explicit datatype
let x: i8 = 5;
```


Constants.

In general:

```
"const" <upper_case_name> ":" <datatype> "=" <value> ";"
```

Example:

``` rust
const V: u32 = 100_000;	// 100_000 is 100000
```


Shadowing.

Variable name can be reused in the same visibility scope, e.g.:

``` rust
let x = 5;

let x = x + 1;           // value has changed

let x = x.to_string();   // datatype has changed to String
```

Basic datatypes.

Integers:

| Length | Signed | Unsigned |
|-|-|-|
| 8-bit | i8 | u8 | 
| 16-bit | i16 | u16 | 
| 32-bit | i32 | u32 | 
| 64-bit | i64 | u64 | 
| 128-bit | i128 | u128 | 
| arch | isize | usize |

arch - depending on architecture can be 32 bit or 64 bit.

Integer types allow overflow in release build, but program will panic at overflow in debug build.

f32, f64 - floating poit numbers of 32- and 64-bit length correspondingly.

Number literals:

| Number literals | Example |
|-|-|
| Decimal | 98_222 | 
| Hex | 0xff | 
| Octal | 0o77 | 
| Binary | 0b1111_0000 | 
| Byte (u8 only) | b'A' |
| Floating point | 0.2 |

Datatype can be added at the end of numeric literal, e.g.:

```rust
// 127 as u8
let a = 127u8;

// -1.5e+3 as f32
let b = -1.5e+3f32;
```

bool - boolean datatype. Boolean literals are "true" and "false".

char - single character.

|Literal type|Example|
|-|-|
| Char literal example | 'A' |
| String literla examle | "Hello" |

Strings and characters are utf-8 encoded.

Arrays.

Example:

``` rust
// array of length 10 with elements of type u8
let mut x: [u8;10] = [1,2,3,4,5,6,7,8,9,0];

// accessing array element of mutable array
x[0] = 1;

// create array of length 5 and assign 3 as a value for each element
let arr = [3;5];
```

Length of array must be known at compile time. Array is allocated on stack.


Tuples.

Example:

``` rust
// Tuple x
let x: (u32, i8, char) = (12, 0, 'A')

// accessing tuple x elements
let el0 = x.0;
let el1 = x.1;

// expanding tuple x to variables
let (u, i, c) = x;
```

Datatype casting can be performed using `as` keyword:

```rust
let a = 12345u32;

let b = (a as f32) / 2.7;

// accessing fields
println!("{} {}", a, b);
```

## Control flow

### Conditional expressions

Examples:

``` rust
// basic if then else
let number = 3;

if number < 5 {
    println!("condition was true");
} else {
    println!("condition was false");
}
```

``` rust
// if then else with several paths
let number = 3;

if number < 5 {
    ...
} else if number > 10 {
    ...
} else {
    ...
}
```

``` rust
// Assigning result to variable
let condition = true;

let number = if condition {
    5
} else {
    6
};
```

### Loops

Infinite loop:

``` rust
loop {
   ...
};
```

Keywords break and continue are available to control the flow.
`break` allows exit the loop. 
`continue` skips the remaining part of iteration.

Exit the loop with a value:

``` rust
let mut counter = 0;

let result = loop {
    counter += 1;

    if counter == 10 {
        break counter * 2;
    }
};
```


While loop:

``` rust
while <condition> {
   ...
}
```

For loop:

``` rust
for <element> in <itarator> {
    ...
}
```

Example:
``` rust
for i in 1..5 {
    println!("{}", i);
}
```


## Ownership

Ownership differentiates Rust from other programming languages.

Ownership Rules:
 - Each value in Rust has a variable that’s called its owner.
 - There can only be one owner at a time.
 - When the owner goes out of scope, the value will be dropped.

Ownership doesn't apply to simple datatypes, e.g.:

``` rust
let x = 5;
let y = x;

// x stays in scope and is accessible
println!("{}", x);
```

In contrast:

``` rust
let s1 = String::from("hello");
let s2 = s1;

// s1 is not availalbe anymore becuase String is not basic datatype
```

In case you need both you can clone the object:

``` rust
let s1 = String::from("hello");
let s2 = s1.clone();

// s1 and s2 are both available
println!("s1 = {}, s2 = {}", s1, s2);
```

Ownership when calling a function:

``` rust
let s1 = "hello".to_string();

// calling function and passing s1 as argument
some_function(s1);

// s1 is now out of the scope and is not available

let x = 5;

// calling other function and passing x as argument
other_function(x);

// x is still available
println!("{}", x);
```

Transferring ownership when returning value:

``` rust
fn ret_some_string() -> String {
    let some_string = String::from("hello");
    some_string                              // the caller will become the owner of this String instance
}
```


### References and borrowing

Like in C++ you can use a reference to an object or variable.

The Rules of References:

 - At any given time, you can have either one mutable reference or any number of immutable references.
 - References must always be valid (point to existing object).

Example:

``` rust
// two readers
let x: u32 = 5;
let r1: &u32 = &x;
let r2 = &x;

// mutable reference and it's use
let mut y: f32 = 0.3;
let r3: &mut f32 = &mut y;  // no more references of y are allowed after this
 
*r3 += 1.0;          // using reference to change the value of variable "y"

y *= 0.5;            // "y" can be used here only because r3 is never 
                     // used after this line and compiler recognizes such situation!

println!("{}", y);   // prints "3"
```

Passing reference as argument to a function:

``` rust
fn main() {
    let s1 = String::from("hello");

    let len = calculate_length(&s1);    // instead of object itself a reference (pointer) to String is passed

    println!("The length of '{}' is {}.", s1, len);
}

fn calculate_length(s: &String) -> usize {   // function accepts reference to a String
    s.len()                                  // s.len() is identical to (*s).len() here
}
```

It is important to understand difference between data on stack and on the heap. E.g.:

``` rust
fn ret_some_ref() -> &String {
    let s = "a".to_string();
    &s                           // doesn't work because object "s" allocated on stack is deleted after function returns
}
```

### Slices

Slice is a reference to a continuous part of a string or an array (substring in other words). Slice can be represented by a pointer and a length of the referenced data chunk inside string or array.

Example :

``` rust
let s = String::from("hello world");

let hello: &str = &s[0..5];       // slice 1
let world = &s[6..11];            // slice 2

let slice_from_start = &s[..5];   // slice from the beginning and up to 5
let slice_till_end = &s[5..];     // slice from 5 and up to the end
let full_slice = &s[..];          // slice of whole string
```

Slice is immutable. When you take a slice of a mutable string for example you can't modify the string if slice is still in scope. It follows reference rule: if there is a reader there can't be writers.

String slice range indices must occur at valid UTF-8 character boundaries. Program will exit with an error if slice doesn't follow this rule.

String literals in Rust are slices, e.g.:

``` rust
let s: &str = "String literal value";  // "s" is a slice
```


Array slice:

``` rust
let a = [1, 2, 3, 4, 5];

let slice = &a[1..3];

// with data types
let a: [i32;5] = [1, 2, 3, 4, 5];

let slice: &[i32] = &a[1..3];
```

## Structures

Example:

``` rust

// definition
struct User {
    username: String,
    email: String,
    sign_in_count: u64,
    active: bool,
}

// creating instance
let mut user = User {
    username: "Andy".to_string(),
    email: "andy@andy.org".to_string(),
    sign_in_count: 0,
    active: true,
};

// access elements
let mut cnt = user.sign_in_count;
cnt += 1;
user.sign_in_count = cnt;

// shorthand initialization using variables with identical names
let username = "Kris".to_string();
let email = "-".to_string();
let user1 = User {
    username,
    email,
    sign_in_count: 0,
    active: true,
};

// shorthand initialization using second instance
let user2 = User {
    username: "Andy".to_string(),
    email: "andy@andy.org".to_string(),
    ..user1
};

// Unit structure can be used as a marker
struct SomeUnitStruct;
```

Single struct members cannot be mutable, only whole struct can be mutable.

### Named tuples

Example:

``` rust
// declaration
struct MyData1(u32, char, bool);
struct MyData2(u32, char, bool);  // MyData2 is different data type than MyData1

let data1 = MyData1(1, 'A', true);

// accessing fields
println!("f0: {},  f1: {}, f2: {}", data1.0, data1.1, data1.2);
```

### Structure methods

Example:

``` rust
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    // we need to pass self reference here like e.g. in Python
    fn area(&self) -> u32 {
        self.width * self.height
    }

    // associated function (like static method in C++ or Java) has no self pointer
    fn square(size: u32) -> Rectangle {
        Rectangle { width: size, height: size }
    }

    // self and other parameters
    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}

fn main() {
    let rect1 = Rectangle { width: 30, height: 50 };

    println!(
        "The area of the rectangle is {} square pixels.",
        rect1.area()
    );
    
    let square = Rectangle::square(4);

    println!("Area of the square is {}", square.area());

    let rect2 = Rectangle { width: 60, height: 45 };

    println!("Can rect1 hold rect2? {}", rect1.can_hold(&rect2));
}
```

We can create several `impl` blocks and put them in separate files.


## Enumerations and pattern matching

Enum has a set of predefined values. Each value can have associated value, structure, or tuple.

Example:

``` rust
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}

// enum can have methods too
impl Message {
    fn call(&self) {
        // method body would be defined here
    }
}

let quit_msg = Message::Quit;
let move_msg = Message::Move {x: 0, y: 0};
let write_msg = Message::Write("Here is Johnny".to_string());
let change_color_msg = Message::ChangeColor(255,0,255);
```

Predefined Option enum.

`Option` enum is used to replace null value in Rust. Definition:

``` rust
enum Option<T> {
    Some(T),
    None,
}
```

Option also has a set of methods for extracting value from `Some` and do other stuff.

### Pattern matching

Match operator (has similarities to Scala pattern matching or switch operator in C).
Example:

``` rust
enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter,
    Unknown,
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        // match arms
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
        _ => 0                // default arm with placeholder "_"
    }
}

// matching with variable and exact value
fn plus_one(x: Option<i32>) -> Option<i32> {
    match x {
        None => None,
        Some(5) => {
           println!("We don't like fives");
           -1
        }
        Some(i) => Some(i + 1),  // order matters in this example
                                 // Some(5) must be checked first
    }
}
```

`match` evaluates expression and returns value of just one matched arm.

`if let` operator is a form of pattern matching expression. Example:

``` rust
// the condition will hold true if some_u8_value is Option::Some and not Option::None
if let Some(i) = some_u8_value {
    println!("{}", i); 
}

// else is analog of "_" placeholder
let mut count = 0;
if let Coin::Quarter(state) = coin {
    println!("State quarter from {:?}!", state);
} else {
    count += 1;
}
```

## Project organization

Packages.

Packages is a Cargo feature that lets you build, test, and share _crates_. 
Package is a set of crates united by common functionality. 

Crates.

Crate is a tree of _modules_ that produces a _library_ or an _executable_. 
There can be several binaries in a crate but just one library, or a library and several binaries.

Modules.

Modules let you control the organization, scope, and privacy of paths in the module tree.

Paths. 

Paths is a way of naming an item, such as a struct, function, or module, e.g.: `std:io:Error`

Command `cargo new` create a package. 
Cargo uses filenames to distinguish binary and library crate. `main.rs` is a sign of binary crate, `lib.rs` is a sign of library.

Modules let you organize code, e.g.:

``` rust
mod submod {
    mod subsubmod {
        fn subfunc() {
        }
    }

    fn func() {
    }
}

fn main() {
}
```

By default contents of modules is private. To make it accessible you need to put `pub` keyword before module element, e.g.:

``` rust
mod submod {
    mod subsubmod {
        fn subfunc() {
        }
    }

    pub fn func() {
    }
}

fn main() {
    // this is relative path
    submod::func();

    // this is an absolute path
    crate::submod::func();
}
```

`submod` is accessible without `pub` becuase it is on the same level as function `main`.
`subsubmod` and `subfunc` are not accessible from `main`.

`use` keyword brings symbols from submodules to the current scope, e.g.:

``` rust
mod submod {
    pub mod subsubmod {
        pub fn subfunc() {
        }
    }

    pub fn func() {
    }
}

use submod::subsubmod;

fn main() {
    subsubmod::subfunc();
}
```

At the same time child modules can access parent module without restriction.
`super` keyword can be used to access elements from the parent module, e.g.:

``` rust
fn func() {}

mod submod {
    fn subfunc() {
        super::func();
    }
}
```

`as` keyword can be used to alias imported name, e.g.:

``` rust
use std::fmt::Result;
use std::io::Result as IoResult;
```

`pub use` combination can be used to reexport name imported in current scope to external consumers, e.g.:

``` rust
pub use crate::submod::subsubmod;
```

Nesting and globs:

```rust
use std::io;
use std::cmp::Ordering;
```

is equvalent of:

```rust
use std::{cmp::Ordering, io};
```

And:

``` rust
use std::io;
use std::io::Write;
```

is equvalent of:

``` rust
use std::io::{self, Write};
```

Also glob operator can be used:

``` rust
use std::collections::*;
```

If we use `pub` for a whole structure the structure becomes visible, but it's fields are not visible. To make a struct field visible `pub` must be applied to the field. E.g.:

``` rust
mod submod {
    pub struct Structure {
        pub f1: String,       // visible in main()
        f2: String,           // not visible in main()
    }

    impl Structure {
        pub fn make(f1: &str, f2: &str) -> Structure {  // visible in main()                                                                                        
            Structure {
                f1: f1.to_string(),
                f2: f2.to_string()
            }
        }
    }
}

pub fn main() {
    let s = submod::Structure::make("a", "b");
    s.f1
}
```

In case of enum all variants become visible at the same time, e.g.:

``` rust
mod submod {
    pub enum MyEnum {
        Variant1,            // visible in main()
        Variant2,            // visible in main()
    }
}

fn main() {
    ...
}
```

Subdirectories and files can be used to organize modules, .e.g:

``` rust
mod submod {
    pub mod subsubmod {
        pub fn subfunc() {
            println!("Subfun called");
        }
    }

    pub fn func() {
        println!("Func is called");
    }
}

use submod::subsubmod;

fn main() {
    subsubmod::subfunc();
}
```

can be transformed into:

src
  |_main.rs
  |_submod.rs
  |_submod
    |_subsubmod.rs

where main.rs contains:

``` rust
mod submod;

use submod::subsubmod;

pub fn main() {
    subsubmod::subfunc();
}
```

submod.rs contains:

``` rust
pub mod subsubmod;

pub fn func() {
    println!("Func is called");
}
```

subsubmod.rs contains:

``` rust
pub fn subfunc() {
    println!("Subfun called");
}
```

## Basic collections

Collections store data on the heap and thus resizable.

Vector.

Vector is a resizable array of elements of certain type.

Usage examples:

``` rust
// create vector
let v: Vec<i32> = Vec::new();

// create vector using macro, datatype is iferred from the data
let mut v = vec![1, 2, 3];

// add an element
v.push(4);

// modify element
v[0] = -1;

// modify element by it's reference
let mut el_ref: &mut i32 = &mut v[2];
*el_ref -= 2;

// read an element; if index is out of bound program panics
let el: i32 = v[2];

// read using option
match v.get(2) {
    Some(el) => { println!("{}", el); }
    None => ()
}

// iteration
for i in &v {
    println!("{}", i);
}

// iteration with update
for i in &mut v {
    *i += 50;
}

```
To store elements of different types or structure `enum` or `trait` can be used.


String.

String allows to store and manipulate text strings. `String` datatype stores utf-8 encoded strings. For other encodings other datatypes exist.

Usage example:

``` rust
// initialization
let mut s = String::new();
let mut s = "some string".to_string();
let mut s = String::from("some string");

// append slice
s.push_str(" append");

// append char
s.push('a');

// adding strings
let s1 = String::from("first");
let s2 = String::from(" second");
let s3 = s1 + &s2 + "abc";        // s1 is moved to s3 and is not usable after this line

// deref coercion
let s = "string".to_string();
let r: &str = &s;                 // &String is assigned to &str

// format! macro
let formatted: String = format!("{} {}", s2, s3);

// indexing; can take only rage longer than 1 byte
// operates on bytes, not characters
let ss = "testing";
let s = &ss[0..4];

// iteration on utf-8 code points
for c in "testing".chars() {
    println!("{}", c);
}

// iteration on bytes
for c in "testing".bytes() {
    println!("{}", c);
}
```

HashMap.

HashMap is an associative array backed by hash table.

Usage example:

``` rust
use std::collections::HashMap;

let mut hm = HashMap::new();

// keys and values have fixed datatypes
// datatypes here are inferred automatically from the data
hm.insert(String::from("Blue"), 10);
hm.insert(String::from("Yellow"), 50);

// check if entry with a key exists and add if doesn't
hm.entry(String::from("Yellow")).or_insert(50);

// get value be a key
let val = hm.get("Yellow");

// iterate
for (key, value) in &hm {
    println!("{}: {}", key, value);
}

// overwrite entry with a new value
hm.insert(String::from("Yellow"), 100);

// update value
let entry = hm.entry(String::from("Yellow")).or_insert(0);
*entry += 10;
```

## Error handling

Macro `panic!` can be used to abort program with error.

``` rust
fn main() {
    panic!("crash!");
}
```

Command `RUST_BACKTRACE=1 cargo run` can be used for debug version of app to show stack backtrace on panic event.

For generating recoverable error enum `Result` is used:

``` rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

Instance of `Result` has several useful methods:

``` rust
use std::fs::File;

// return value of Ok in case of Ok, and panic in case of Err with custom message
let f1 = File::open("log.txt").expect("Error message");

// return value of Ok in case of Ok, and panic in case of Err
let f2 = File::open("log.txt").unwrap();

// open file or return different value on error
let f3 = File::open("log.txt").unwrap_or_else(|_| -> File {f2});
```

Error propagation.

`?` operator can be used inside function returning `Result` type.
The `?` placed after a `Result` value will interrupt execution in case of Err and function will return Err result. If the `Result` value is Ok `?` returns unwrapped value of Ok and processing continues.

Retrun type of the function can differ from Err type passed to `?` operator. 
Error values that have the `?` operator called on them go through the `from` function defined in the `From` trait in the standard library, which is used to convert errors from one type into another.

Example:

``` rust
use std::io::Error;
use std::fs::File;

fn test() -> Result<File, std::Error> {
  let f = File::open("file.txt")?;       // generates std::io::Error on error
  Ok(f)
}
```

`eprintln!` macro can be sued to print to stderr in runtime.


## Generic data types, traits, and lifetimes

### Generic types

Generic data types can be used in functions, structures, and enumerations.
Basic usage examples:

``` rust
// generic datatype in a function
fn my_fun<T>(arg: T) -> T {
    println!("Sample fun called");
    arg
}


// generic datatype in sctructure
struct MyStruct<T> {
    f: T,
}

// generalized implementation
impl<T> MyStruct<T> {
    fn test(&mut self, v: T) -> &T {
        println!("General implementation");
        self.f = v;
        &self.f
    }
}


// implementation for an exact type
impl MyStruct<f32> {
    fn test_f32(&mut self, v: f32) -> &f32 {
        println!("Method specific to f32 type");
        self.f = v;
        &self.f
    }
}

fn main() {

    // implicit type inference
    my_fun(1);

    // explicit type specification
    my_fun::<f32>(1.0);


    // structure instantiation and method calls
    let mut x = MyStruct { f: 0 };

    x.test(10);

    let mut y: MyStruct<f32> = MyStruct { f: 0.0 };

    y.test(1.0);
    y.test_f32(2.0);
}
```

Multiple generic types can be used:

``` rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```
Generic data types do not have influence on performance of resulting code.

### Traits

Trait defines specific behaviour of a type. Trait is similar to interface in Java.

Trait can have methods without implementation or with default implementation. Not-implemented methods of a trait can be called inside other methods of the trait with default implementation.

Coherence (or orphan rule): we can't implement trait defined in a different crate for data type defined in an another different create. We can implement a trait for a data type if one of them is local to our crate or both of them are inside our crate.

Examples:

``` rust
// trait definition
pub trait Summary {
    fn summarize(&self) -> String;
}

// a type
pub struct NewsArticle {
    pub headline: String,
    pub location: String,
    pub author: String,
    pub content: String,
}

// implementation of the trait method
impl Summary for NewsArticle {
    fn summarize(&self) -> String {
        format!("{}, by {} ({})", self.headline, self.author, self.location)
    }
}

// another type
pub struct Tweet {
    pub username: String,
    pub content: String,
    pub reply: bool,
    pub retweet: bool,
}

// another implementation
impl Summary for Tweet {
    fn summarize(&self) -> String {
        format!("{}: {}", self.username, self.content)
    }
}

```

Calling of a trait method example:

``` rust
let tweet = Tweet {
    username: String::from("horse_ebooks"),
    content: String::from("of course, as you probably already know, people"),
    reply: false,
    retweet: false,
};

println!("1 new tweet: {}", tweet.summarize());
```

Default implementation of a trait method example:

``` rust
// a trait with default method implementation
pub trait Summary {
    fn summarize(&self) -> String {
        String::from("(Read more...)")
    }
}

// a type
pub struct NewsArticle {
    pub headline: String,
    pub location: String,
    pub author: String,
    pub content: String,
}

// implementation of the trait with default implementation for the type
impl Summary for NewsArticle {}
```

Calling not-implemented method inside other method of a trait example:

``` rust
pub trait Summary {
    fn summarize_author(&self) -> String;

    fn summarize(&self) -> String {
        format!("(Read more from {}...)", self.summarize_author())
    }
}
```

### Trait bound

Generic type can be bound to certain traits. In that case generic type must implement those traits.

Examples:

``` rust
// full syntax
pub fn notify<T: Summary>(item: T) {
    println!("Breaking news! {}", item.summarize());
}

// simplified syntax
impl Summary for Tweet {
    fn summarize_author(&self) -> String {
        format!("@{}", self.username)
    }
}

// multiple trait syntax
pub fn notify(item: impl Summary + Display) {
    ...
}

pub fn notify<T: Summary + Display>(item: T) {
    ...
}

// trait bound with where keyword makes code more readable
fn some_function<T, U>(t: T, u: U) -> i32
    where T: Display + Clone,
          U: Clone + Debug
{
    ...
}

// return type that implement traits
fn returns_summarizable() -> impl Summary {
    ...
}

// conditional method implementation:
// the following method of MyStruct is implemented only for types that 
// implement both Display and PartialOrd traits
impl<T: Display + PartialOrd> MyStruct<T> {
    fn my_method(&self) {
        ...
    }
}
```

Blanket implementation is an implementation for generic type.

Example:

``` rust
trait MyTrait {
    fn get_info(&self) -> String {
        "some info".to_string()
    }
}

trait InfoPrinter {
    fn print_info(&self);
}

// blanket implementation of InfoPrinter for any type that implements MyTrait
impl<T: MyTrait> InfoPrinter for T {
    fn print_info(&self) {
        println!("{}", self.get_info());
    }
}

struct MyStruct {}

impl MyTrait for MyStruct {}

fn main() {

    let s = MyStruct {};

    // we have not specified explicitly that we implement InfoPrinter 
    // yet we can call it's method because there is blanket implementation
    s.print_info();
}
```

### Lifetimes

_Lifetime_ is a characteristic of a reference. Lifetime defines scope of function arguments which are of reference type and return value if it is of reference type. Sometimes compiler can infer lifetime automatically, but in other situations compiler might not know what is an assumed lifetime of references coming into and going out of a function.

Syntax:

``` rust
&i32        // a reference
&'a i32     // a reference with explicit lifetime
&'a mut i32 // a mutable reference with explicit lifetime
```

Example:

``` rust
// we tell compiler that the result of the function
// will live as long as arguments of the function
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}

fn main() {
    let string1 = String::from("long string is long");

    {
        let string2 = String::from("xyz");

        // result can be a reference to string1 or string2 
        // which one is not known at compile time
        // for that, explicit lifetime in the function definition is used to explicitly
        // tell compiler that the lifetime for both arguments and return value is the same
        let result = longest(string1.as_str(), string2.as_str());
        println!("The longest string is {}", result);
    }
}
```

We can't specify reference as data type of struct field unless we use lifetime.
Example:

``` rust
struct ImportantExcerpt<'a> {
    part: &'a str,                // wouldn't work without lifetime
}

fn main() {
    let novel = String::from("Call me Ishmael. Some years ago...");
    let first_sentence = novel.split('.')
        .next()
        .expect("Could not find a '.'");

    // first_sentence exists before ImportantExcerpt instance is initialized 
    // and does not go out of scope before ImportantExcerpt instance
    let i = ImportantExcerpt { part: first_sentence };
}
```

Compiler uses three rules to figure out what lifetimes references have when there are no explicit lifetimes specified. THese are 
_lifetime elision rules_:

1. Each parameter that is a reference gets its own lifetime parameter. In other words, a function with one parameter gets one lifetime parameter: `fn foo<'a>(x: &'a i32);` a function with two parameters gets two separate lifetime parameters: `fn foo<'a, 'b>(x: &'a i32, y: &'b i32);` and so on.

2. If there is exactly one input lifetime parameter, that lifetime is assigned to all output lifetime parameters: `fn foo<'a>(x: &'a i32) -> &'a i32`.

3. If there are multiple input lifetime parameters, but one of them is `&self` or `&mut self` because this is a method, the lifetime of self is assigned to all output lifetime parameters.

The rules help to make code more concise.
Examples:

``` rust
fn first_word(s: &str) -> &str {
```

will become:

```rust
fn first_word<'a>(s: &'a str) -> &'a str {
```

``` rust
fn longest(x: &str, y: &str) -> &str {
```

will become:

``` rust
fn longest<'a, 'b>(x: &'a str, y: &'b str) -> &str {
```

And for method:

``` rust
impl<'a> ImportantExcerpt<'a> {
    // first lifetime elision rule gives both &self and announcement 
    // their own lifetimes. Then, because one of the parameters is &self,
    // the return type gets the lifetime of &self
    fn announce_and_return_part(&self, announcement: &str) -> &str {
        println!("Attention please: {}", announcement);
        self.part
    }
}
```

`'static` is a special lifetime which means that this reference can live for the entire duration of the program. All references to string literals have `'static` lifetime.


## Testing

Rust provides means for unit-testing and integration testing.

Unit tests are usually created in the same file with tested functionality. E.g.

``` rust
fn some_func(arg: i32) -> i32 {
    println!("Hello");
    arg
}

// unit tests reside inside module with annotation "cfg"
#[cfg(test)]
mod tests {
    // bring some_func into scope of this test module
    use super::*;

    // non-test function
    fn some_setup() {
        println!("I do nothing");
    }

    // some test func
    #[test]
    fn test1() {
        let v = some_func(2);
        assert!(v*v == v*v, "For some reason {} x {} not equal {}", v, v, v*v);
        assert_eq!(v, v, "additional message goes here");
        assert_ne!(v, v+1, "additional message goes here");
    }

    // some test func which should panic with certain message; then test succeeds.
    // Value of expected argument in should_panic annotation treated as substring 
    // of panic message thrown by the test
    #[test]
    #[should_panic(expected="Out of memory")]
    fn test2() {
        panic!("We have problem: Out of memory!");
    }

    // test can return Result<(), String> instead of panicing
    #[test]
    fn test3() -> Result<(), String> {
        if 2 + 2 == 4 {
            Ok(())
        } else {
            Err(String::from("two plus two does not equal four"))
        }
    }
    
    // this test with ignore annotation will be skipped
    #[test]
    #[ignore]
    fn ignored_test() {
        panic!("I was not ignored!");
    }
}
```

Assertion macros should be used in unit-tests. Available macros include:

 - `assert!`: accepts boolean argument of type bool. Test fails if argument is false.
 - `assert_eq!`: accepts two arguments and uses `==` operator to check if arguments are equal, prints the values if test fails. Arguments must implement `PartialEq` and `Debug` traits.
 - `assert_ne!`: accepts two arguments and uses `!=` operator to check if arguments are equal, prints the values if test fails. Arguments must implement `PartialEq` and `Debug` traits.

Running all tests:

> cargo test

Running specific tests:

> cargo test test1

Cargo searches for substring in test name and, if it matches, test is executed, i.e. `cargo test test1` would also run `test12`, `test1_3`, etc.

By default tests run in parallel in several threads. It can lead to contention on some shared resources, e.g. files. The number of threads is configurable as well:

> cargo test -- --test-threads=1

By default stdout of successful tests is suppressed. To print stdout of successful tests use:

> cargo test -- --nocapture

Run tests with `ignore` annotation:

> cargo test -- --ignored

_Integration tests_ should be placed in a separate directory named `tests` in the root of your project. Each file in tests directory is a separate crate representing integration test. Functions which are common to all integration tests can be put in `tests/<some_dir>/mod.rs`. `tests/<some_dir>/mod.rs` will not be interpreted as integration test.

In case of binary package it is not possible to perform integration tests agains it. It is common practice to make library package and pull almost everything inside library. Along with it, create a binary package with small amout of functionlality which uses the library.


## Closures, iterators, and functional features

Closure syntax:

"|" [ <param_name> [ ":" <data_type> ] ] { "," <param_name> [ ":" <data_type> ] } "|" [ "->" <data_type> ] <expression>

Examples:

``` rust
// standard function
fn  add_one_v1   (x: u32) -> u32 { x + 1 }

// similar closures
// each of add_one_v variables below has unique anonymous type
let add_one_v2 = |x: u32| -> u32 { x + 1 };
let add_one_v3 = |x|             { x + 1 };
let add_one_v4 = |x|               x + 1  ;
```

Closures are similar to functions, but data type specification is not mandatory becuase compiler can always infer data type.
Data type can be inferred just once, at first use in code, e.g.:

``` rust
let example_closure = |x| x;

let s = example_closure(String::from("hello"));    // after this line example_closure can accept only string arguments
```

Closures can capture environment, e.g.:

``` rust
fn main() {
    let x = 4;

    let add_to_x = |z| z + x;

    let y = 2;

    assert_eq!(add_to_x(y), 6);
}
```

Closures (and functions) implement traits `FnOnce`, `FnMut`, and `Fn`:

 - `FnOnce` consumes the variables it captures from its enclosing scope, known as the closure’s environment. To consume the captured variables, the closure must take ownership of these variables and move them into the closure when it is defined. The "Once" part of the name represents the fact that the closure can’t take ownership of the same variables more than once, so it can be called only once.
 - `FnMut` can change the environment because it mutably borrows values.
 - `Fn` borrows values from the environment immutably.

Example:

``` rust
// struct has field which has type implementing Fn trait
struct MyStruct<T>
    where T: Fn(i32) -> i32
{
    closure_field: T
}

let my_closure = |x| x;

let s = MyStruct { closure_field: my_closure, };

println!("{}", (s.closure_field)(0));
```

`move` keyword can be used to forcibly move captured environment to closure, e.g.:

``` rust
let x = vec![1, 2, 3];
let equal_to_x = move |z| z == x;    // x is moved out of scope and can't be used after this line
```

`move` is useful for passing closure with captured environment to a different thread.

### Iterators

_Iterator_ allows to perform tasks on sequences of items.

Examples:

``` rust
// define vector of integers
let v1 = vec![1, 2, 3];

// get mutable iterator over vector elements
// if iterator is not mutable method next cannot be called
let mut v1_iter = v1.iter();

// use method next to fetch items
// method next returns immutable reference to a value
assert_eq!(v1_iter.next(), Some(&1));
assert_eq!(v1_iter.next(), Some(&2));
assert_eq!(v1_iter.next(), Some(&3));
assert_eq!(v1_iter.next(), None);

// to iterate over items once more a new iterator is required
// getting a new immutable iterator
let v1_iter = v1.iter();

// use iterator in for loop
// for loop converts iterator to mutable imlicitly
for val in v1_iter {
    println!("Got: {}", val);
}
```

Any iterator implements trait `Iterator` with definition:

``` rust
pub trait Iterator {
    type Item;

    fn next(&mut self) -> Option<Self::Item>;

    // methods with default implementations are elided.
    // The trait also provides default implementations for:
    //   collect - make a collection, 
    //   sum - calculate sum of all items, 
    //   map - perform calculation on every item
    //   filter - filter out some items
    //   etc.
}
```

Example of using `Iterator` trait:

``` rust
// struct for counting 
struct Counter {
    count: u32,
}

impl Counter {
    fn new() -> Counter {
        Counter { count: 0 }
    }
}

// iterator implementation
impl Iterator for Counter {
    type Item = u32;

    // give out integers from 1 and up to 5
    fn next(&mut self) -> Option<Self::Item> {
        self.count += 1;

        if self.count < 6 {
            Some(self.count)
        } else {
            None
        }
    }
}

// usage example
fn main() {
    let d = 3;
    let sum: u32 = Counter::new().zip(Counter::new().skip(1))  // zip produces tuples (a,b)
                                 .map(|(a, b)| a * b)          // map uses closure to process items of iterator
                                 .filter(|x| x % d == 0)       // in filter closure captures d from outer scope
                                 .sum();
    assert_eq!(18, sum);
}
```

Use of iterators does not introduce additional performance penalty compared to loops.

## Project documentation

`///` is used to put documentation on functions.
`//!` is used to put documentation on crate or module as a whole.

`///` is interpreted as markdown and can contain sections:

 - Examples: usage examples. `cargo test` runs not only unit and integration tests but also code of this section.
 - Panics: The scenarios in which the function being documented could panic.
 - Errors: If the function returns a Result, describes the kinds of errors that might occur and what conditions might cause those errors.
 - Safety: If the function is unsafe to call there should be a section explaining why the function is unsafe and covering the invariants that the function expects callers to uphold.

Examples:

``` rust
/// Adds one to the number given.
///
/// # Examples
///
/// ```
/// let arg = 5;
/// let answer = my_crate::add_one(arg);
///
/// assert_eq!(6, answer);
/// ```
pub fn add_one(x: i32) -> i32 {
    x + 1
}
```

``` rust
//! # My Crate
//!
//! `my_crate` is a collection of utilities to make performing certain
//! calculations more convenient.

...
```

## Workspaces

Project can be organized as _workspace_. E.g.:

```
<workspace_dir>
├── Cargo.lock
├── Cargo.toml
├── add-one
│   ├── Cargo.toml
│   └── src
│       └── lib.rs
├── adder
│   ├── Cargo.toml
│   └── src
│       └── main.rs
└── target
```

Workspace has Cargo.lock and Cargo.toml in the root of the workspace folder. The files are common to all creates inside the workspace.
Workspace also has single target directory.

`<workspace_dir>/Cargo.toml` has contents:

```
[workspace]

members = [
    "adder",
    "add-one",
]
```

Cargo.toml in subdirectories has contents typical to any package.

## Smart pointers

Smart pointers allow to allocate data on heap, count references, free up memory automatically, and do other things.
Basic smart pointers:

 - `Box<T>` is for allocating values on the heap.
 - `Rc<T>`, a reference counting type that enables multiple ownership.
 - `Ref<T>` and `RefMut<T>`, accessed through `RefCell<T>`, a type that enforces the borrowing rules at runtime instead of compile time.

They have the following properties and use-cases:

 - `Rc<T>` enables multiple owners of the same data; `Box<T>` and `RefCell<T>` have single owners.
 - `Box<T>` allows immutable or mutable borrows checked at compile time; `Rc<T>` allows only immutable borrows checked at compile time; `RefCell<T>` allows immutable or mutable borrows checked at runtime.
 - Because `RefCell<T> `allows mutable borrows checked at runtime, you can mutate the value inside the `RefCell<T>` even when the `RefCell<T>` is immutable.

### Box

Boxes are used in the following situations:
 - When you have a type whose size can't be known at compile time and you want to use a value of that type in a context that requires an exact size
 - When you have a large amount of data and you want to transfer ownership but ensure the data won’t be copied when you do so
 - When you want to own a value and you care only that it’s a type that implements a particular trait rather than being of a specific type

Smart pointers implement the `Deref` and `Drop` traits. `Deref` trait allows to convert smart pointer to regular reference. `Drop` trait defines destructor for a type. When value of the type goes out of scope destructor for that value is called.

Examples:

```rust
// recursive data structure
#[derive(Debug)]
enum List {
    Cons(i32, Box<List>),           // can't reference List directly without Box becuase size 
                                    // of the structure is not known at compile time
    Nil,
}

use crate::List::{Cons, Nil};

fn main() {
    let list = Cons(1,
        Box::new(Cons(2,
            Box::new(Cons(3,
                Box::new(Nil))))));

    // "{:?}" prints type implementing Debug trait
    println!("{:?}", list);
}
```

### Deref trait

`Box` implements `Deref` which allows to do the following:

``` rust
fn main() {
    let x = 5;
    let y = Box::new(x);

    assert_eq!(5, x);
    assert_eq!(5, *y);
}
```

`Deref` is also implemented for `Vec` and `String`.
`Deref` can be implemented for cusom type, e.g.:

``` rust
use std::ops::Deref;

struct MyBox<T>(T);

impl<T> MyBox<T> {
    fn new(x: T) -> MyBox<T> {
        MyBox(x)
    }
}

impl<T> Deref for MyBox<T> {
    type Target = T;

    fn deref(&self) -> &T {
        &self.0
    }
}

fn main() {
    let x = 5;
    let y = MyBox::new(x);

    assert_eq!(5, x);
    assert_eq!(5, *y);  // *y is identical to *(y.deref())
}
```

_Deref coercion_ is automatic conversion from type implemeting `Deref` to reference type returned by `deref()` method of the trait.

Example:

``` rust
fn hello(name: &str) {
    println!("Hello, {}!", name);
}

fn main() {
    let m = MyBox::new(String::from("Rust"));
    hello(&m);   // MyBox is coerced to &String, and String is coerced to &str
}
```

Similary to `Deref` the `DerefMut` trait can be used to override the `*` operator on mutable references.
Rust does deref coercion when it finds types and trait implementations in three cases:

 - From `&T` to `&U` when `T: Deref<Target=U>`
 - From `&mut T` to `&mut U` when `T: DerefMut<Target=U>`
 - From `&mut T` to `&U` when `T: Deref<Target=U>`

In the third case Rust will coerce a mutable reference to an immutable one. But the reverse is not possible becuase there can be several users of immutable reference and just one user of mutable.

### Drop trait

`Drop` trait is used to define behaviour when smart pointer goes out of scope.

Example:

``` rust
struct CustomSmartPointer {
    data: String,
}

impl Drop for CustomSmartPointer {
    fn drop(&mut self) {
        println!("Dropping CustomSmartPointer with data `{}`!", self.data);
    }
}

fn main() {
    let c = CustomSmartPointer { data: String::from("my stuff") };
    let d = CustomSmartPointer { data: String::from("other stuff") };
    println!("CustomSmartPointers created.");
}    // drop is automatically called at the end of scope
```

Dropping explicitly:

```rust
...

fn main() {
    let c = CustomSmartPointer { data: String::from("some data") };
    println!("CustomSmartPointer created.");

    // explicit drop of c. Note, c.drop() can't be used.
    drop(c);

    println!("CustomSmartPointer dropped before the end of main.");
}
```

### Reference counting with Rc<T>

The `Rc<T>` type keeps track of the number of references to a value which determines whether or not a value is still in use. 

Example:

```rust
enum List {
    Cons(i32, Rc<List>),
    Nil,
}

use crate::List::{Cons, Nil};
use std::rc::Rc;

fn main() {
    let a = Rc::new(Cons(5, Rc::new(Cons(10, Rc::new(Nil)))));  // num of users: 1
    let b = Cons(3, Rc::clone(&a));                             // num of users: 2
    let c = Cons(4, Rc::clone(&a));                             // num of users: 3
}
// b, c, and a are out of scope and use counter becomes 0
// and Rc drops owned data
```

`Rc::clone` doesn’t make a deep copy of all the data. It just copies smart pointer instance and increments use counter. Data is dropped when use counter becomes 0.

### Interior mutability with RefCell<T>

_Interior mutability_ is a design pattern in Rust that allows you to mutate data even when there are immutable references to that data.

`RefCell<T>` implements borrowing rules at runtime:

 - At any given time, you can have either (but not both of) one mutable reference or any number of immutable references.
 - References must always be valid.

If rules are broken at runtime program will panic and exit.

Example:

``` rust
use std::cell::RefCell;

struct MyStruct {
    f1: RefCell<String>,
    f2: String
}

fn main() {
    let a = MyStruct { 
      f1: RefCell::new("can mutate".to_string()), 
      f2: "can't mutate".to_string() 
    };

    // a is immutable, but f1 can be changed; and a.f2 can't be changed
    *a.f1.borrow_mut() = "mutated".to_string();

    println!("{}; {}", a.f1.borrow(), a.f2);
}
```

`RefCell<T>` keeps track of borrows. E.g. you can't borrow twice with `borrow_mut()`, or call `borrow_mut()` when values is already borrowed for read with `borrow()`. Although type `Rc<RefCell<T>>` allows you to have several holders of data that can mutate.

Example:

``` rust
use std::rc::Rc;
use std::cell::RefCell;

fn main() {
    let v = Rc::new(RefCell::new(5));

    let a = Rc::clone(&v);

    // "a" is immutable
    println!("a before = {:?}", a.borrow());

    // update "v"
    *v.borrow_mut() += 10;

    // value referenced by "a" mutated as well
    println!("a after = {:?}", a.borrow());
    println!("v after = {:?}", v.borrow());
}
```

### Avoiding reference cycles with Weak references

If we have two values referencing each other with use of `Rc<T>` then when the values go out of scope both `Rc<T>` will still have count value of 1 which will lead to a memory leak. Using Weak references can help to avoid described situation.

E.g. consider tree where parent reference children and children reference parent:

``` rust
use std::rc::{Rc, Weak};
use std::cell::RefCell;

#[derive(Debug)]
struct Node {
    value: i32,
    parent: RefCell<Weak<Node>>,
    children: RefCell<Vec<Rc<Node>>>,
}

fn main() {
    let leaf = Rc::new(Node {
        value: 3,
        parent: RefCell::new(Weak::new()),   // empty reference for now
        children: RefCell::new(vec![]),
    });

    // weak reference points to None by now
    println!("leaf parent = {:?}", leaf.parent.borrow().upgrade());

    let branch = Rc::new(Node {
        value: 5,
        parent: RefCell::new(Weak::new()),
        children: RefCell::new(vec![Rc::clone(&leaf)]),   // referencing leaf
    });

    // referencing parent of leaf
    *leaf.parent.borrow_mut() = Rc::downgrade(&branch);

    // weak reference now points to "branch"
    println!("leaf parent = {:?}", leaf.parent.borrow().upgrade());
}
```

`Rc::downgrade` returns a weak pointer increasing weak reference counter, and does not increase strong reference counter compared to `RC::clone`. `upgrade()` method on the Weak pointer instance returns `Option`: either referenced value still exists or already doesn't.


## Concurrency (parallelism)

Rust's standard library provides means for managing threads, message passing between threads, and shared state primitives.

Threads example:

``` rust
use std::thread;

fn main() {
    let v = vec![1, 2, 3];

    // creating thread
    // thread::spawn expects closure
    // this closure captures reference to vector
    // and thus requires explicit move keyword
    let handle = thread::spawn(move || {
        println!("Here's a vector: {:?}", v);
    });
    // v becomes inaccessible here

    // the main thread awaits for thread termination
    // and unwraps Result returned by join()
    handle.join().unwrap();
}
```

Message passing with channels example:

``` rust
use std::thread;
use std::sync::mpsc;       // using multiple producer, single consumer channel

fn main() {
    // creating channel: tx is transmitter, rx is receiver
    let (tx, rx) = mpsc::channel();

    // create thread and move tx there
    thread::spawn(move || {
        let val = String::from("hi");

        // sned the message
        tx.send(val).unwrap();

        // val here can't be used anymore as it was moved
    });

    // receive the message using blocking method recv and print it
    let received = rx.recv().unwrap();
    println!("Got: {}", received);
}
```

Using second sender and iterator to read messages:

``` rust
use std::thread;
use std::sync::mpsc;

fn main() {
    let (tx, rx) = mpsc::channel();

    // creating second sender
    let tx2 = mpsc::Sender::clone(&tx);

    thread::spawn(move || {
        let val = String::from("hi");
        tx.send(val).unwrap();
    });

    thread::spawn(move || {
        let val = String::from("hey");
        tx2.send(val).unwrap();
    });

    // receiving messages by using iterator
    for received in rx {
        println!("Got: {}", received);
    }
}
```

Sharing state with `Mutex<T>`:

``` rust
use std::sync::Mutex;

fn main() {
    // create mutex with integer value
    let m = Mutex::new(5);

    {
        // acquire lock and get value reference
        let mut num = m.lock().unwrap();

        // mutate value
        *num = 6;
    }
    // after going out of scope mutex is automatically unlocked

    println!("m = {:?}", m);
}
```

Method `lock` returns a smart pointer called `MutexGuard`, wrapped in a `LockResult`. 
`MutexGuard` implements `Deref` and `Drop`, and when it goes out of scope `drop()` is called and mutex is unlocked.

Mutex can be shared between threads with help of `Arc<T>`. `Arc<T>` is similar to `Rc<T>`, but `Rc<T>` can't be used by mutiple threads, so atomic `Rc<T>` is required. `Arc<T>` is slower than `Rc<T>` because of costs of syncronization.

Example:

```rust
use std::sync::{Mutex, Arc};
use std::thread;

fn main() {
    // new mutex wrapped in Arc
    let counter = Arc::new(Mutex::new(0));
    let mut handles = vec![];

    for _ in 0..10 {
        // increase reference count of the mutex
        let counter = Arc::clone(&counter);

        // create thread and move Arc instance there
        let handle = thread::spawn(move || {
            let mut num = counter.lock().unwrap();

            *num += 1;
        });

        // save thread handle
        handles.push(handle);
    }

    // wait for the threads to finish
    for handle in handles {
        handle.join().unwrap();
    }

    println!("Result: {}", *counter.lock().unwrap());
}
```

It is not possible to move an `Rc<T>` or `RefCell<T>` instance in a thread because anything that can be moved in a thread must implement `Send` and `Sync` traits.
The `Send` marker trait indicates that ownership of the type implementing `Send` can be transferred between threads.

Almost every Rust type is `Send`. Any type composed entirely of `Send` types is automatically marked as `Send` as well. Almost all primitive types are `Send`, aside from raw pointers.
 
The `Sync` marker trait indicates that it is safe for the type implementing `Sync` to be referenced from multiple threads. In other words, any type `T` is `Sync` if `&T` is `Send`, meaning the reference can be sent safely to another thread. E.g. `Mutex<T>` is a smart pointer and it is `Sync`.

Manually implementing these traits involves implementing unsafe Rust code.


## Obect-oriented features

Incapsulation is provided by `pub` keyword on modules, structures, enumerations, structure fields, and functions.

Compile time polymorphism is available with use of generic types. It is also called _bounded parametric polymorphism_. With bounds on generics the compiler generates nongeneric implementations of functions and methods for each concrete type that we use in place of a generic type parameter. This is called _static dispatch_.

Rust does not provide rich inheritance features. Traits are used to define common behaviour.

### Dynamic polimorphism

Dynamic polymorphism (_dynamic dispatch_) can be achieved by using trait objects. With dynamic dispatch, at runtime, Rust uses the pointers inside the trait object to know which method to call. There is a runtime cost when this lookup happens that doesn’t occur with static dispatch. Dynamic dispatch also prevents the compiler from choosing to inline a method’s code, which in turn prevents some optimizations. 

A trait can be used to create trait objects if all the methods defined in the trait have the following properties:

 - The return type isn’t `Self`.
 - There are no generic type parameters.

Trait object variable is a pointer (can be a smart pointer) followed by keyword `dyn` and trait type, e.g.:

``` rust
let trait_obj: &dyn MyTrait;
let trait_obj: Box<dyn MyTrait>;
```

Usage example:

```rust
trait Test {
    fn run_test(&self);
}

struct MyStruct1 {}
struct MyStruct2 {}

impl Test for MyStruct1 {
    fn run_test(&self) {
        println!("Callled on MyStruct 1");
    }
}

impl Test for MyStruct2 {
    fn run_test(&self) {
        println!("Callled on MyStruct 2");
    }
}

fn main() {
    let obj1 = MyStruct1 {};
    let obj2 = MyStruct2 {};

    let mut trait_obj: &dyn Test = &obj1;

    // prints "Callled on MyStruct 1"
    trait_obj.run_test();

    trait_obj = &obj2;

    // prints "Callled on MyStruct 2"
    trait_obj.run_test();
}
```

## Patterns and matching

Patterns are used in the following constructs:

``` rust
// matching a single value to an exhaustive set of patterns
match <value> {
    <pattern> => <expression>,
    <pattern> => <expression>,
    <pattern> => <expression>,
}
```

``` rust
// matching to sevaral patterns
if let <pattern> = <expression> {         // if let
} else if let <pattern> = <expression> {  // else if let
} else if <condition> {                   // without pattern
} else {
}
```

``` rust
// cycle while there is a match
while let <pattern> = <expression> {
}
```

``` rust
// in for cycle
for <pattern> in <iterator> {
}
```

``` rust
// creating variables
let <pattern> = <expression>;
```

``` rust
// in closure and function parameters
fn function_name(<pattern>: <datatype>) {
}
```

<pattern> consists of some combination of the following:

 - Literals
 - Destructured arrays, enums, structs, or tuples
 - Variables
 - Wildcards
 - Placeholders

Patterns come in two forms: _refutable_ and _irrefutable_. Patterns that will match for any possible value passed are irrefutable. Patterns that can fail to match for some possible value are refutable.

Function parameters, let statements, and for loops can only accept irrefutable patterns.

Matching examples.

Literals:

``` rust
let x = 1;

match x {
    1 => println!("one"),
    2 => println!("two"),
    3 => println!("three"),
    _ => println!("anything"),
}
```

Variables:

``` rust
let x = Some(5);
let y = 10;

match x {
    Some(50) => println!("Got 50"),
    Some(y) => println!("Matched, y = {:?}", y),  // previously defined y is shadowed
                                                  // up to the end of expression
                                                  // after the expression outer y
                                                  // is available again
    _ => println!("Default case, x = {:?}", x),
}
```

Mutliple patterns:

``` rust
let x = 1;

match x {
    1 | 2 => println!("one or two"),  // patteras are "or"ed with |
    3 => println!("three"),
    _ => println!("anything"),
}
```

Ranges of values:

``` rust
let x = 5;

match x {
    1..=5 => println!("one through five"),    // is equvalent to 1|2|3|4|5
    _ => println!("something else"),
}
```

Ranges are allowed only with numeric and char types. Example for chars:

``` rust
let x = 'c';

match x {
    'a'..='j' => println!("early ASCII letter"),
    'k'..='z' => println!("late ASCII letter"),
    _ => println!("something else"),
}
```

Destructuring structs:

``` rust
struct Point {
    x: i32,
    y: i32,
}

fn main() {
    let p = Point { x: 0, y: 7 };

    // full syntax
    let Point { x: a, y: b } = p;
    assert_eq!(0, a);
    assert_eq!(7, b);

    // short syntax,
    // variable names must match the filed names
    let Point { x, y } = p;
    assert_eq!(0, x);
    assert_eq!(7, y);

    // refutable patterns with literals
    match p {
        Point { x, y: 0 } => println!("On the x axis at {}", x),
        Point { x: 0, y } => println!("On the y axis at {}", y),
        Point { x, y } => println!("On neither axis: ({}, {})", x, y),
    }
}
```

Destructuring Enums:

``` rust
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}

fn main() {
    let msg = Message::ChangeColor(0, 160, 255);

    match msg {
        Message::Quit => {
            println!("The Quit variant has no data to destructure.")
        },
        Message::Move { x, y } => {
            println!(
                "Move in the x direction {} and in the y direction {}",
                x,
                y
            );
        }
        Message::Write(text) => println!("Text message: {}", text),
        Message::ChangeColor(r, g, b) => {
            println!(
                "Change the color to red {}, green {}, and blue {}",
                r,
                g,
                b
            )
        }
    }
}
```

Destructuring nested structs and enums:

``` rust
enum Color {
   Rgb(i32, i32, i32),
   Hsv(i32, i32, i32),
}

enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(Color),
}

fn main() {
    let msg = Message::ChangeColor(Color::Hsv(0, 160, 255));

    match msg {
        Message::ChangeColor(Color::Rgb(r, g, b)) => {
            println!(
                "Change the color to red {}, green {}, and blue {}",
                r,
                g,
                b
            )
        },
        Message::ChangeColor(Color::Hsv(h, s, v)) => {
            println!(
                "Change the color to hue {}, saturation {}, and value {}",
                h,
                s,
                v
            )
        }
        _ => ()
    }
}
```

Destructuring structs and tuples:

``` rust
let ((feet, inches), Point {x, y}) = ((3, 10), Point { x: 3, y: -10 });
```

Ignoring value:

``` rust
fn foo(_: i32, y: i32) {
    println!("This code only uses the y parameter: {}", y);
}


fn main() {
    foo(3, 4);
}
```

Ignore some parts of value:

``` rust
let numbers = (2, 4, 8, 16, 32);

match numbers {
    (first, _, third, _, fifth) => {
        println!("Some numbers: {}, {}, {}", first, third, fifth)
    },
}
```

Marking unused variable:

``` rust
// unuzed var is prefixed with _
let _x = 5;

// use in pattern
let s = Some(String::from("Hello!"));
if let Some(_s) = s {                  // note s is moved to _s 
                                       // and is not accessible after this line
    println!("found a string");
}

```

Ignoring parts with `..` :

```rust
struct Point {
    x: i32,
    y: i32,
    z: i32,
}

let origin = Point { x: 0, y: 0, z: 0 };

match origin {
    Point { x, .. } => println!("x is {}", x),
}

let numbers = (2, 4, 8, 16, 32);

match numbers {
    (first, .., last) => {
        println!("Some numbers: {}, {}", first, last);
    },
}
```

Extra conditions in `match`:

``` rust
let num = Some(4);

match num {
    Some(x) if x < 5 => println!("less than five: {}", x),
    Some(x) => println!("{}", x),
    None => (),
}

let x = 4;
let y = false;

match x {
    4 | 5 | 6 if y => println!("yes"),
    _ => println!("no"),
}
```

Bindings with `@`:

``` rust
enum Message {
    Hello { id: i32 },
}

let msg = Message::Hello { id: 5 };

match msg {
    Message::Hello { id: id_variable @ 3..=7 } => {
        println!("Found an id in range: {}", id_variable)
    },
    Message::Hello { id: 10..=12 } => {
        println!("Found an id in another range")
    },
    Message::Hello { id } => {
        println!("Found some other id: {}", id)
    },
}
```


## Advanced Rust

### Unsafe Rust

Unsafe Rust gives the ability to:

 - Dereference a raw pointer
 - Call an unsafe function or method
 - Access or modify a mutable static variable
 - Implement an unsafe trait
 - Access fields of unions

Unsafe block starts with `unsafe` keyword:

```rust
unsafe {
   // unsafe code
}
```

Raw pointers:

``` rust
let mut num = 5;

// immutable raw pointer
let r1 = &num as *const i32;

// mutable raw pointer
let r2 = &mut num as *mut i32;

// the raw pointers can only be used in unsafe block
unsafe {
    println!("r1 is: {}", *r1);
    println!("r2 is: {}", *r2);
}
```

Different from references and smart pointers, raw pointers:

 - Are allowed to ignore the borrowing rules by having both immutable and mutable pointers or multiple mutable pointers to the same location
 - Aren’t guaranteed to point to valid memory
 - Are allowed to be null
 - Don’t implement any automatic cleanup

Functions can be unsafe too:

``` rust
// function which whole body is unsafe
unsafe fn dangerous() {}

// function can be called only in unsafe block
unsafe {
    dangerous();
}
```

Rust can call exteranl functions created in other languages. This feature is called _Foreign Function Interface_ (FFI). These kind of functions are always unsafe. `extern` keyword with type of _application binary interface_ (ABI) should be used in that case. For example for function compatible with `C` language we can write:

``` rust
extern "C" {
    // unsafe functions
    fn abs(input: i32) -> i32;
}

fn main() {
    unsafe {
        println!("Absolute value of -3 according to C: {}", abs(-3));
    }
}
```

Rust in it's turn can export functions as well (although function is safe in that case):

``` rust
#[no_mangle]
pub extern "C" fn call_from_c() {
    println!("Just called a Rust function from C!");
}
```

Rust allows declaration of static variables. Static variables have lifetime `'static`. Mutable static variables are unsafe. Example:

``` rust
// safe immutable static variable
static HELLO_WORLD: &str = "Hello, world!";

// unsafe mutable static variable
static mut COUNTER: u32 = 0;


fn main() {
    // safe to use
    println!("name is: {}", HELLO_WORLD);

    // must be marked unsafe
    unsafe {
        COUNTER += 1;
        println!("COUNTER: {}", COUNTER);
    }
}
```

Note, const and immutable static variables differ in that static variable has fixed memory address while const value can be directly substituted in the code.

Traits can be unsafe too:

``` rust
unsafe trait Foo {
    // methods go here
}

unsafe impl Foo for i32 {
    // method implementations go here
}
```

For example the compiler implements `Send` and `Sync` traits automatically if a type is composed entirely of `Send` and `Sync` types. If we implement a type that contains a type that is not `Send` or `Sync`, such as raw pointers, and we want to mark that type as `Send` or `Sync`, we must use `unsafe`.

### Advanced traits

Traits can contain type placeholder for an associated type. E.g.:

```rust
pub trait Iterator {
    // placeholder type
    type Item;

    fn next(&mut self) -> Option<Self::Item>;
}

// implementation
impl Iterator for Counter {
    // u32 is associated type
    type Item = u32;

    fn next(&mut self) -> Option<Self::Item> {
        // --snip--
```

The idea looks similar to generic types. The difference is that when using associated type, trait can be implemented only once for certain associated type, while when using generic type, trait can be implemented many times, and concrete type must be specified explicitly.

*Default generic type and operator overloading*

For generic type default value can be assigned.
It is used in two main ways:

 - To extend a type without breaking existing code
 - To allow customization in specific cases most users won’t need

Example for the first use-case:

```rust
// initially trait had no generic type
trait MyTrait {
    fn my_fun(self, param: u32);
}

// after some time we decided to exted it with generic type
// and to not break code that uses the trait default type is assigned
// for the generic type
trait MyTrait <T=u32> {
    fn my_fun(self, param: T);
}
```

Example for the second use-case:

```rust
// std has a trait implementing "+" operator.
// The default implementation allows adding two values of the 
// same type which is the most common case of using "+" operator.
//
// trait Add<RHS=Self> {
//     type Output;
//
//     fn add(self, rhs: RHS) -> Self::Output;
// }


use std::ops::Add;

// a sample 2d vector type
#[derive(Debug)]
struct Vector2 {x: i32, y: i32 }

// implementation for default type
impl Add for Vector2 {
   type Output = Vector2;

   // add two vercors
   fn add(self, rhs: Vector2) -> Self::Output {
       Vector2 {
          x: rhs.x + self.x,
          y: rhs.y + self.y
       }
   }
}

// implementation for custom type
impl Add<i32> for Vector2 {
   type Output = Vector2;

   // add two scalar to vector
   fn add(self, rhs: i32) -> Self::Output {
       Vector2 {
          x: rhs + self.x,
          y: rhs + self.y
       }
   }
}

fn main() {
    let v1 = Vector2 { x: 1, y: 2 };
    let v2 = Vector2 { x: 2, y: 3 };
    let v3 = Vector2 { x: -1, y: 1 };

    println!("{:?}", v1 + v2);
    println!("{:?}", v3 + 5);
}
```

*Calling methods with the same name*

A struct and a trait which the struct is implementing can define methods with the same signature, e.g.:

``` rust
struct MyStruct;

impl MyStruct {
    fn my_fun(&self) {
        println!("Calling method of MyStruct instance");
    }
}

trait SomeTrait {
    // the same signature as in MyStruct
    fn my_fun(&self);
}

impl SomeTrait for MyStruct {
    fn my_fun(&self) {
        println!("Calling method of SomeTrait instance");
    }
}

fn main() {
    let obj = MyStruct;
    obj.my_fun();            // will print "Calling method of MyStruct instance"
    MyStruct::my_fun(&obj);  // will print "Calling method of MyStruct instance"
    SomeTrait::my_fun(&obj); // will print "Calling method of SomeTrait instance"
}
```

For methods that don't have `self` argument the following general purpose syntax can be used:

```rust
<Type as Trait>::function(receiver_if_method, next_arg, ...);
```

Example:

``` rust
struct MyStruct;

impl MyStruct {
    // doesn't take self this time
    fn my_fun() {
        println!("Calling fun of MyStruct instance");
    }
}

trait SomeTrait {
    // the same signature as in MyStruct
    fn my_fun();
}

impl SomeTrait for MyStruct {
    fn my_fun() {
        println!("Calling fun of SomeTrait instance");
    }
}

fn main() {
    MyStruct::my_fun();                // will print "Calling method of MyStruct instance"
    <MyStruct as SomeTrait>::my_fun(); // will print "Calling method of SomeTrait instance"
}
```

*Supertraits*

Using supertraits allows one trait to use another trait’s functionality.

Example:

``` rust
trait BaseTrait {
    fn print_data(s: &str) {
        println!("{}", s);
    }
}

trait DecoratorTrait : BaseTrait {
    // the same signature as in MyStruct
    fn decorate(s: &str) {
        println!("{}", "=".repeat(str::len(s)));

        // the function from BaseTrait now can be used
        Self::print_data(s);
        
        println!("{}", "=".repeat(str::len(s)));
    }
}

struct MyStruct;
impl DecoratorTrait for MyStruct {}
impl BaseTrait for MyStruct {}

fn main() {
    MyStruct::decorate("The data");
}
```

*Newtype pattern*

_Newtype_ pattern can be used to implement a trait defined in a different crate on a struct or enum defined in another crate. Because it is not possible to do directly, the type can be wrapped in struct for which the trait will be implemented.

Example:

``` rust
use std::fmt;

// because Display trait can't be implemented directly for Vec
// the wrapper is used
struct Wrapper(Vec<String>);

impl fmt::Display for Wrapper {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "[{}]", self.0.join(", "))
    }
}

fn main() {
    let w = Wrapper(vec![String::from("hello"), String::from("world")]);
    println!("w = {}", w);
}
```

Newtype pattern also can be used for:

  - abstracting away or hiding some implementation details of a type
  - distinguishing types of the same structure, e.g. to distinguish units of measure of something

### Advanced types

Type alias can be used to make a new type name, e.g.:

``` rust
type Kilometers = i32;

let x: Kilometers = 1;
let y: i32 = 1;

// the alias is not a new type but just an alternative name
assert_eq!(x, y);
```

Type alias can be used to shorten complex names, e.g:

```rust
type Thunk = Box<dyn Fn() + Send + 'static>;

let f: Thunk = Box::new(|| println!("hi"));

fn takes_long_type(f: Thunk) {
    // --snip--
}

fn returns_long_type() -> Thunk {
    // --snip--
}
```

Another example:

```rust
// makes code easier to write 
// and it gives a consistent interface across all of std::io
type Result<T> = std::result::Result<T, std::io::Error>;
```

*Never type*

Type _Never_ is a type that can be used for functions that never return, or for infinite loops. The type is denoted `!`.

Example:

``` rust
// the function that never returns (diverging function)
fn bar() -> ! {
    // --snip--
}
```

Type Never can be coerced to any type, e.g.:

``` rust
let search = Some(5);

let val: u32 = match search {
    Some(num) => num,
    None(_) => continue,        // continue returns Never
                                // but is coerced to u32 at compile time
};
```

*Sized trait and dynamically sized types*

_Dynamically sized types_ (DSTs or unsized types) let us write code using values whose size we can know only at runtime. Dynamically sized type is described with a pointer, and it's data length.

The golden rule of dynamically sized types is that we must always put values of dynamically sized types behind a pointer of some kind. For example, `str` is DST and can be accessed only as reference: `&str`, `Box<str>`, `Rc<str>`, etc. Also, having a trait `Trait` the construct `&dyn Trait` is DST.

Rust has a particular trait called the `Sized`. The trait is used for every type whose size is known at compile time. Trait `Sized` is automatically added to every generic type, i.e.:

```rust
fn generic<T>(t: T) {
    // --snip--
}
```

is eqivalent to

```rust
fn generic<T: Sized>(t: T) {
    // --snip--
}
```

The rule can be relaxed like this

``` rust
// T can be Sized but not necessary
// because of that function can accept only pointer: &T
fn generic<T: ?Sized>(t: &T) {
    // --snip--
}
```

### Advanced functions and closures

Functions can be passed as arguments to other functions. Any function coerce to the type `fn`. The `fn` type is called a _function pointer_.

Example: 

``` rust
fn add_one(x: i32) -> i32 {
    x + 1
}

fn do_twice(f: fn(i32) -> i32, arg: i32) -> i32 {
    f(arg) + f(arg)
}

fn main() {
    let answer = do_twice(add_one, 5);

    println!("The answer is: {}", answer);
}
```

Function pointers implement all three of the closure traits (`Fn`, `FnMut`, and `FnOnce`). Function pointer can alway be passed as an argument for a function that expects a closure. It’s best to write functions using a generic type and one of the closure traits so your functions can accept either functions or closures.

The initializer `()` in structs is also of function type, e.g:

``` rust
enum Status {
    Value(u32),
    Stop,
}

let list_of_statuses: Vec<Status> =
    (0u32..20)
    .map(Status::Value)
    .collect();
```

### Macros

Macros are a way of writing code that writes other code.
The term macro refers to a family of features in Rust: 
 - declarative macros with `macro_rules!`
 - procedural macros, there are three kind of them:
  * Custom `#[derive]` macros that specify code added with the derive attribute used on structs and enums
  * Attribute-like macros that define custom attributes usable on any item
  * Function-like macros that look like function calls but operate on the tokens specified as their argument

Macros can take a variable number of parameters compared to functions.
Macros definition must appear in scope before you use macro.

_Declarative macros_ are most widely used in Rust. The syntax is somewhat similar to `match` expression, e.g.:

```rust
// export allows to bring macro in the scope when crate is imported
#[macro_export]
macro_rules! vec {
    // matches a list of Rust expressions followed by comma,
    // zero or more expressions expected
    ( $( $x:expr ),* ) => {
        {
            let mut temp_vec = Vec::new();
            $(
                temp_vec.push($x);
            )*
            temp_vec
        }
    };
}
```

For `vec![1, 2, 3];` the resulting code will be:

```rust
let mut temp_vec = Vec::new();
temp_vec.push(1);
temp_vec.push(2);
temp_vec.push(3);
temp_vec
```

The macros in details are described in [The Little Book of Rust Macros](https://danielkeep.github.io/tlborm/book/index.html)


_Procedural macros_ take a `TokenStream` and produce `TokenStream` in result.
When creating procedural macros, the definitions must reside in their own crate with special type.
The general template for procedural macro is:

``` rust
use proc_macro;

#[some_attribute]
pub fn some_name(input: TokenStream) -> TokenStream {
}
```

Example of custom `derive` macro.

Call:

> cargo new hello_macro --lib

In `src/lib.rs` add:

``` rust
pub trait HelloMacro {
    fn hello_macro();
}
```

Inside `hello_macro` directory call:

> cargo new hello_macro_derive --lib

In `hello_macro_derive/Cargo.toml` specify:

``` rust
[lib]
proc-macro = true

[dependencies]
syn = "0.14.4"
quote = "0.6.3"
```

In `hello_macro_derive/src/lib.rs` add:

```rust
extern crate proc_macro;

use crate::proc_macro::TokenStream;
use quote::quote;
use syn;

#[proc_macro_derive(HelloMacro)]
pub fn hello_macro_derive(input: TokenStream) -> TokenStream {
    // Construct a representation of Rust code as a syntax tree
    // that we can manipulate.
    // The AST looks like this:
    //   DeriveInput {
    //       // --snip--
    //
    //       ident: Ident {
    //           ident: "Pancakes",
    //           span: #0 bytes(95..103)
    //       },
    //       data: Struct(
    //           DataStruct {
    //               struct_token: Struct,
    //               fields: Unit,
    //               semi_token: Some(
    //                   Semi
    //               )
    //           }
    //       )
    //   }
    let ast = syn::parse(input).unwrap();

    // Build the trait implementation
    impl_hello_macro(&ast)
}

fn impl_hello_macro(ast: &syn::DeriveInput) -> TokenStream {
    let name = &ast.ident;
    let gen = quote! {
        impl HelloMacro for #name {
            fn hello_macro() {
                println!("Hello, Macro! My name is {}", stringify!(#name));
            }
        }
    };
    gen.into()
}
```

Then other crates can import the macro with specifying dependencies:

```
[dependencies]
hello_macro = { path = "../hello_macro" }
hello_macro_derive = { path = "../hello_macro/hello_macro_derive" }
```

Attribute-like macro.

Example:

```
#[route(GET, "/")]
fn index() {
    ...
```

The macro definition will look like:

``` rust
#[proc_macro_attribute]
pub fn route(attr: TokenStream, item: TokenStream) -> TokenStream {
```

The first `TokenStream` named "attr" accepts macro attributes `GET, "/"`, the second `TokenStream` named "item" contains body of the item to which attribute is applied.

Function-like macro.

Example:

```rust
let sql = sql!(SELECT * FROM posts WHERE id=1);
```

The macro definition will look like:

```rust
#[proc_macro]
pub fn sql(input: TokenStream) -> TokenStream {
    ...
```
