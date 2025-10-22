# Rust Data Types

Rust is a statically typed language, meaning it must know the types of all variables at compile time. However, the compiler is often smart enough to infer the type, so you don't always have to write it explicitly.

Rust's data types can be broadly categorized as follows:

## 1. Scalar Types

Scalar types represent a single value.

*   **Integers:**
    *   Signed: `i8`, `i16`, `i32`, `i64`, `i128` (can store negative or positive numbers)
    *   Unsigned: `u8`, `u16`, `u32`, `u64`, `u128` (can only store positive numbers)
    *   Architecture-dependent: `isize`, `usize` (pointer size, typically 32 or 64 bits)
    *   *Example:* `let x: i32 = 42;`

*   **Floating-Point Numbers:**
    *   `f32` (single-precision)
    *   `f64` (double-precision, default)
    *   *Example:* `let pi: f64 = 3.14159;`

*   **Booleans:**
    *   `bool` (can be `true` or `false`)
    *   *Example:* `let is_rust_fun: bool = true;`

*   **Characters:**
    *   `char` (represents a single Unicode scalar value, 4 bytes in size)
    *   *Example:* `let initial: char = 'R';`

## 2. Compound Types

Compound types can group multiple values into one type.

*   **Tuples:**
    *   A general-purpose way of grouping together a number of values with a variety of types into one compound type.
    *   Fixed length.
    *   *Example:* `let person: (&str, i32, bool) = ("Alice", 30, true);`

*   **Arrays:**
    *   A collection of values of the *same type*.
    *   Fixed length.
    *   Stored on the stack.
    *   *Example:* `let numbers: [i32; 5] = [1, 2, 3, 4, 5];`

*   **Slices:**
    *   A reference to a contiguous sequence of elements in a collection (like an array or `Vec`).
    *   They don't have ownership.
    *   *Example:* `let a = [1, 2, 3, 4, 5]; let slice = &a[1..4];`

## 3. User-Defined Types

These types are defined by the programmer.

*   **Structs:**
    *   Custom data structures that let you name and package together multiple related values.
    *   *Example:*
        ```rust
        struct Point {
            x: i32,
            y: i32,
        }
        let origin = Point { x: 0, y: 0 };
        ```

*   **Enums:**
    *   Allow you to define a type by enumerating its possible variants. Can hold data.
    *   *Example:*
        ```rust
        enum TrafficLight {
            Red,
            Yellow,
            Green,
        }
        let light = TrafficLight::Red;
        ```

*   **Unions:**
    *   Similar to C unions, allowing multiple fields to occupy the same memory location.
    *   **Unsafe** to use, primarily for FFI.
    *   *Example:*
        ```rust
        #[repr(C)]
        union MyUnion {
            i: u32,
            f: f32,
        }
        // Usage requires `unsafe` blocks.
        ```

## 4. Pointers and References

Rust uses references for borrowing values without taking ownership. Raw pointers are available for unsafe operations.

*   **References:**
    *   `&T`: Immutable reference (read-only access).
    *   `&mut T`: Mutable reference (read-write access).
    *   *Example:* `let mut s = String::from("hello"); let r1 = &s; let r2 = &mut s;` (Note: `r1` and `r2` cannot coexist in the same scope due to borrowing rules).

*   **Raw Pointers:**
    *   `*const T`: Immutable raw pointer.
    *   `*mut T`: Mutable raw pointer.
    *   **Unsafe** to dereference, used for FFI or highly optimized code.
    *   *Example:* `let num = 5; let r = &num as *const i32;`

## 5. String Types

Rust has two main string types.

*   **String Slices (`&str`):**
    *   An immutable view into a UTF-8 encoded string data.
    *   Often used for string literals or references to parts of `String`s.
    *   *Example:* `let hello: &str = "Hello, world!";`

*   **Owned Strings (`String`):**
    *   A growable, mutable, owned UTF-8 encoded string type.
    *   Stored on the heap.
    *   *Example:* `let mut s = String::from("hello"); s.push_str(", world!");`

## 6. Other Important Types

These are not primitive types but are fundamental to Rust programming.

*   **Unit Type (`()`):**
    *   Represents the absence of any value.
    *   Often used as the return type of functions that don't return anything meaningful.
    *   *Example:* `fn do_nothing() -> () { /* ... */ }`

