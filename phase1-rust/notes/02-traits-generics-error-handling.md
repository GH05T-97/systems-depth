## Rust - Traits, Generics & Error Handling

### Traits

A trait defines the functionality a particular type has and can share with other types. We can use traits to define shared behavior in an abstract way. In other languages, this feature would be known as an interface - such as with TypeScript.

Below is how you instaniate a trait

```rust
pub trait Summary {
    fn summarize(&self) -> String;
}
```

The code snippet above showcases the trait - Summary which has a function called summarise which will return a string. Anything that implements this trait will have to include the summarize function which will have to return a string, otherwise, the compiler will throw an error.

```rust
pub struct NewsArticle {
    pub headline: String,
    pub location: String,
    pub author: String,
    pub content: String,
}

impl Summary for NewsArticle {
    fn summarize(&self) -> String {
        format!("{}, by {} ({})", self.headline, self.author, self.location)
    }
}
```

Now we have a struct called NewsArticle which will implement the trait Summary - therefore the implementation of NewsArticle MUST include any methods/functions that have been pre-defined in the trait Summary in the first code snippet.

We can also use traits as parameters in functions

```rust
pub fn notify(item: &impl Summary) {
    println!("Breaking news! {}", item.summarize());
}
```

The above function has a parameter called item - the type of the item data-structure is irrelevant, all we know for a fact is that it will implement Summary as a trait. Code that calls the function with any other type, such as a String or an i32, won’t compile, because those types don’t implement Summary.

#### From & Into

#### Deref & DerefMut

#### Drop & Iterator


### Generics
When using generics - we place the generics in the signatures of the function - ie - where we would usually specify the data types of the parameters and return value.

```rust
fn largest<T>(list: &[T]) -> &T {
    let mut largest = &list[0];

    for item in list {
        if item > largest {
            largest = item;
        }
    }

    largest
}
```

So the function above - largest accepts a parameter of a generic type enclosed in an array and returns a single generic type.

However, the code snippet will fail to compile. The code fails because the generic type T has no trait bounds, so the compiler doesn't know that values of type T can be compared with >. Rust won't let you use operators on arbitrary generic types — you have to explicitly declare what capabilities T has.

The comparison item > largest requires T to implement std::cmp::PartialOrd.

```rust
fn largest<T: PartialOrd>(list: &[T]) -> &T {...}

```
### Error Handling

Rust has 2 groups of errors

1) Recoverable - for eg: file not found error, we most likely just want to report the problem to the user and retry the operation
2) Uncrecoverable - always symptoms of bugs, such as trying to access a location beyond the end of an array, and so we want to immediately stop the program

If you program fails in a manner that is unrecoverable, you can call the panic macro

```rust
fn run(){
    panic!("Error running this operation!")
}
```
For errors that are recoverable, we can use the Result enum - which represents a success in an operation and a failure in an operation

```rust
use std::fs::File;

fn run(){
    let f: Result<File, Error> = File::open("hello_world.txt");
    let f: File = match f {
        Ok((file)) => File,
        Err(error) => panic("Error opening file!: {;?}", e)
}
```

In the above example, we have the open operation from the File component imported from the std library. The variable f has a Result enum that is returned, within the enum - we can either return a File in a successful operation or an Error if it fails, the code snippet below showcases how we can handle successful and failures using the match keyword in Rust.

If we wanted to be more precise and handle errors more gracefully, and be more specific in our scenarios, we can use the ErrorKind component from the std library

```rust
use std::fs::File;
use std::io::ErrorKind

fn run(){
    let f: Result<File, Error> = File::open("hello_world.txt");
    let f: File = match f {
        Ok((file)) => File,
        Err(error) => match error.kind() {
            ErrorKind.NotFound => match File::create("hello_world.txt") {
                Ok(fc: File) => fc,
                Error(e: Error) => panic!("Problem creating the file!: {;?}", e)
            },
            other_error => {
                panic!("Problem opening the file!")
            }
        }
    }
}
```

In the above code snippet - we are importing ErrorKind, and handling 2 edge cases, one if the file hello_world.txt is not found in our directory, the program attempts to create it, if that operation fails, then we have the panic operation. Because match statements are exhaustive on their own, we need to include other scenarios - which is represneted by the other_error edge case.

To make our code more concise - we can use the ? operator, which is incredibly useful for Error Propagation

```rust
fn run() -> Result<String, Error>{
        let mut f = File::open("hello_world.txt")?;
        let mut s = String::new();
        f.read_to_string(&mut s)?;
        Ok(s);
    }
}
```

On the above code snippet on the first line - we are using the ? operator when we attempt to open the text file of interest and also reading it to a string format, this essentially allows error handling without writing long match statements and helps us from not even writing panic statements. Saves us time in the end.

As a general rule of thumb - we should be using the Result enum and ? operator for error propagation to prevent our program from crashing

