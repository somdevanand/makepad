# array_vec Documentation

## Overview

The `array_vec` crate provides a vector-like data structure that stores its elements inline on the stack. Unlike the standard `Vec` which allocates memory on the heap, `ArrayVec` uses a fixed-size array as its backing storage, making it ideal for situations where you need a collection with a known maximum size and want to avoid heap allocations.

## Purpose and Use Cases

`ArrayVec` is particularly useful in scenarios where:

- You need a collection with a fixed maximum capacity
- You want to avoid heap allocations for performance reasons
- You're working in environments where heap allocation is restricted or expensive
- You need predictable memory usage patterns
- You're building embedded systems or real-time applications

## Key Features

- **Stack Allocation**: Stores elements inline on the stack for better performance
- **Fixed Capacity**: Maximum size is determined at compile time through a const generic parameter
- **Zero Heap Allocations**: No dynamic memory allocation required
- **Standard API Compatibility**: Implements many of the same methods as `std::vec::Vec`
- **Safety**: Provides safe access to elements with bounds checking
- **Performance**: Faster access times due to stack allocation and cache locality

## Getting Started

To use the `array_vec` crate, add it to your `Cargo.toml`:

```toml
[dependencies]
array_vec = "0.1"
```

Then, in your Rust code:

```rust
use array_vec::ArrayVec;

// Create an ArrayVec with a capacity of 10 elements
let mut vec: ArrayVec<i32, 10> = ArrayVec::new();

// Add elements to the vector
vec.push(1);
vec.push(2);
vec.push(3);

// Access elements
assert_eq!(vec[0], 1);
assert_eq!(vec.len(), 3);
assert_eq!(vec.capacity(), 10);
```

## Core Concepts

### Generic Parameters

`ArrayVec<T, const N: usize>` has two generic parameters:
- `T`: The type of elements stored in the vector
- `N`: The maximum capacity of the vector, specified as a compile-time constant

### Stack vs Heap Allocation

Unlike `std::vec::Vec`, `ArrayVec` stores its elements directly on the stack:

```rust
// Stack allocated - size is known at compile time
let mut array_vec: ArrayVec<i32, 100> = ArrayVec::new();

// Heap allocated - size can grow dynamically
let mut std_vec: Vec<i32> = Vec::new();
```

## Basic Operations

### Creating an ArrayVec

```rust
use array_vec::ArrayVec;

// Create an empty ArrayVec with capacity 10
let mut vec: ArrayVec<i32, 10> = ArrayVec::new();

// Create from an array
let vec: ArrayVec<i32, 3> = ArrayVec::from([1, 2, 3]);

// Create from an iterator
let vec: ArrayVec<i32, 5> = (0..3).collect();
```

### Adding Elements

```rust
let mut vec: ArrayVec<i32, 5> = ArrayVec::new();

// Add a single element
vec.push(42);

// Insert at a specific index
vec.insert(0, 10);

// Extend with a slice
vec.extend_from_slice(&[1, 2, 3]);
```

### Removing Elements

```rust
let mut vec: ArrayVec<i32, 5> = ArrayVec::from([1, 2, 3, 4, 5]);

// Remove the last element
let last = vec.pop(); // Returns Some(5)

// Remove at a specific index
let removed = vec.remove(0); // Returns 1

// Clear all elements
vec.clear();
```

### Accessing Elements

```rust
let vec: ArrayVec<i32, 5> = ArrayVec::from([10, 20, 30, 40, 50]);

// Index access
let first = vec[0]; // Returns 10

// Safe access
let maybe_third = vec.get(2); // Returns Some(&30)
let maybe_tenth = vec.get(9); // Returns None

// Iteration
for element in &vec {
    println!("{}", element);
}
```

## Error Handling

Since `ArrayVec` has a fixed capacity, operations that would exceed the capacity return a `CapacityError`:

```rust
use array_vec::{ArrayVec, CapacityError};

let mut vec: ArrayVec<i32, 2> = ArrayVec::new();
vec.push(1);
vec.push(2);

// This will return an error since capacity is exceeded
match vec.try_push(3) {
    Ok(()) => println!("Element added successfully"),
    Err(CapacityError) => println!("Vector is at capacity"),
}
```

## Performance Considerations

### Advantages

1. **No Heap Allocations**: All memory is allocated on the stack
2. **Cache Locality**: Elements are stored contiguously in memory
3. **Predictable Performance**: No allocation overhead or resizing
4. **Memory Safety**: Compile-time capacity checking

