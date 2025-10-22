# "Classes" in Rust

Rust does not have a `class` keyword in the way that languages like C++, Java, or Python do. Instead, it provides a powerful combination of features that allow you to achieve the goals of object-oriented programming (OOP) in a slightly different, but very effective, way. The primary components are **`structs`**, **`impl`** blocks, and **`traits`**.

---

## 1. Structs: Defining the Data

A `struct` (structure) is used to bundle related data together. This is equivalent to the fields or attributes of a class.

```rust
// Define a struct named Circle with a public field 'radius'.
pub struct Circle {
    pub radius: f64,
}
```

In this example, `Circle` is a struct that holds a single piece of data: `radius`. The `pub` keyword makes the `radius` field accessible from outside the module where `Circle` is defined.

---

## 2. `impl` Blocks: Implementing Behavior

An `impl` (implementation) block is where you define functions and methods associated with your struct. This is where the behavior (methods) of your "class" lives.

```rust
// Implementation block for the Circle struct
impl Circle {
    // An "associated function" that acts as a constructor.
    // It's convention to name this 'new'. It doesn't belong to a specific
    // instance, but to the struct type itself.
    pub fn new(radius: f64) -> Self {
        Self { radius }
    }

    // A "method" that operates on an instance of the struct.
    // The first parameter is always `self`, `&self`, or `&mut self`.
    // `&self` is an immutable reference to the instance.
    pub fn area(&self) -> f64 {
        std::f64::consts::PI * self.radius * self.radius
    }
}
```

### Putting It Together: Instantiation and Usage

You can now create and use an instance of `Circle` just like you would with a class object.

```rust
fn main() {
    // Create a new instance using the associated function 'new'.
    let my_circle = Circle::new(5.0);

    // Call the 'area' method on the instance.
    println!("The area of the circle is: {}", my_circle.area());

    // Access the public field directly.
    println!("The radius is: {}", my_circle.radius);
}
```

---

## 3. Traits: Shared Behavior and Interfaces

A `trait` is how Rust defines shared behavior across different structs. It's similar to an `interface` in other languages. Traits allow for a form of polymorphism.

Let's define a `Shape` trait that requires any implementing type to have an `area` method.

```rust
// Define a trait for any shape that has an area.
pub trait Shape {
    fn area(&self) -> f64;
}

// Now, let's define another struct.
pub struct Rectangle {
    pub width: f64,
    pub height: f64,
}

// Implement the Shape trait for Rectangle.
impl Shape for Rectangle {
    fn area(&self) -> f64 {
        self.width * self.height
    }
}

// We also need to implement it for our Circle.
// Let's imagine we already have the Circle struct and its impl block.
// We can add a new impl block to implement the trait.
impl Shape for Circle {
    fn area(&self) -> f64 {
        std::f64::consts::PI * self.radius * self.radius
    }
}
```

Now you can write functions that accept any type that implements the `Shape` trait:

```rust
// This function can take any type that implements the Shape trait.
fn print_area(shape: &impl Shape) {
    println!("This shape has an area of: {}", shape.area());
}

fn main() {
    let circle = Circle { radius: 10.0 };
    let rectangle = Rectangle { width: 3.0, height: 4.0 };

    print_area(&circle);      // Works with Circle
    print_area(&rectangle);   // Works with Rectangle
}
```

---

## Inheritance in Rust (Composition and Traits)

Rust does not have a direct concept of class inheritance as found in object-oriented languages like Java or C++. Instead, Rust encourages different patterns to achieve code reuse and polymorphism: **composition** and **traits**.

### Composition

Composition is the practice of building complex types by combining simpler types. Instead of inheriting from a base class, a struct can contain instances of other structs.

