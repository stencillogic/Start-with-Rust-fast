# Learn Rust fast (work in progress)

This is a shorter version of the official Rust tutorial (The Rust Programming Language book).
Enjoy.

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
The build command also creates Cargo.lock which containes exact version of dependencies used to build the project. Cargo needs to lock versions because versions of dependencies in Cargo.toml are not exact but rather specify expected "compatibility version" while Cargo.lock specifies exact version.

Also you can call:

> cargo run                 # build and run the resulting application
> cargo check               # quick build without making output
> cargo build --release     # build the release version of app and put it in `target/release` dir
> cargo doc --open          # generate html documentation from code

## Language concepts

Strict type system.
Not interpretable.
Language has functional and OOP features.
Concepts which are specific to this language is ownership, borrowing, and lifetimes. 
OOP features are limited compared to Java and C++.

## Basic control structures

### Function definition.

In general:

``` rust
"fn" <function_name> ([<arg1_name>: <datatype>, <arg2_name>: <datatype>, ...]) {
    <body>
}
```

Function body consists of statements and expressions. Statement ends with ";".
Function can return value as expression without ";".

Examples:

``` rust

// function without arguments and return value
fn some_func1() {
    println!("Hello");   // statement
}

// function with arguments and without return value
fn some_func2(x: u32, y: i8) {
    println!("x = {}, y = {}", x, y);   // print x and y
    return;            // return from function without return value
}

// function with arguments and return value
fn some_func3(x: u32, y: u32) -> u32 {	// return value datatype
    x + y     // return value as expression
}

fn some_func3(x: u32, y: u32) -> u32 {
    return x + y;     // return statement with return keyword
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
    }; // end of statement
}
```



### Varibale declaration.

In general:

``` rust
"let" ["mut"] <var_name>[ ":" <datatype>] [ "=" <expression>] ";"
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

``` rust
"const" <upper_case_name>: <datatype> = <value>;
```

Example:

``` rust
const V:u32 = 100_000;	// 100_000 is 100000
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

f32, f64 - floating poit numbers of 32- and 64-bit length.

Number literals:

| Number literals | Example |
|-|-|
| Decimal | 98_222 | 
| Hex | 0xff | 
| Octal | 0o77 | 
| Binary | 0b1111_0000 | 
| Byte (u8 only) | b'A' |
| Floating point | 0.2 |

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

## Control flow

### conditional expressions

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

// x stays in scope and accessible
println!("{}", x);
```

In contrast:

``` rust
let s1 = String::from("hello");
let s2 = s1;

// s1 is not availalbe anymore becuase String is not basic datatype.
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

// calling othe function and passing x as argument
other_function(s1);

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
let r3: &mut f32 = &mut y;  // no more references are allowed after this
 
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
    s.len()
}
```

It is important to understand difference between data on stack and in heap. E.g.:

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

let hello: &str = &s[0..5];   // slice 1
let world = &s[6..11];  // slice 2

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

Single members cannot be mutable, only whole struct can be mutable.

### Named tuples.

Example:

``` rust
// declaration
struct MyData1(u32, char, bool);
struct MyData2(u32, char, bool);  // MyData2 is different data type than MyData1

let data1 = MyData1(1, 'A', true);
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

Enum has a set of predefined values. Each value can have associated value, structure or tuple.

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

Option enum is used to replace null value. Definition:

``` rust
enum Option<T> {
    Some(T),
    None,
}
```

Option also has a set of methods to extract value from Some and do other stuff.

### pattern matching

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
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter => 25,
        _ => 0                // default arm with placeholder
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

`match` returns value of one of matched arms.

`if let` operator is a form of pattern matching expression. Example:

``` rust
// the condition will hold true if some_u8_value is Option::Some and not Option::None
if let Some(i) = some_u8_value {
    println!("{}", i); 
}

// else is analog of _ placeholder
let mut count = 0;
if let Coin::Quarter(state) = coin {
    println!("State quarter from {:?}!", state);
} else {
    count += 1;
}
```

## Project organization

Packages.

Packages is a Cargo feature that lets you build, test, and share crates. 
Package is a set of crates united by common functionality. 

Crates.

Crate is a tree of modules that produces a library or an executable. 
There can be several binaries in a crate but just one library, or a library and several binaries.

Modules.

Modules let you control the organization, scope, and privacy of paths.

Paths. 

Paths is a way of naming an item, such as a struct, function, or module


Command `cargo new` create a package. 
Cargo uses filenames to distinguish binary and library crate. `main.rs` is a sign of binary crate, `lib.rs` is a sign of library.