*   **Never Type (`!`):}
    *   Represents a computation that never completes (e.g., a function that always panics or loops forever).
    *   *Example:* `fn forever() -> ! { loop {} }`

*   **Option (`Option<T>`):**
    *   An enum that represents a value that could be present (`Some(T)`) or absent (`None`).
    *   Used to handle the possibility of null values safely.
    *   *Example:* `let some_number = Some(5); let no_number: Option<i32> = None;`

*   **Result (`Result<T, E>`):**
    *   An enum that represents a fallible operation: either success (`Ok(T)`) with a value, or failure (`Err(E)`) with an error.
    *   Used for error handling.
    *   *Example:* `let file_result = File::open("foo.txt");`

*   **Box (`Box<T>`):**
    *   A smart pointer that allocates data on the heap.
    *   Used for recursive data structures or when you need to store a value on the heap.
    *   *Example:* `let b = Box::new(5);`

*   **Reference Counting (`Rc<T>`, `Arc<T>`):
    *   `Rc<T>`: Multiple ownership of data on the heap within a single thread.
    *   `Arc<T>`: Atomic reference counting, for multiple ownership across multiple threads.
    *   *Example:* `let a = Rc::new(String::from("test")); let b = Rc::clone(&a);`

*   **Interior Mutability (`Cell<T>`, `RefCell<T>`):
    *   Types that allow you to mutate data even when you have an immutable reference to the containing type.
    *   `Cell<T>`: For types that implement `Copy`.
    *   `RefCell<T>`: For types that don't implement `Copy`, checks borrowing rules at runtime.
    *   *Example:* `let c = RefCell::new(vec![1, 2, 3]);`

This list covers the most common and fundamental data types in Rust.

---

## Tuple Destructuring

In Rust, you can destructure tuples to extract their individual values into separate variables. This is a very common and convenient way to work with tuple data.

Here are the main ways to destructure tuples:

### 1. Basic Destructuring with `let`

The most common way is to use a `let` statement with a pattern that matches the structure of the tuple.

```rust
fn main() {
    let person_data = ("Alice", 30, true);

    // Destructure the tuple into three separate variables
    let (name, age, is_active) = person_data;

    println!("Name: {}", name);
    println!("Age: {}", age);
    println!("Is Active: {}", is_active);

    // You can also destructure directly when creating the tuple
    let (x, y) = (10, 20);
    println!("x: {}, y: {}", x, y);
}
```

### 2. Accessing Elements by Index (Not Destructuring, but related)

While not destructuring, you can always access individual elements of a tuple using dot notation (`.`) followed by the index (0-based).

```rust
fn main() {
    let coordinates = (100, 200, 300);

    let x = coordinates.0;
    let y = coordinates.1;
    let z = coordinates.2;

    println!("X: {}, Y: {}, Z: {}", x, y, z);
}
```

### 3. Ignoring Elements During Destructuring

You can ignore specific elements of a tuple during destructuring by using an underscore (`_`) in place of a variable name.

```rust
fn main() {
    let product_info = ("Laptop", 1200.00, 15, "Electronics");

    // Destructure, but only extract the name and price, ignoring quantity and category
    let (product_name, price, _, _) = product_info;

    println!("Product: {}", product_name);
    println!("Price: ${}", price);
}
```

### 4. Destructuring in Function Parameters

You can destructure tuples directly in function parameter lists, which can make function signatures cleaner when dealing with tuple inputs.

```rust
fn print_point((x, y): (i32, i32)) {
    println!("Point coordinates: ({}, {})", x, y);
}

fn main() {
    let my_point = (50, 75);
    print_point(my_point);

    // You can also pass a literal tuple
    print_point((10, 20));
}
```

### 5. Destructuring with `match` Expressions

For more complex pattern matching, especially when dealing with nested tuples or enums that contain tuples, `match` expressions are very powerful.

```rust
fn process_status(status: (u32, &str)) {
    match status {
        (200, "OK") => println!("Request successful!"),
        (404, "Not Found") => println!("Error: Resource not found."),
        (code, message) => println!("Unhandled status: {} - {}", code, message),
    }
}

fn main() {
    process_status((200, "OK"));
    process_status((404, "Not Found"));
    process_status((500, "Internal Server Error"));
}
```