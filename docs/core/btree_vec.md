# btree_vec Documentation

## Overview

The `btree_vec` crate provides an implementation of a B-tree based vector data structure. It combines the benefits of a B-tree's efficient insertion, deletion, and search operations with the familiar interface of a vector. This implementation is particularly useful for managing large collections of data where efficient concatenation, splitting, and range operations are needed.

## Purpose and Use Cases

The BTreeVec implementation is particularly useful for:

- Managing large collections of data that need efficient insertion and deletion
- Implementing data structures for text editors or document processing
- Building rope-like data structures for efficient string manipulation
- Creating data structures that support efficient concatenation and splitting operations
- Working with data where you need to track additional metrics (like length, weight, etc.)

## Key Features

- **B-tree Based**: Built on top of the btree crate for efficient operations
- **Vector-like Interface**: Provides a familiar vector-like API
- **Efficient Operations**: O(log n) insertion, deletion, and search operations
- **Range Operations**: Efficient operations on ranges of data
- **Custom Metrics**: Support for tracking custom metrics (length, weight, etc.)
- **Memory Efficient**: Uses ArrayVec for leaf nodes and Arc for shared ownership
- **Iterator Support**: Full support for iterators in both directions
- **Cursor-based Traversal**: Efficient traversal using cursors

## Getting Started

To use the `btree_vec` crate, simply create a new BTreeVec and start adding elements:

```rust
use btree_vec::BTreeVec;

// Create a new BTreeVec
let mut vec: BTreeVec<i32> = BTreeVec::new();

// Add elements
vec.push_back(1);
vec.push_back(2);
vec.push_back(3);

// Access elements
assert_eq!(vec[0], 1);
assert_eq!(vec.len(), 3);

// Iterate over elements
for item in &vec {
    println!("{}", item);
}
```

## Core Concepts

### BTreeVec Structure

A BTreeVec is built on top of a B-tree where each leaf node contains an ArrayVec of elements. This structure provides efficient operations while maintaining the familiar vector interface.

### Metrics

BTreeVec supports custom metrics through the `Metric` trait. By default, it uses a simple counting metric, but you can implement custom metrics to track additional information like total length of strings, sum of weights, etc.

```rust
use btree_vec::{BTreeVec, Metric, Measure};

// Define a custom metric that tracks the sum of values
struct SumMetric;

impl Metric<i32> for SumMetric {
    type Measure = i32;
    
    fn measure(item: &i32) -> Self::Measure {
        *item
    }
}

// Create a BTreeVec with the custom metric
let mut vec: BTreeVec<i32, SumMetric> = BTreeVec::new();
vec.push_back(10);
vec.push_back(20);
vec.push_back(30);

// Get the total sum
assert_eq!(vec.measure(), 60);
```

### Cursors

Cursors provide efficient traversal of the BTreeVec without needing to repeatedly search the tree:

```rust
use btree_vec::BTreeVec;

let vec: BTreeVec<i32> = (0..100).collect();

// Create a cursor at the start
let mut cursor = vec.cursor_start();

// Move through the elements
while let Some(item) = cursor.current() {
    println!("Current item: {}", item);
    if !cursor.move_next() {
        break;
    }
}
```

## Basic Operations

### Creating a BTreeVec

```rust
use btree_vec::BTreeVec;

// Create an empty BTreeVec
let vec: BTreeVec<i32> = BTreeVec::new();

// Create from a Vec
let vec: BTreeVec<i32> = vec![1, 2, 3, 4, 5].into();

// Create from an iterator
let vec: BTreeVec<i32> = (0..10).collect();
```

### Adding Elements

```rust
let mut vec: BTreeVec<i32> = BTreeVec::new();

// Add to the back
vec.push_back(1);

// Add to the front
vec.push_front(0);

// Insert at a specific index
vec.insert(1, 5);

// Append another BTreeVec
let mut other: BTreeVec<i32> = vec![6, 7, 8].into();
vec.append(other);
```

### Removing Elements

```rust
let mut vec: BTreeVec<i32> = (0..10).collect();

// Remove from the back
let last = vec.pop_back();

// Remove from the front
let first = vec.pop_front();

// Remove at a specific index
let item = vec.remove(3);

// Remove a range
vec.remove_from(5);  // Remove everything from index 5 onwards
vec.remove_to(3);    // Remove everything up to index 3
```

### Accessing Elements

```rust
let vec: BTreeVec<i32> = (0..10).collect();

// Index access
let item = vec[5];

// Safe access
let item = vec.get(5);

// Get first and last elements
let first = vec.front();
let last = vec.back();
```

### Iteration

```rust
let vec: BTreeVec<i32> = (0..10).collect();

// Forward iteration
for item in &vec {
    println!("{}", item);
}

// Reverse iteration
for item in vec.iter_rev() {
    println!("{}", item);
}

// Using iterators
let doubled: Vec<i32> = vec.iter().map(|x| x * 2).collect();
```