Mopdules let you organize code, e.g.:

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
    cargo::submod::func();
}
```

`submod` is accessible without `pub` becuase it is on the same level as function `main`.
`subsubmod` and `subfunc` are not accessible from `main`.

`use` keyword brings symbols from submodules to current scope, e.g.:

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
`super` keyword can be used to access elements from parent module, e.g.:

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

Glob operator:

``` rust
use std::collections::*;
```

If we use `pub` for a whole structure the structure becomes visible, but it's fields are not visible. To make a struct field visible `pub` must be applied to that field. E.g.:

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
}
```

Subdirectories and files can be used to organize modules, .e.g:

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

Collections store data on heap and thus resizable.

Vector.

Vector is resizable array of elements of certain type.

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

// read an element, if index is out of bound program panics
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
To store elements of different type or structure enum or trait can be used.


String.

String allows store and manipulate on text strings. `String` datatype stores utf-8 encoded strings. For other encodings other datatypes exist.

Usage example

``` rust
// initialization
let mut s = String::new();
let mut s = "some string".to_string();
let mut s = String::from("some string");

// append string
s.push_str(" append");

// append char
s.push('a');

// adding strings
let s1 = String::from("first");
let s2 = String::from(" second");
let s3 = s1 + &s2 + "abc";        // s1 is movd to s3 and is not usable after this line

// deref coercion
let s = "string".to_string();
let r: &str = &s;                 // &String is assigned to &str

// format! macro
let formatted: String = format!("{} {}", s2, s3);

// indexing, can take only rage longer than 1 byte
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

HashMap is associative array backed by hash table.

Usage example:

``` rust
use std::collections::HashMap;

let mut hm = HashMap::new();

// keys and values have fixed datatypes
// datatypes here are inferred automatically from the data
hm.insert(String::from("Blue"), 10);
hm.insert(String::from("Yellow"), 50);

// check if entry with a key exists and add if not
hm.entry(String::from("Yellow")).or_insert(50);

// get value
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

For recoverable error enum `Result` is used:

``` rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

Instance of Result has several useful methods:

``` rust
use std::fs::File;

// return value of Ok in case of Ok, and panic in case of Err with custom message
let f1 = File::open("log.txt").expect("Error message");

// return value of Ok in case of Ok, and panic in case of Err
let f2 = File::open("log.txt").unwrap();

// open file or return stdout on error
let f3 = File::open("log.txt").unwrap_or_else(|_| -> File {f2});
```

Error propagation.

`?` operator can be used inside function returning `Result` type.
The `?` placed after a `Result` value will interrupt execution in case of Err and function will return Err result. If the `Result` value is Ok `?` returns unwrapped value of Ok and processing continues.

Retrun type for of the function can differ from Err type passed to `?` operator. 
Error values that have the ? operator called on them go through the `from` function, defined in the `From` trait in the standard library, which is used to convert errors from one type into another.

Example:

``` rust
use std::io::Error;
use std::fs::File;

