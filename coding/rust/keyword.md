# Rust Keywords

Keywords are reserved words in Rust that have special meaning to the compiler. They cannot be used as identifiers (e.g., variable names, function names, struct names, etc.). Understanding keywords is fundamental to writing correct and idiomatic Rust code.

## Main Keywords (In Use)

These keywords are actively used in Rust syntax and have specific functionalities.

### 1. Control Flow

Keywords that manage the execution flow of your program.

*   **`if` / `else`**: Conditional execution.
    ```rust
    fn main() {
        let x = 10;
        if x > 5 {
            println!("x is greater than 5");
        } else {
            println!("x is 5 or less");
        }
    }
    ```
*   **`match`**: Pattern matching for exhaustive checking.
    ```rust
    fn main() {
        let x = Some(5);
        match x {
            Some(val) => println!("Got a value: {}", val),
            None => println!("No value"),
        }
    }
    ```
*   **`loop`**: Infinite loop.
    ```rust
    fn main() {
        let mut count = 0;
        loop {
            count += 1;
            if count == 3 {
                break; // Exit the loop
            }
            println!("Looping...");
        }
    }
    ```
*   **`while`**: Loop with a condition.
    ```rust
    fn main() {
        let mut num = 3;
        while num != 0 {
            println!("{}!", num);
            num -= 1;
        }
    }
    ```
*   **`for`**: Iteration over a range or collection.
    ```rust
    fn main() {
        for i in 1..=3 { // Loop from 1 to 3 inclusive
            println!("{}", i);
        }
    }
    ```
*   **`break`**: Exits the innermost loop.
    *   *Note:* Can return a value from a `loop` expression.
*   **`continue`**: Skips the rest of the current loop iteration and proceeds to the next.
*   **`return`**: Exits the current function and returns a value.
    ```rust
    fn add_one(x: i32) -> i32 {
        return x + 1; // Explicit return
        // x + 1 // Implicit return (common in Rust)
    }
    ```

### 2. Types and Modules

Keywords for defining data structures, organizing code, and managing visibility.

*   **`struct`**: Defines a structure (a custom data type with named fields).
    ```rust
    struct Point {
        x: i32,
        y: i32,
    }
    ```
*   **`enum`**: Defines an enumeration (a type that can be one of several variants).
    ```rust
    enum Message {
        Quit,
        Move { x: i32, y: i32 },
        Write(String),
    }
    ```
*   **`union`**: Defines a union (similar to C unions, primarily for FFI and unsafe code).
    *   *Note:* Accessing union fields is `unsafe`.
*   **`mod`**: Declares a module, used for organizing code into namespaces.
    ```rust
    mod my_module {
        fn hello() {
            println!("Hello from my_module!");
        }
    }
    ```
*   **`use`**: Brings paths (modules, functions, structs, enums) into scope.
    ```rust
    use std::collections::HashMap;
    ```
*   **`crate`**: Refers to the root of the current crate.
*   **`super`**: Refers to the parent module.
*   **`type`**: Creates a type alias, or declares an associated type within a trait.
    ```rust
    type Kilometers = i32; // Type alias
    ```
*   **`fn`**: Declares a function.
*   **`trait`**: Defines a trait (a collection of methods that a type can implement, similar to an interface).
    ```rust
    trait Summary {
        fn summarize(&self) -> String;
    }
    ```
*   **`impl`**: Implements a trait for a type, or defines methods directly on a type.
    ```rust
    struct NewsArticle { /* ... */ }
    impl Summary for NewsArticle { /* ... */ } // Implement trait
    impl NewsArticle { fn new() -> Self { /* ... */ } } // Implement methods
    ```
*   **`extern`**: Used for Foreign Function Interface (FFI) to declare functions or static variables defined in external languages (like C).
    ```rust
    extern "C" {
        fn abs(input: i32) -> i32;
    }
    ```

### 3. Variables and Bindings

