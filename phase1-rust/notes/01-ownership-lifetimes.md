## Rust - Ownership & Borrowing

Rust uses the Ownership model. Ownership is a set of rules that sets the show on how a Rust program manages memory. How that is done is with a set of rules that a compiler checks. If these rules are violated, the program itself will not compile. TLDR: There are rules that the Rust compiler has, and if your code breaks them, it simply won't work

This "strictness" allows Rust to have these attributes
1) Control over memory
2) To be Error free
3) Faster runtimes
4) Smaller program size

At compile time, Rust decides if certain variables and results of functions should be put on 2 core data structures. The Stack and the Heap

The Stack is a data-structure that has a LIFO (Last in, First Out) property
The Heap is a tree-like data-structure that has no implied ordering between siblings and has no implied sequence for in-order traversal. - similar to a Binary Search Tree

Key differences
1) Binary Search Tree - keeps values in a sorted order, O(log(n)) for balanced tree - O(n) for a degenerate tree
2) Heap - No implied ordering, O(log(n)) for any heap

So at compile time, Rust decides if variables should be pushed onto the stack or heap. For something to be pushed onto the stack, values must have a known size at compile time and/or have a fixed size type (primitives such a bool or char are examples of this), where as dynamically sized or growable data is stored on the heap, usually via a fixed-size stack handle such as String, Vec, or Box, for eg -  a batch of JSON returned from an API call that needs to be stored.


## Borrowing: Share Nicely or Don't Share at All

Rust uses a borrowing mechanism to access data without direct ownership of it. Instead of passing objects by value like JavaScript does, objects can be passed by reference by using the & operator

`let message = String::from("Hello"); // message owns this string

let greeting = message; // ownership moves to greeting

// println!("{}", message); // ❌ Error: message has been moved`

The message variable owns the value String::from("Hello) which is stored on the Heap. The Heap has a pointer telling the compiler where this value is located. In the second line, the ownership is transferred from the variable message to a new variable called greeting. In the third line, we have an error that signifies the mechanism of ownership

`// This is different from JavaScript where:

// let message = "Hello";
// let greeting = message; // Creates a copy/reference

// console.log(message); // ✅ Works fine in JS`

// Now let's look at borrowing

`let original = String::from("Hello");

let borrowed = &original; // Immutable borrow with &

println!("{}", borrowed); // Prints: Hello

println!("{}", original); // ✅ Still works! original maintains ownership`

If we wanted to log out both the original and borrowed variables successfully and for same logging, we would need to use the & operator. This does not transfer ownership of the value defined originally, but merely allows the borrowed variable to do exactly what the borrowing mechanism is, to borrow the value. The original variable still owns the string however!

Using the & operator creates an immutable reference, meaning the variable thats doing the borrowing, cannot mutate the original value. So how can we mutate the value by using the borrower?

We use the mut keyword


`let mut value = String::from("Hello");

let mut_borrow = &mut value; // Mutable borrow with &mut

mut_borrow.push_str(" World"); // Modify through mutable reference`

By using the mut keyword, we are essentially allowing the borrower to be able to mutate the original value. So now, if we wanted to log out value, it would output "Hello World"