fn test() -> Result<File, Error> {
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
// trait defenition
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

Calling trait method example:

``` rust
let tweet = Tweet {
    username: String::from("horse_ebooks"),
    content: String::from("of course, as you probably already know, people"),
    reply: false,
    retweet: false,
};

println!("1 new tweet: {}", tweet.summarize());
```

Default implementation of trait method example:

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

Lifetime is a characteristic of a reference. Lifetime defines scope of function arguments which are of reference type and return value if it is of reference time. Sometimes compiler can infer lifetime automatically, but in other situations compiler might not know what is assumed lifetime of references coming an going out of a function.

Syntax:

``` rust
&i32        // a reference
&'a i32     // a reference with an explicit lifetime
&'a mut i32 // a mutable reference with an explicit lifetime
```

Example:

``` rust
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
        // for that explicit lifetime in function definition is used to explicitly
        // tell compiler that lifetime for both arguments and return value is the same
        let result = longest(string1.as_str(), string2.as_str());
        println!("The longest string is {}", result);
    }
}
```

We can't specify reference as a data type of struct field unless we use lifetime.
Example:

``` rust
struct ImportantExcerpt<'a> {
    part: &'a str,
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

When explicit annotations are omitted the compiler uses three rules to figure out what lifetimes references have when there aren’t explicit annotations.
Lifetime elision rules:

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

will become

``` rust
fn longest<'a, 'b>(x: &'a str, y: &'b str) -> &str {
```

For method:

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

`'static` is a special lifetime which means that this reference can live for the entire duration of the program. All string literals have the `'static` lifetime.


## Testing

Rust provides means for unit-testing and integration testing.

Unit tests usually created in the same file with tested functionality. E.g.

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

    // not test function
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

    // some test func which sould panic with certain message, then test succeeds.
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
 - `assert_eq!`: accepts two arguments and uses == operator to check if arguments are equal, prints the values if test fails. Arguments must implement `PartialEq` and `Debug` traits.
 - `assert_ne!`: accepts two arguments and uses != operator to check if arguments are equal, prints the values if test fails. Arguments must implement `PartialEq` and `Debug` traits.

Running all tests:

> cargo test

Running specific tests:

> cargo test test1

Cargo searches for substring in test name and if it matches test is executed, i.e. if `cargo test test1` will also run `test12`, `test1_3`, etc.

By default tests run in parallel in several threads. It can lead to contention on some shared resources, e.g. files. The number of threads is configurable as well:

> cargo test -- --test-threads=1

By default stdout of successful tests is suppressed. To print stdout of successful tests use:

> cargo test -- --nocapture

Run tests with `ignore` annotation:

> cargo test -- --ignored


Integration tests should be placed in separated directory named `tests` in the root of your project. Each file in tests directory is a separate crate representing integration test. Functiona which are common to all integration tests can be put in `tests/<some_dir>/mod.rs`. `tests/<some_dir>/mod.rs` will not be interpreted as integration test in that case.

In case of binary package it is not possible to perform integration tests agains it. It is common practice to make library package and pull almost everything insode library. Along with it create a binary package with small amout of unctionlality which uses the library.


## Closures, iterators, and functional features

Closure syntax:

"|" [ <param_name> [ ":" <data_type> ] ] { "," <param_name> [ ":" <data_type> ] } "|" [ "->" <data_type> ] <expression>

Examples:

``` rust
// standard function
fn  add_one_v1   (x: u32) -> u32 { x + 1 }

// closures
// each of add_one_v variables below has unique anonymous type
let add_one_v2 = |x: u32| -> u32 { x + 1 };
let add_one_v3 = |x|             { x + 1 };
let add_one_v4 = |x|               x + 1  ;
```

Closure is analogous to function, but data type specification is not mandatory becuase compiler can always infer data type.
Data type can be inferred just once.

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

 - `FnOnce` consumes the variables it captures from its enclosing scope, known as the closure’s environment. To consume the captured variables, the closure must take ownership of these variables and move them into the closure when it is defined. The Once part of the name represents the fact that the closure can’t take ownership of the same variables more than once, so it can be called only once.
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

`move` can be used to pass closure with captured environment to a different thread.

### Iterators

Iterator allows to perform tasks on sequences of items.

Examples:

``` rust
// define vector of integers
let v1 = vec![1, 2, 3];

// get mutable iterator over elements
let mut v1_iter = v1.iter();

// use next method to fetch next item
// method next returns immutable reference to a value
assert_eq!(v1_iter.next(), Some(&1));
assert_eq!(v1_iter.next(), Some(&2));
assert_eq!(v1_iter.next(), Some(&3));
assert_eq!(v1_iter.next(), None);

// to iterate over items once more new iterator is required
// getting new immutable iterator
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

    // methods with default implementations elided
    // the trait also provides default implementations for those methods
    // methods include: 
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
    let sum: u32 = Counter::new().zip(Counter::new().skip(1))
                                 .map(|(a, b)| a * b)          // map uses closure
                                 .filter(|x| x % d == 0)       // d is captured from outer scope
                                 .sum();
    assert_eq!(18, sum);
}
```

Use of iterators does not introduce additional performance penalty compared to loops.

## Project documentation

`///` is used to put documentation on functions.
`//!` is used to put documentation on crate or module as a whole.

`///` is interpreted as markdown and can contain sections:

 - Examples: usage examples. `cargo test` runs not only nit and integration tests but also code from examples.
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
```

## Workspaces

Project can be organized as workspace. E.g.:

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

Workspace has Cargo.lock and Cargo.toml in the root of the workspace folder. THe files are common to all creates inside the workspace.
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

Smart pointers allow to allocate data on heap, count references, free up memory automatically, and other things.
Basic smart pointers:

 - `Box<T>` for allocating values on the heap.
 - `Rc<T>`, a reference counting type that enables multiple ownership.
 - `Ref<T>` and `RefMut<T>`, accessed through `RefCell<T>`, a type that enforces the borrowing rules at runtime instead of compile time.

They have the following properties and use-cases:

 - `Rc<T>` enables multiple owners of the same data; `Box<T>` and `RefCell<T>` have single owners.
 - `Box<T>` allows immutable or mutable borrows checked at compile time; `Rc<T>` allows only immutable borrows checked at compile time; `RefCell<T>` allows immutable or mutable borrows checked at runtime.
 - Because `RefCell<T> `allows mutable borrows checked at runtime, you can mutate the value inside the `RefCell<T>` even when the `RefCell<T>` is immutable.

### Box

Boxes are used in the following situations:
 - When you have a type whose size can’t be known at compile time and you want to use a value of that type in a context that requires an exact size
 - When you have a large amount of data and you want to transfer ownership but ensure the data won’t be copied when you do so
 - When you want to own a value and you care only that it’s a type that implements a particular trait rather than being of a specific type

Smart pointers implement the `Deref` and `Drop` traits. `Deref` trait allows to convert smart pointer to regular reference. `Drop` trait defines destructor for a type. When value of the type goes out of scope destructor for that value is called.

Examples:

```rust
// recursive data structure
#[derive(Debug)]
enum List {
    Cons(i32, Box<List>),           // can't reference List directly becuase size 
                                    // of the structure is not known at compile time
    Nil,
}

use crate::List::{Cons, Nil};

fn main() {
    let list = Cons(1,
        Box::new(Cons(2,
            Box::new(Cons(3,
                Box::new(Nil))))));

    println!("{:?}", list);
}
```

### Deref trait

`Box` implements `Deref` whic allows to do the following:

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

Deref coercion is automatic conversion from reference to type implemeting `Deref` to reference type returned by `deref()` method of the trait.
Example:

``` rust
fn hello(name: &str) {
    println!("Hello, {}!", name);
}

fn main() {
    let m = MyBox::new(String::from("Rust"));
    hello(&m);   // &m is identical to &(*(m.deref())).deref()
}
```

The `DerefMut` trait can be used to override the * operator on mutable references.
Rust does deref coercion when it finds types and trait implementations in three cases:

 - From `&T` to `&U` when `T: Deref<Target=U>`
 - From `&mut T` to `&mut U` when `T: DerefMut<Target=U>`
 - From `&mut T` to `&U` when `T: Deref<Target=U>`

In the third case Rust will coerce a mutable reference to an immutable one. But the reverse is not possible becuase there can be several users of immutable reference and just one user of mutable one.

### Drop trait

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
}    // drop is automatically called on the end of scope
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

`Rc::clone` doesn’t make a deep copy of all the data. It just copies smart pointer instance in increments use counter. Data is dropped when use counter becomes 0.

### Interior mutability with RefCell<T>

Interior mutability is a design pattern in Rust that allows you to mutate data even when there are immutable references to that data.

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

    // a is immutable, but f1 can be changed; a.f2 can't be changed
    *a.f1.borrow_mut() = "mutated".to_string();

    println!("{}; {}", a.f1.borrow(), a.f2);
}
```

Use case for `RefCell<T>` is for example mocking objects in tests.
`RefCell<T>` keeps track of borrows. E.g. you can't borrow twice with `borrow_mut()`, or call `borrow_mut()` when values is already borrowed for read with `borrow()`.

Type `Rc<RefCell<T>>` allows to have several holders of data that can mutate.
Example:

``` rust
#[derive(Debug)]
enum List {
    Cons(Rc<RefCell<i32>>, Rc<List>),
    Nil,
}

use crate::List::{Cons, Nil};
use std::rc::Rc;
use std::cell::RefCell;

fn main() {
    let value = Rc::new(RefCell::new(5));

    let a = Rc::new(Cons(Rc::clone(&value), Rc::new(Nil)));

    let b = Cons(Rc::new(RefCell::new(6)), Rc::clone(&a));
    let c = Cons(Rc::new(RefCell::new(10)), Rc::clone(&a));

    *value.borrow_mut() += 10;

    // several items refer to data that was mutated at runtime
    println!("a after = {:?}", a);
    println!("b after = {:?}", b);
    println!("c after = {:?}", c);
}
```

### Avoiding reference cycles with Weak references

If we have two values referencing each other with use of `Rc<T>` then when the values go out of scope both `Rc<T>` will still have value of 1 which will lead to a memory leak.

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

    // weak reference points to None
    println!("leaf parent = {:?}", leaf.parent.borrow().upgrade());

    let branch = Rc::new(Node {
        value: 5,
        parent: RefCell::new(Weak::new()),
        children: RefCell::new(vec![Rc::clone(&leaf)]),   // referencing leaf
    });

    // referencing parent of leaf
    *leaf.parent.borrow_mut() = Rc::downgrade(&branch);

    // weak reference points to "branch"
    println!("leaf parent = {:?}", leaf.parent.borrow().upgrade());
}
```

`Rc::downgrade` returns a weak pointer increasing weak reference counter, and does not increase strong reference counter compared to `RC::clone`. `upgrade()` method on the Weak pointer instance returns Option: either referenced value still exists or already doesn't.

## Concurrency (parallelism)

Rust's standard library provides means for managing threads, message passing between threads and shared state primitives.

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
    // v is not accessible here

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