```rust
// A base component
pub struct Engine {
    pub horsepower: u32,
}

impl Engine {
    pub fn new(horsepower: u32) -> Self {
        Engine { horsepower }
    }

    pub fn start(&self) {
        println!("Engine with {} HP started!");
    }
}

// A struct that "has-a" (composes) an Engine
pub struct Car {
    pub make: String,
    pub model: String,
    pub engine: Engine, // Car "has-a" Engine
}

impl Car {
    pub fn new(make: String, model: String, horsepower: u32) -> Self {
        Car {
            make,
            model,
            engine: Engine::new(horsepower),
        }
    }

    pub fn drive(&self) {
        println!("Driving a {} {}...", self.make, self.model);
        self.engine.start(); // Delegate behavior to the composed Engine
    }
}

fn main() {
    let my_car = Car::new(String::from("Toyota"), String::from("Camry"), 150);
    my_car.drive();
}
```
In this example, `Car` doesn't inherit from `Engine`; instead, it *contains* an `Engine`. The `Car` delegates the `start` behavior to its `engine` field.

### Traits for Polymorphism

As discussed earlier, traits provide a way to define shared behavior (interfaces) that different types can implement. This allows for polymorphism without inheritance.

---

## Encapsulation (Public and Private Methods/Fields)

Rust uses its **module system** to control the visibility (public or private) of items (functions, structs, enums, methods, fields). By default, everything in Rust is **private** to its containing module.

### `pub` Keyword

To make an item public and accessible from outside its module, you use the `pub` keyword.

```rust
// In a file like `src/lib.rs` or `src/main.rs`

mod my_module {
    // This struct is public, so it can be used outside `my_module`.
    pub struct MyStruct {
        // This field is public, so it can be accessed directly.
        pub public_field: i32,
        // This field is private by default.
        private_field: String,
    }

    impl MyStruct {
        // This associated function (constructor) is public.
        pub fn new(public_val: i32, private_val: String) -> Self {
            MyStruct {
                public_field: public_val,
                private_field: private_val,
            }
        }

        // This method is public.
        pub fn get_private_field(&self) -> &str {
            &self.private_field
        }

        // This method is private by default.
        fn internal_logic(&self) {
            println!("Running internal logic.");
        }
    }

    // This function is public.
    pub fn public_function() {
        println!("This is a public function.");
    }

    // This function is private by default.
    fn private_function() {
        println!("This is a private function.");
    }
}

fn main() {
    use my_module::MyStruct;
    use my_module::public_function;

    let instance = MyStruct::new(10, String::from("secret"));

    println!("Public field: {}", instance.public_field);
    // println!("Private field: {}", instance.private_field); // ERROR: `private_field` is private

    println!("Private field via public method: {}", instance.get_private_field());

    public_function();
    // my_module::private_function(); // ERROR: `private_function` is private
}
```

### Key Points on Encapsulation:

*   **Default Privacy:** Everything is private unless explicitly marked `pub`.
*   **Granular Control:** You can control visibility at the module, struct, field, enum, and function/method level.
*   **`pub(crate)`:** Makes an item public within the current crate, but private to external crates.
*   **`pub(super)`:** Makes an item public to the parent module.
*   **`pub(in path)`:** Makes an item public within a specific path.

This system allows for strong encapsulation, ensuring that the internal implementation details of a module or type can be changed without affecting external code that uses it.

## Summary

| OOP Concept     | Rust Equivalent                               | Description                                                  |
|-----------------|-----------------------------------------------|--------------------------------------------------------------|
| **Class**       | `struct` + `impl`                             | A struct holds data; an impl block defines its methods.      |
| **Fields**      | `struct` fields                               | Variables that belong to an object.                          |
| **Methods**     | Functions in an `impl` block                  | Functions that operate on an object instance (`&self`).      |
| **Constructor** | Associated function (conventionally `new`)    | A function that creates and returns a new instance.          |
| **Static Method**| Associated function                           | A function associated with the type, not a specific instance.|
| **Interface**   | `trait`                                       | Defines a set of methods that a type must implement.         |
| **Inheritance** | Composition and Traits (No direct inheritance)| Rust prefers composing functionality via traits over class inheritance. |
| **Encapsulation**| Module system (`pub`)                         | By default, fields and methods are private to their module.  |

The Rust approach encourages composition over inheritance and leverages the type system and compiler to ensure memory safety and prevent common bugs found in traditional OOP languages.