### Trade-offs

1. **Fixed Size**: Maximum capacity must be known at compile time
2. **Stack Usage**: Large capacities consume significant stack space
3. **No Resizing**: Cannot grow beyond the initial capacity

## Advanced Features

### Splicing

Replace a range of elements with new ones:

```rust
let mut vec: ArrayVec<i32, 10> = ArrayVec::from([1, 2, 3, 4, 5]);
let new_elements = vec![10, 20, 30];

// Replace elements at indices 1..3 with new elements
let removed: Vec<i32> = vec.splice(1..3, new_elements).collect();
assert_eq!(removed, vec![2, 3]);
assert_eq!(vec.as_slice(), &[1, 10, 20, 30, 4, 5]);
```

### Draining

Remove and iterate over a range of elements:

```rust
let mut vec: ArrayVec<i32, 10> = ArrayVec::from([1, 2, 3, 4, 5]);

// Remove and iterate over elements at indices 1..4
let drained: Vec<i32> = vec.drain(1..4).collect();
assert_eq!(drained, vec![2, 3, 4]);
assert_eq!(vec.as_slice(), &[1, 5]);
```

### Truncation

Reduce the length of the vector:

```rust
let mut vec: ArrayVec<i32, 10> = ArrayVec::from([1, 2, 3, 4, 5]);
vec.truncate(3); // Keep only first 3 elements
assert_eq!(vec.as_slice(), &[1, 2, 3]);
```

## Integration with Standard Library

`ArrayVec` implements many standard traits for seamless integration:

```rust
use array_vec::ArrayVec;

// Clone
let vec1: ArrayVec<i32, 5> = ArrayVec::from([1, 2, 3]);
let vec2 = vec1.clone();

// Debug
println!("{:?}", vec1); // [1, 2, 3]

// Equality
assert_eq!(vec1, vec2);

// Hash
use std::collections::HashSet;
let mut set = HashSet::new();
set.insert(vec1);

// Extend
let mut vec: ArrayVec<i32, 10> = ArrayVec::new();
vec.extend([1, 2, 3]);

// FromIterator
let vec: ArrayVec<i32, 5> = (0..3).collect();
```

## Best Practices

1. **Choose Appropriate Capacity**: Select a capacity that balances memory usage and functionality needs
2. **Handle Capacity Errors**: Use `try_*` methods when you're unsure if an operation will succeed
3. **Prefer Stack Allocation**: Use `ArrayVec` when you know the maximum size at compile time
4. **Avoid Large Capacities**: Large stack allocations can cause stack overflow issues
5. **Use Deref**: `ArrayVec` implements `Deref<Target = [T]>`, so you can use slice methods directly

## Examples

### Basic Usage

```rust
use array_vec::ArrayVec;

fn main() {
    // Create an ArrayVec with capacity for 5 strings
    let mut names: ArrayVec<String, 5> = ArrayVec::new();
    
    // Add some names
    names.push("Alice".to_string());
    names.push("Bob".to_string());
    names.push("Charlie".to_string());
    
    // Iterate over names
    for name in &names {
        println!("Hello, {}!", name);
    }
    
    // Check capacity and length
    println!("Capacity: {}", names.capacity());
    println!("Length: {}", names.len());
}
```

### Fixed-Size Buffer

```rust
use array_vec::ArrayVec;

fn process_data_stream() {
    // Fixed-size buffer for processing data
    let mut buffer: ArrayVec<u8, 1024> = ArrayVec::new();
    
    // Simulate reading data
    for i in 0..100 {
        if buffer.len() == buffer.capacity() {
            // Process full buffer
            process_buffer(&buffer);
            buffer.clear();
        }
        buffer.push(i as u8);
    }
    
    // Process remaining data
    if !buffer.is_empty() {
        process_buffer(&buffer);
    }
}

fn process_buffer(buffer: &[u8]) {
    // Process the buffer data
    println!("Processing {} bytes", buffer.len());
}
```

### Error Handling

```rust
use array_vec::{ArrayVec, CapacityError};

fn add_elements_safely() -> Result<(), CapacityError> {
    let mut vec: ArrayVec<i32, 3> = ArrayVec::new();
    
    // These operations might fail if capacity is exceeded
    vec.try_push(1)?;
    vec.try_push(2)?;
    vec.try_push(3)?;
    
    // This would return an error
    // vec.try_push(4)?;
    
    Ok(())
}