Keywords related to declaring variables and managing mutability and ownership.

*   **`let`**: Declares a variable binding.
    ```rust
    let x = 5;
    ```
*   **`mut`**: Makes a variable binding or a reference mutable.
    ```rust
    let mut y = 10;
    y += 1;
    let mut s = String::from("hello");
    let r = &mut s; // Mutable reference
    ```
*   **`const`**: Declares a constant. Constants must have a type annotation and can only be set to a constant expression.
    ```rust
    const MAX_POINTS: u32 = 100_000;
    ```
*   **`static`**: Declares a static variable (lives for the entire program duration) or specifies the `'static` lifetime.
    ```rust
    static HELLO_WORLD: &str = "Hello, world!";
    ```
*   **`ref`**: Used in pattern matching to take a reference to a value rather than moving or copying it.
    ```rust
    let x = 5;
    match x {
        ref r => println!("Got a reference to {}", r),
    }
    ```
*   **`move`**: Used in closures to force them to take ownership of captured variables.
    ```rust
    let x = vec![1, 2, 3];
    let closure = move || println!("{:?}", x); // x is moved into the closure
    closure();
    // println!("{:?}", x); // Error: x is moved
    ```

### 4. Visibility

*   **`pub`**: Makes an item (function, struct, enum, module, field) publicly visible.
    ```rust
    pub fn public_function() { /* ... */ }
    ```

### 5. Special Purpose

*   **`as`**: Used for type casting (e.g., `x as f64`) or for disambiguating trait methods.
*   **`dyn`**: Used for trait objects, enabling dynamic dispatch. (Introduced in Rust 2018 Edition).
    ```rust
    trait Draw { fn draw(&self); }
    fn draw_object(obj: &dyn Draw) { obj.draw(); }
    ```
*   **`self`**: Refers to the instance of the struct or enum on which a method is called.
*   **`Self`**: Refers to the type of the current `impl` block or `trait`.
*   **`unsafe`**: Marks a block of code or a function as "unsafe," allowing operations that the Rust compiler cannot guarantee memory safety for (e.g., dereferencing raw pointers).
    *   *Note:* Using `unsafe` shifts the responsibility for memory safety to the programmer.
*   **`where`**: Specifies additional constraints on generic type parameters or lifetime parameters.
    ```rust
    fn print_debug<T>(item: T) where T: Debug {
        println!("{:?}", item);
    }
    ```

### 6. Boolean Literals

*   **`true`**: Boolean value for true.
*   **`false`**: Boolean value for false.

### 7. Asynchronous Programming (Rust 2018 Edition and later)

*   **`async`**: Used to define an asynchronous function that returns a `Future`.
    ```rust
    async fn fetch_data() -> String { /* ... */ }
    ```
*   **`await`**: Used to pause the execution of an `async` function until a `Future` completes.
    ```rust
    async fn main() {
        let data = fetch_data().await;
        println!("{}", data);
    }
    ```

## Reserved Keywords (for Future Use)

These words are reserved by the Rust compiler and cannot be used as identifiers, even though they don't currently have a specific meaning in the language. They are kept for potential future language extensions.

*   `abstract`
*   `become`
*   `box`
*   `do`
*   `final`
*   `macro`
*   `override`
*   `priv`
*   `try`
*   `typeof`
*   `unsized`
*   `virtual`
*   `yield`

## Important Notes

*   **Cannot be used as Identifiers:** The most crucial rule is that you cannot use any of these keywords as names for variables, functions, types, modules, etc.
*   **Edition-Specific Keywords:** Some keywords like `async`, `await`, and `dyn` were introduced in the Rust 2018 Edition. Older editions might not recognize them or treat them differently.
*   **Contextual Keywords:** While Rust generally has strict keywords, some constructs might *look* like keywords but are actually identifiers in specific contexts (e.g., `union` before it became a keyword, or `default` in trait implementations). However, it's best to avoid using any word that resembles a keyword to prevent confusion.
