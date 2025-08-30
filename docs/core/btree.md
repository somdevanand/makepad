# btree Documentation

## Overview

The `btree` crate provides an implementation of a B-tree data structure, which is a self-balancing tree data structure that maintains sorted data and allows for efficient insertion, deletion, and search operations. This implementation is designed to be efficient for large datasets and provides features like cursor-based traversal and slicing.

## Purpose and Use Cases

The B-tree implementation in this crate is particularly useful for:

- Managing large collections of data that need to be kept sorted
- Implementing efficient data structures for text editors or document processing
- Building rope-like data structures for efficient string manipulation
- Creating data structures that support efficient range queries
- Implementing data structures with efficient concatenation and splitting operations

## Key Features

- **Balanced Tree Structure**: Maintains balance to ensure O(log n) operations
- **Efficient Operations**: Insertion, deletion, and search operations in O(log n) time
- **Cursor-Based Traversal**: Efficient traversal of the tree using cursors
- **Slicing Support**: Create views of subranges of the tree
- **Memory Efficient**: Uses Arc for shared ownership and efficient cloning
- **Generic Design**: Works with any type that implements the Leaf trait
- **Range Operations**: Efficient operations on ranges of data

## Getting Started

To use the `btree` crate, you'll need to implement the `Leaf` trait for your data type. Here's a basic example:

```rust
use btree::{BTree, Leaf, Info};

// Define an info type that tracks the length of data
#[derive(Copy, Clone, Debug)]
struct LengthInfo(usize);

impl Info for LengthInfo {
    fn empty() -> Self {
        LengthInfo(0)
    }
    
    fn combine(self, other: Self) -> Self {
        LengthInfo(self.0 + other.0)
    }
    
    fn len(&self) -> usize {
        self.0
    }
}

// Define a simple leaf implementation
#[derive(Clone, Debug)]
struct SimpleLeaf {
    data: Vec<u8>,
}

impl Leaf for SimpleLeaf {
    const MAX_LEN: usize = 32;
    
    type Info = LengthInfo;
    
    fn new() -> Self {
        SimpleLeaf { data: Vec::new() }
    }
    
    fn is_at_least_half_full(&self) -> bool {
        self.data.len() >= Self::MAX_LEN / 2
    }
    
    fn can_split_at(&self, index: usize) -> bool {
        index > 0 && index < self.data.len()
    }
    
    fn len(&self) -> usize {
        self.data.len()
    }
    
    fn info_to(&self, end: usize) -> Self::Info {
        LengthInfo(end)
    }
    
    fn move_left(&mut self, other: &mut Self, end: usize) {
        let to_move = other.data.drain(..end).collect::<Vec<_>>();
        self.data.extend(to_move);
    }
    
    fn move_right(&mut self, other: &mut Self, start: usize) {
        let to_move = self.data.drain(start..).collect::<Vec<_>>();
        other.data.splice(..0, to_move);
    }
    
    fn remove_from(&mut self, start: usize) {
        self.data.truncate(start);
    }
    
    fn remove_to(&mut self, end: usize) {
        self.data.drain(..end);
    }
    
    fn split_off(&mut self, index: usize) -> Self {
        let data = self.data.split_off(index);
        SimpleLeaf { data }
    }
}

// Now you can use the BTree with your leaf type
fn main() {
    let mut tree: BTree<SimpleLeaf> = BTree::new();
    // Use the tree...
}
```

## Core Concepts

### B-tree Structure

A B-tree is a self-balancing tree data structure that maintains sorted data and allows searches, sequential access, insertions, and deletions in logarithmic time. The tree is kept balanced by requiring that all leaf nodes have the same depth.

### Leaf Trait

The `Leaf` trait defines the interface that leaf nodes must implement. This includes methods for managing the data within a leaf, splitting and merging leaves, and providing information about the leaf's contents.

### Info Trait

The `Info` trait is used to track metadata about the data in the tree. This might include the length of the data, checksums, or other aggregate information.

### Cursor

A `Cursor` provides efficient traversal of the tree. It allows you to move through the tree in both directions and access the current position.

### Slice

A `Slice` represents a view of a subrange of the tree. It allows you to work with a portion of the tree as if it were a separate tree.

## Basic Operations

### Creating a BTree

```rust
use btree::BTree;

let tree: BTree<MyLeaf> = BTree::new();
```

### Checking Properties

```rust
let is_empty = tree.is_empty();
let length = tree.len();
let info = tree.info();
```

### Traversal with Cursors

```rust
// Create a cursor at the start of the tree
let mut cursor = tree.cursor_start();

// Move to the next chunk
while cursor.move_next_chunk() {
    // Process the current chunk
    let (leaf, start, end) = cursor.current();
    // ...
}

// Create a cursor at a specific index
let cursor = tree.cursor(10);
```