## Advanced Features

### Custom Metrics

You can define custom metrics to track additional information about your data:

```rust
use btree_vec::{BTreeVec, Metric, Measure};

// Define a metric that tracks string length
struct StringLengthMetric;

impl Metric<String> for StringLengthMetric {
    type Measure = usize;
    
    fn measure(item: &String) -> Self::Measure {
        item.len()
    }
}

let mut vec: BTreeVec<String, StringLengthMetric> = BTreeVec::new();
vec.push_back("Hello".to_string());
vec.push_back("World".to_string());

// Get total length of all strings
assert_eq!(vec.measure(), 10);
```

### Range Operations

BTreeVec excels at range operations:

```rust
let mut vec: BTreeVec<i32> = (0..10).collect();
let other: BTreeVec<i32> = (100..105).collect();

// Replace a range
vec.replace_range(3, 7, other);

// Split the vector
let other = vec.split_off(5);
```

### Working with Cursors

Cursors provide efficient traversal:

```rust
let vec: BTreeVec<i32> = (0..100).collect();
let mut cursor = vec.cursor(50);

// Move around efficiently
cursor.move_prev();
cursor.move_next();
cursor.move_next_chunk();  // Move to next leaf node

// Access current element
if let Some(item) = cursor.current() {
    println!("Current item: {}", item);
}
```

## Performance Considerations

### Time Complexity

- Access by index: O(log n)
- Insertion at ends: O(log n)
- Insertion in middle: O(log n)
- Deletion: O(log n)
- Iteration: O(n)
- Concatenation: O(log n)
- Splitting: O(log n)

### Space Complexity

The space complexity is O(n) where n is the number of elements in the vector. The B-tree structure adds some overhead for maintaining the branching structure.

## Best Practices

1. **Use for Large Collections**: BTreeVec is most beneficial for large collections where efficient range operations are needed
2. **Prefer push_back/push_front**: When possible, use these operations as they're more efficient than insertions in the middle
3. **Use Cursors for Traversal**: For efficient traversal, use cursors instead of repeated indexing
4. **Implement Custom Metrics**: When you need to track additional information, implement custom metrics
5. **Batch Operations**: When possible, batch operations together to minimize tree restructuring

## Examples

### Text Buffer Implementation

```rust
use btree_vec::{BTreeVec, Metric, Measure};

// Define a metric that tracks character count
struct CharCountMetric;

impl Metric<String> for CharCountMetric {
    type Measure = usize;
    
    fn measure(item: &String) -> Self::Measure {
        item.chars().count()
    }
}

fn main() {
    let mut buffer: BTreeVec<String, CharCountMetric> = BTreeVec::new();
    
    // Add lines of text
    buffer.push_back("Hello, ".to_string());
    buffer.push_back("world!".to_string());
    buffer.push_back(" This is a".to_string());
    buffer.push_back(" BTreeVec example.".to_string());
    
    // Get total character count
    println!("Total characters: {}", buffer.measure());
    
    // Efficiently insert text in the middle
    buffer.insert(2, "beautiful".to_string());
    
    // Convert to regular Vec for processing
    let lines: Vec<String> = buffer.to_vec();
    println!("Buffer content: {:?}", lines);
}
```

### Working with Custom Data

```rust
use btree_vec::{BTreeVec, Metric, Measure};

#[derive(Clone, Debug)]
struct Person {
    name: String,
    age: u32,
}

struct AgeSumMetric;

impl Metric<Person> for AgeSumMetric {
    type Measure = u32;
    
    fn measure(item: &Person) -> Self::Measure {
        item.age
    }
}

fn main() {
    let mut people: BTreeVec<Person, AgeSumMetric> = BTreeVec::new();
    
    people.push_back(Person { name: "Alice".to_string(), age: 30 });
    people.push_back(Person { name: "Bob".to_string(), age: 25 });
    people.push_back(Person { name: "Charlie".to_string(), age: 35 });
    
    // Get total age
    println!("Total age: {}", people.measure());
    
    // Find average age
    if !people.is_empty() {
        let average_age = people.measure() / people.len() as u32;
        println!("Average age: {}", average_age);
    }
}
```

### Efficient Text Processing

```rust
use btree_vec::BTreeVec;

fn process_large_text() {
    // Create a large text buffer
    let mut text: BTreeVec<String> = BTreeVec::new();
    
    // Add many lines efficiently
    for i in 0..10000 {
        text.push_back(format!("Line {}", i));
    }
    
    // Efficiently process in chunks
    let mut cursor = text.cursor_start();
    while !cursor.is_end() {
        let chunk = cursor.current_chunk();
        for line in chunk {
            // Process each line
            println!("Processing: {}", line);
        }
        cursor.move_next_chunk();
    }
    
    // Split the text at a specific point
    let second_half = text.split_off(500);
    println!("First half has {} lines", text.len());
    println!("Second half has {} lines", second_half.len());
}