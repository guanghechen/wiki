# Smart Pointers in Rust

In Rust, smart pointers are data structures that act like pointers but also have additional metadata and capabilities. They manage memory, ownership, and other resources, providing more functionality than a simple reference. They are a key part of Rust's ownership system, enabling safe memory management without a garbage collector.

Here are some of the most common smart pointers in Rust:

## 1. `Box<T>` (Heap Allocation)

*   **Purpose:** Allows you to store data on the heap rather than the stack. When a `Box` goes out of scope, its destructor runs, and the heap memory is freed.
*   **Characteristics:**
    *   **Single Ownership:** A `Box` has sole ownership of the data it points to.
    *   **Fixed Size on Stack:** The `Box` itself is a pointer on the stack, but the data it points to is on the heap. This is useful for types whose size cannot be known at compile time (e.g., recursive types) or when you have a large amount of data that would otherwise overflow the stack.
*   **Use Cases:**
    *   When you have a type whose size can't be known at compile time and you want to use a value of that type in a context that requires an exact size.
    *   When you have a large amount of data and want to transfer ownership without copying the data.
    *   When you want to own a trait object (e.g., `Box<dyn Trait>`).

```rust
fn main() {
    let b = Box::new(5); // 'b' is a Box pointing to an integer 5 on the heap
    println!("b = {}", b);

    // Example with a recursive data structure (like a linked list)
    enum List {
        Cons(i32, Box<List>),
        Nil,
    }
    let list = List::Cons(1, Box::new(List::Cons(2, Box::new(List::Nil))));
}
```

## 2. `Rc<T>` (Reference Counting)

*   **Purpose:** Enables multiple ownership of data. `Rc` stands for "reference counting." When there are multiple owners, the data is only cleaned up when the last owner goes out of scope.
*   **Characteristics:**
    *   **Multiple Immutable Owners:** Allows multiple parts of your code to own the same data, but the data itself is immutable through `Rc<T>`.
    *   **Single-Threaded:** Only works within a single thread. Not safe for sharing across threads.
    *   **Runtime Overhead:** Reference counts are updated at runtime, incurring a small performance cost.
*   **Use Cases:**
    *   When you need to share data among multiple parts of your program, and you know that data will only be accessed from a single thread.
    *   Graph data structures where multiple nodes might point to the same data.

```rust
use std::rc::Rc;

fn main() {
    let five = Rc::new(5);
    let five_clone = Rc::clone(&five); // Increment reference count
    let five_another = Rc::clone(&five);

    println!("Reference count: {}", Rc::strong_count(&five)); // Output: 3

    // Data is dropped when the last Rc goes out of scope
    drop(five_clone);
    println!("Reference count after drop: {}", Rc::strong_count(&five)); // Output: 2
}
```

## 3. `Arc<T>` (Atomic Reference Counting)

*   **Purpose:** Similar to `Rc<T>`, but provides thread-safe multiple ownership. `Arc` stands for "atomic reference counting."
*   **Characteristics:**
    *   **Multiple Immutable Owners:** Allows multiple parts of your code to own the same data.
    *   **Multi-Threaded (Thread-Safe):** Safe to share across multiple threads.
    *   **Higher Runtime Overhead:** Uses atomic operations for reference counting, which are slower than non-atomic operations but necessary for thread safety.
*   **Use Cases:**
    *   When you need to share data among multiple threads.

```rust
use std::sync::Arc;
use std::thread;

fn main() {
    let five = Arc::new(5);
    let five_clone = Arc::clone(&five);

    let handle = thread::spawn(move || {
        println!("Value from thread: {}", five_clone);
    });

    handle.join().unwrap();
    println!("Value from main: {}", five);
}
```

## 4. `RefCell<T>` (Interior Mutability)

*   **Purpose:** Allows you to mutate data even when you have an immutable reference to the data's owner. This is known as "interior mutability."
*   **Characteristics:**
    *   **Single-Threaded:** Only works within a single thread.
    *   **Runtime Borrow Checking:** Enforces Rust's borrowing rules at runtime, panicking if rules are violated.
    *   **Mutable Borrow through Immutable Reference:** Enables scenarios where you need to modify data that is otherwise immutably borrowed (e.g., in `Rc<T>`).
*   **Use Cases:**
    *   When you need to mutate data inside an `Rc<T>`.
    *   When you have a data structure that needs to update its internal state even when it's logically immutable from the outside (e.g., a cache).

```rust
use std::cell::RefCell;

fn main() {
    let x = RefCell::new(vec![1, 2, 3]);
    let y = &x; // Immutable reference to RefCell

    // We can still mutate the inner vector through the immutable reference 'y'
    y.borrow_mut().push(4);

    println!("{:?}", x.borrow()); // Output: [1, 2, 3, 4]
}
```

## 5. `Cell<T>` (Interior Mutability for `Copy` Types)

*   **Purpose:** Similar to `RefCell<T>`, but specifically for types that implement the `Copy` trait. It allows interior mutability by replacing the entire value.
*   **Characteristics:**
    *   **Single-Threaded:** Only works within a single thread.
    *   **No Runtime Borrow Checking:** Because it only works with `Copy` types, it doesn't need runtime borrow checks; it simply replaces the value.
*   **Use Cases:**
    *   When you need interior mutability for small, `Copy` types (like integers, booleans, characters).

```rust
use std::cell::Cell;

fn main() {
    let x = Cell::new(5);
    let y = &x; // Immutable reference to Cell

    y.set(10); // Mutate the inner value

    println!("Value: {}", x.get()); // Output: 10
}
```

## 6. `Weak<T>` (Non-Owning Reference)

*   **Purpose:** Used in conjunction with `Rc<T>` (or `Arc<T>`) to create non-owning references. This is crucial for breaking reference cycles that would otherwise lead to memory leaks.
*   **Characteristics:**
    *   **Non-Owning:** Does not contribute to the reference count of the `Rc<T>` it points to.
    *   **Can Be Upgraded:** Can be upgraded to an `Rc<T>` (or `Arc<T>`) if the data still exists. If the data has been dropped, `upgrade()` returns `None`.
*   **Use Cases:**
    *   Implementing parent-child relationships in tree-like data structures where children own parents, but parents don't own children (to avoid cycles).

```rust
use std::rc::{Rc, Weak};

struct Node {
    value: i32,
    parent: Option<Weak<Node>>,
    children: RefCell<Vec<Rc<Node>>>, // RefCell for interior mutability of children
}

fn main() {
    let leaf = Rc::new(Node {
        value: 3,
        parent: None,
        children: RefCell::new(vec![]),
    });

    let branch = Rc::new(Node {
        value: 5,
        parent: Some(Rc::downgrade(&leaf)), // Parent is a Weak reference
        children: RefCell::new(vec![Rc::clone(&leaf)]),
    });

    // Now, make the leaf's parent point to the branch
    // This would create a cycle if 'parent' was Rc<Node>
    // leaf.parent = Some(Rc::downgrade(&branch)); // This line would cause a cycle if uncommented and parent was Rc

    println!("Leaf parent: {:?}", leaf.parent.as_ref().and_then(|w| w.upgrade()).map(|n| n.value));
    println!("Branch parent: {:?}", branch.parent.as_ref().and_then(|w| w.upgrade()).map(|n| n.value));
}
```