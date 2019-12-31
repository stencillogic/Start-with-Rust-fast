# Learn Rust fast

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


To build the project cd to <project_name> directory and call:

> cargo build

Note, linker is not included as part of Rust. External linker is used to build output.
The command creates `target/debug` directory where output (it can be executable or library) is saved.
The build command also creates Cargo.lock which containes exact version of dependencies used to build the project. Cargo needs to lock versions because versions of dependencies in Cargo.toml are not exact but rather specify expected "compatibility version" while Cargo.lock specifies exact version.

Also you can call:

> cargo run                 # build and run the resulting application
> cargo check               # quick build without making output
> cargo build --release     # build the release version of app and put it in `target/release` dir

## Language concepts

Strict type system.

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