### Slicing

```rust
// Create a slice of the tree
let slice = tree.slice(5, 15);

// Work with the slice
let slice_len = slice.len();
let slice_cursor = slice.cursor_start();
```

## Advanced Features

### Concatenation

```rust
let mut tree1: BTree<MyLeaf> = BTree::new();
let tree2: BTree<MyLeaf> = BTree::new();

// Append tree2 to tree1
tree1.append(tree2);
```

### Splitting

```rust
let mut tree: BTree<MyLeaf> = BTree::new();

// Split the tree at index 10
let other_tree = tree.split_off(10);
```

### Range Operations

```rust
let mut tree: BTree<MyLeaf> = BTree::new();

// Remove a range from the tree
tree.remove_from(5);  // Remove everything from index 5 onwards
tree.remove_to(10);   // Remove everything up to index 10

// Replace a range in the tree
let replacement: BTree<MyLeaf> = BTree::new();
tree.replace_range(5, 10, replacement);  // Replace indices 5-10
```

## Performance Considerations

### Time Complexity

- Search: O(log n)
- Insertion: O(log n)
- Deletion: O(log n)
- Traversal: O(n)
- Concatenation: O(log n)
- Splitting: O(log n)

### Space Complexity

The space complexity is O(n) where n is the number of elements in the tree. The tree structure itself adds some overhead for maintaining the branching structure.

## Best Practices

1. **Implement Leaf Efficiently**: The performance of the B-tree depends heavily on the efficiency of your Leaf implementation.
2. **Choose Appropriate MAX_LEN**: The MAX_LEN constant in your Leaf implementation affects the branching factor of the tree.
3. **Use Cursors for Traversal**: Cursors provide efficient traversal without needing to repeatedly search the tree.
4. **Use Slices for Subranges**: Slices provide efficient views of subranges without copying data.
5. **Batch Operations**: When possible, batch operations together to minimize tree restructuring.

## Examples

### Simple Text Rope

```rust
use btree::{BTree, Leaf, Info};

#[derive(Copy, Clone, Debug)]
struct TextInfo {
    len: usize,
    lines: usize,
}

impl Info for TextInfo {
    fn empty() -> Self {
        TextInfo { len: 0, lines: 0 }
    }
    
    fn combine(self, other: Self) -> Self {
        TextInfo {
            len: self.len + other.len,
            lines: self.lines + other.lines,
        }
    }
    
    fn len(&self) -> usize {
        self.len
    }
}

#[derive(Clone, Debug)]
struct TextLeaf {
    text: String,
}

impl Leaf for TextLeaf {
    const MAX_LEN: usize = 64;
    
    type Info = TextInfo;
    
    fn new() -> Self {
        TextLeaf { text: String::new() }
    }
    
    fn is_at_least_half_full(&self) -> bool {
        self.text.len() >= Self::MAX_LEN / 2
    }
    
    fn can_split_at(&self, index: usize) -> bool {
        index > 0 && index < self.text.len()
    }
    
    fn len(&self) -> usize {
        self.text.len()
    }
    
    fn info_to(&self, end: usize) -> Self::Info {
        let text_slice = &self.text[..end];
        let lines = text_slice.chars().filter(|&c| c == '\n').count();
        TextInfo { len: end, lines }
    }
    
    fn move_left(&mut self, other: &mut Self, end: usize) {
        let to_move = other.text.drain(..end).collect::<String>();
        self.text.push_str(&to_move);
    }
    
    fn move_right(&mut self, other: &mut Self, start: usize) {
        let to_move = self.text.drain(start..).collect::<String>();
        other.text.insert_str(0, &to_move);
    }
    
    fn remove_from(&mut self, start: usize) {
        self.text.truncate(start);
    }
    
    fn remove_to(&mut self, end: usize) {
        self.text.drain(..end);
    }
    
    fn split_off(&mut self, index: usize) -> Self {
        let text = self.text.split_off(index);
        TextLeaf { text }
    }
}

fn main() {
    let mut rope: BTree<TextLeaf> = BTree::new();
    // Use the rope for efficient text manipulation...
}
```

### Working with Cursors

```rust
use btree::{BTree, Leaf, Info};

fn traverse_tree<L: Leaf>(tree: &BTree<L>) {
    let mut cursor = tree.cursor_start();
    
    loop {
        let (leaf, start, end) = cursor.current();
        if let Some(leaf) = leaf {
            // Process the current leaf segment
            println!("Processing segment: {:?}", &leaf[start..end]);
        }
        
        // Move to the next chunk
        if !cursor.move_next_chunk() {
            break;
        }
    }
}