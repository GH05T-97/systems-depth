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