# btree API Reference

## Overview

The `btree` crate provides an implementation of a B-tree data structure, which is a self-balancing tree data structure that maintains sorted data and allows for efficient insertion, deletion, and search operations. This API reference documents all public types, traits, and methods available in the crate.

## Structs

### `BTree<L>`

A B-tree data structure implementation.

```rust
pub struct BTree<L>
where
    L: Leaf,
{
    // fields omitted
}
```

#### Methods

##### `BTree::new() -> Self`

Creates a new empty BTree.

```rust
pub fn new() -> Self
```

**Returns:**
- A new empty BTree

**Example:**
```rust
use btree::BTree;

let tree: BTree<MyLeaf> = BTree::new();
```

##### `BTree::is_empty(&self) -> bool`

Returns true if the BTree is empty.

```rust
pub fn is_empty(&self) -> bool
```

**Returns:**
- `true` if the BTree contains no elements, `false` otherwise

##### `BTree::len(&self) -> usize`

Returns the number of elements in the BTree.

```rust
pub fn len(&self) -> usize
```

**Returns:**
- The number of elements in the BTree

##### `BTree::info(&self) -> L::Info`

Returns the aggregate info for the entire BTree.

```rust
pub fn info(&self) -> L::Info
```

**Returns:**
- The aggregate info for all elements in the BTree

##### `BTree::info_to(&self, end: usize) -> L::Info`

Returns the aggregate info for the BTree up to the specified index.

```rust
pub fn info_to(&self, end: usize) -> L::Info
```

**Parameters:**
- `end`: The index up to which to calculate the aggregate info

**Returns:**
- The aggregate info for elements up to the specified index

##### `BTree::find_leaf_by(&self, f: impl FnMut(L::Info) -> bool) -> (&L, L::Info)`

Finds a leaf in the BTree using a predicate function.

```rust
pub fn find_leaf_by(&self, f: impl FnMut(L::Info) -> bool) -> (&L, L::Info)
```

**Parameters:**
- `f`: A predicate function that takes cumulative info and returns a boolean

**Returns:**
- A reference to the found leaf and its cumulative info

##### `BTree::find_leaf_by_index(&self, index: usize) -> (&L, L::Info)`

Finds the leaf containing the element at the specified index.

```rust
pub fn find_leaf_by_index(&self, index: usize) -> (&L, L::Info)
```

**Parameters:**
- `index`: The index of the element to find

**Returns:**
- A reference to the leaf containing the element and its cumulative info

##### `BTree::cursor_start(&self) -> Cursor<'_, L>`

Creates a cursor positioned at the start of the BTree.

```rust
pub fn cursor_start(&self) -> Cursor<'_, L>
```

**Returns:**
- A cursor positioned at the start of the BTree

##### `BTree::cursor_end(&self) -> Cursor<'_, L>`

Creates a cursor positioned at the end of the BTree.

```rust
pub fn cursor_end(&self) -> Cursor<'_, L>
```

**Returns:**
- A cursor positioned at the end of the BTree

##### `BTree::cursor(&self, index: usize) -> Cursor<'_, L>`

Creates a cursor positioned at the specified index.

```rust
pub fn cursor(&self, index: usize) -> Cursor<'_, L>
```

**Parameters:**
- `index`: The index at which to position the cursor

**Returns:**
- A cursor positioned at the specified index

**Panics:**
- If `index` is greater than the length of the BTree

##### `BTree::slice(&self, start: usize, end: usize) -> Slice<L>`

Creates a slice representing a subrange of the BTree.

```rust
pub fn slice(&self, start: usize, end: usize) -> Slice<L>
```

**Parameters:**
- `start`: The start index of the slice
- `end`: The end index of the slice

**Returns:**
- A slice representing the specified subrange

**Panics:**
- If `start` > `end` or `end` > the length of the BTree

##### `BTree::prepend(&mut self, other: Self)`

Prepends another BTree to this one.

```rust
pub fn prepend(&mut self, other: Self)
```

**Parameters:**
- `other`: The BTree to prepend

##### `BTree::append(&mut self, other: Self)`

Appends another BTree to this one.

```rust
pub fn append(&mut self, other: Self)
```

**Parameters:**
- `other`: The BTree to append

##### `BTree::remove_from(&mut self, start: usize)`

Removes all elements from the specified index onwards.

```rust
pub fn remove_from(&mut self, start: usize)
```

**Parameters:**
- `start`: The index from which to remove elements

**Panics:**
- If `start` is greater than the length of the BTree

##### `BTree::remove_to(&mut self, end: usize)`

Removes all elements up to the specified index.

```rust
pub fn remove_to(&mut self, end: usize)
```

**Parameters:**
- `end`: The index up to which to remove elements

**Panics:**
- If `end` is greater than the length of the BTree

##### `BTree::replace_range(&mut self, start: usize, end: usize, btree: Self)`

Replaces a range of elements with another BTree.

```rust
pub fn replace_range(&mut self, start: usize, end: usize, btree: Self)
```

**Parameters:**
- `start`: The start index of the range to replace
- `end`: The end index of the range to replace
- `btree`: The BTree to insert in place of the removed range

**Panics:**
- If `start` > `end` or `end` > the length of the BTree

##### `BTree::split_off(&mut self, index: usize) -> Self`

Splits the BTree into two at the specified index.

```rust
pub fn split_off(&mut self, index: usize) -> Self
```

**Parameters:**
- `index`: The index at which to split

**Returns:**
- A new BTree containing elements from the specified index onwards

**Panics:**
- If `index` is greater than the length of the BTree

#### Trait Implementations

- `Clone`: Clone implementation for BTree where L implements Clone
- `Debug`: Debug implementation for BTree where L and L::Info implement Debug

### `Cursor<'a, L>`

A cursor for traversing a BTree.

```rust
pub struct Cursor<'a, L>
where
    L: Leaf,
{
    // fields omitted
}
```

#### Methods

##### `Cursor::is_start(&self) -> bool`

Returns true if the cursor is positioned at the start of the BTree.

```rust
pub fn is_start(&self) -> bool
```

**Returns:**
- `true` if the cursor is at the start, `false` otherwise

##### `Cursor::is_end(&self) -> bool`

Returns true if the cursor is positioned at the end of the BTree.

```rust
pub fn is_end(&self) -> bool
```

**Returns:**
- `true` if the cursor is at the end, `false` otherwise

##### `Cursor::index(&self) -> usize`

Returns the current index of the cursor.

```rust
pub fn index(&self) -> usize
```

**Returns:**
- The current index of the cursor

##### `Cursor::offset(&self) -> usize`

Returns the offset within the current leaf.

```rust
pub fn offset(&self) -> usize
```

**Returns:**
- The offset within the current leaf

##### `Cursor::current(&self) -> (Option<&'a L>, usize, usize)`

Returns the current leaf and its start and end indices.

```rust
pub fn current(&self) -> (Option<&'a L>, usize, usize)
```

**Returns:**
- A tuple containing:
  - An optional reference to the current leaf
  - The start index within the leaf
  - The end index within the leaf

##### `Cursor::move_next_chunk(&mut self) -> bool`

Moves the cursor to the next chunk.

```rust
pub fn move_next_chunk(&mut self) -> bool
```

**Returns:**
- `true` if the cursor was moved, `false` if it was already at the end

##### `Cursor::move_next(&mut self) -> bool`

Moves the cursor to the next element.

```rust
pub fn move_next(&mut self) -> bool
```

**Returns:**
- `true` if the cursor was moved, `false` if it was already at the end

##### `Cursor::move_prev_chunk(&mut self) -> bool`

Moves the cursor to the previous chunk.

```rust
pub fn move_prev_chunk(&mut self) -> bool
```

**Returns:**
- `true` if the cursor was moved, `false` if it was already at the start

##### `Cursor::move_prev(&mut self) -> bool`

Moves the cursor to the previous element.

```rust
pub fn move_prev(&mut self) -> bool
```

**Returns:**
- `true` if the cursor was moved, `false` if it was already at the start

#### Trait Implementations

- `Clone`: Clone implementation for Cursor
- `Debug`: Debug implementation for Cursor where L and L::Info implement Debug

### `Slice<'a, L>`

A slice representing a subrange of a BTree.

```rust
pub struct Slice<'a, L>
where
    L: Leaf,
{
    // fields omitted
}
```

#### Methods

##### `Slice::is_empty(&self) -> bool`

Returns true if the slice is empty.

```rust
pub fn is_empty(&self) -> bool
```

**Returns:**
- `true` if the slice contains no elements, `false` otherwise

##### `Slice::len(&self) -> usize`

Returns the number of elements in the slice.

```rust
pub fn len(&self) -> usize
```

**Returns:**
- The number of elements in the slice

##### `Slice::start_info(&self) -> L::Info`

Returns the aggregate info at the start of the slice.

```rust
pub fn start_info(&self) -> L::Info
```

**Returns:**
- The aggregate info at the start of the slice

##### `Slice::end_info(&self) -> L::Info`

Returns the aggregate info at the end of the slice.

```rust
pub fn end_info(&self) -> L::Info
```

**Returns:**
- The aggregate info at the end of the slice

##### `Slice::find_by(&self, f: impl FnMut(L::Info) -> bool) -> (&L, L::Info)`

Finds a leaf in the slice using a predicate function.

```rust
pub fn find_by(&self, f: impl FnMut(L::Info) -> bool) -> (&L, L::Info)
```

**Parameters:**
- `f`: A predicate function that takes cumulative info and returns a boolean

**Returns:**
- A reference to the found leaf and its cumulative info

##### `Slice::cursor_start(&self) -> Cursor<'a, L>`

Creates a cursor positioned at the start of the slice.

```rust
pub fn cursor_start(&self) -> Cursor<'a, L>
```

**Returns:**
- A cursor positioned at the start of the slice

##### `Slice::cursor_end(&self) -> Cursor<'a, L>`

Creates a cursor positioned at the end of the slice.

```rust
pub fn cursor_end(&self) -> Cursor<'a, L>
```

**Returns:**
- A cursor positioned at the end of the slice

##### `Slice::cursor(&self, index: usize) -> Cursor<'a, L>`

Creates a cursor positioned at the specified index within the slice.

```rust
pub fn cursor(&self, index: usize) -> Cursor<'a, L>
```

**Parameters:**
- `index`: The index at which to position the cursor

**Returns:**
- A cursor positioned at the specified index

##### `Slice::slice(&self, start: usize, end: usize) -> Slice<'a, L>`

Creates a slice representing a subrange of this slice.

```rust
pub fn slice(&self, start: usize, end: usize) -> Slice<'a, L>
```

**Parameters:**
- `start`: The start index of the new slice
- `end`: The end index of the new slice

**Returns:**
- A slice representing the specified subrange

#### Trait Implementations

- `Clone`: Clone implementation for Slice
- `Copy`: Copy implementation for Slice
- `Debug`: Debug implementation for Slice where L and L::Info implement Debug

## Traits

### `Leaf`

The trait that leaf nodes must implement.

```rust
pub trait Leaf {
    const MAX_LEN: usize;
    type Info: Info;
    
    fn new() -> Self;
    fn is_at_least_half_full(&self) -> bool;
    fn can_split_at(&self, index: usize) -> bool;
    fn len(&self) -> usize;
    fn info_to(&self, end: usize) -> Self::Info;
    fn move_left(&mut self, other: &mut Self, end: usize);
    fn move_right(&mut self, other: &mut Self, start: usize);
    fn remove_from(&mut self, start: usize);
    fn remove_to(&mut self, end: usize);
    fn split_off(&mut self, index: usize) -> Self;
    
    // Provided methods
    fn is_full(&self) -> bool { ... }
    fn info(&self) -> Self::Info { ... }
    fn prepend(&mut self, other: &mut Self) { ... }
    fn append(&mut self, other: &mut Self) { ... }
    fn distribute(&mut self, other: &mut Self) { ... }
    fn prepend_distribute(&mut self, other: &mut Self) -> bool { ... }
    fn append_distribute(&mut self, other: &mut Self) -> bool { ... }
}
```

#### Required Methods

##### `Leaf::new() -> Self`

Creates a new empty leaf.

```rust
fn new() -> Self
```

**Returns:**
- A new empty leaf

##### `Leaf::is_at_least_half_full(&self) -> bool`

Returns true if the leaf is at least half full.

```rust
fn is_at_least_half_full(&self) -> bool
```

**Returns:**
- `true` if the leaf contains at least half of its maximum capacity

##### `Leaf::can_split_at(&self, index: usize) -> bool`

Returns true if the leaf can be split at the specified index.

```rust
fn can_split_at(&self, index: usize) -> bool
```

**Parameters:**
- `index`: The index at which to check if splitting is possible

**Returns:**
- `true` if the leaf can be split at the specified index

##### `Leaf::len(&self) -> usize`

Returns the number of elements in the leaf.

```rust
fn len(&self) -> usize
```

**Returns:**
- The number of elements in the leaf

##### `Leaf::info_to(&self, end: usize) -> Self::Info`

Returns the aggregate info for the leaf up to the specified index.

```rust
fn info_to(&self, end: usize) -> Self::Info
```

**Parameters:**
- `end`: The index up to which to calculate the aggregate info

**Returns:**
- The aggregate info for elements up to the specified index

##### `Leaf::move_left(&mut self, other: &mut Self, end: usize)`

Moves elements from the start of `other` to the end of `self`.

```rust
fn move_left(&mut self, other: &mut Self, end: usize)
```

**Parameters:**
- `other`: The leaf from which to move elements
- `end`: The index up to which to move elements

##### `Leaf::move_right(&mut self, other: &mut Self, start: usize)`

Moves elements from the end of `self` to the start of `other`.

```rust
fn move_right(&mut self, other: &mut Self, start: usize)
```

**Parameters:**
- `other`: The leaf to which to move elements
- `start`: The index from which to move elements

##### `Leaf::remove_from(&mut self, start: usize)`

Removes all elements from the specified index onwards.

```rust
fn remove_from(&mut self, start: usize)
```

**Parameters:**
- `start`: The index from which to remove elements

##### `Leaf::remove_to(&mut self, end: usize)`

Removes all elements up to the specified index.

```rust
fn remove_to(&mut self, end: usize)
```

**Parameters:**
- `end`: The index up to which to remove elements

##### `Leaf::split_off(&mut self, index: usize) -> Self`

Splits the leaf into two at the specified index.

```rust
fn split_off(&mut self, index: usize) -> Self
```

**Parameters:**
- `index`: The index at which to split

**Returns:**
- A new leaf containing elements from the specified index onwards

#### Provided Methods

##### `Leaf::is_full(&self) -> bool`

Returns true if the leaf is full.

```rust
fn is_full(&self) -> bool
```

**Returns:**
- `true` if the leaf contains its maximum capacity of elements

##### `Leaf::info(&self) -> Self::Info`

Returns the aggregate info for the entire leaf.

```rust
fn info(&self) -> Self::Info
```

**Returns:**
- The aggregate info for all elements in the leaf

##### `Leaf::prepend(&mut self, other: &mut Self)`

Prepends another leaf to this one.

```rust
fn prepend(&mut self, other: &mut Self)
```

**Parameters:**
- `other`: The leaf to prepend

##### `Leaf::append(&mut self, other: &mut Self)`

Appends another leaf to this one.

```rust
fn append(&mut self, other: &mut Self)
```

**Parameters:**
- `other`: The leaf to append

##### `Leaf::distribute(&mut self, other: &mut Self)`

Distributes elements between this leaf and another to balance them.

```rust
fn distribute(&mut self, other: &mut Self)
```

**Parameters:**
- `other`: The leaf with which to distribute elements

##### `Leaf::prepend_distribute(&mut self, other: &mut Self) -> bool`

Prepends another leaf to this one, distributing elements if necessary.

```rust
fn prepend_distribute(&mut self, other: &mut Self) -> bool
```

**Parameters:**
- `other`: The leaf to prepend

**Returns:**
- `true` if the leaves were successfully merged, `false` if they were distributed

##### `Leaf::append_distribute(&mut self, other: &mut Self) -> bool`

Appends another leaf to this one, distributing elements if necessary.

```rust
fn append_distribute(&mut self, other: &mut Self) -> bool
```

**Parameters:**
- `other`: The leaf to append

**Returns:**
- `true` if the leaves were successfully merged, `false` if they were distributed

### `Info`

The trait for aggregate information tracked by the B-tree.

```rust
pub trait Info: Copy {
    fn empty() -> Self;
    fn combine(self, other: Self) -> Self;
    fn len(&self) -> usize;
}
```

#### Required Methods

##### `Info::empty() -> Self`

Returns an empty info value.

```rust
fn empty() -> Self
```

**Returns:**
- An empty info value

##### `Info::combine(self, other: Self) -> Self`

Combines two info values.

```rust
fn combine(self, other: Self) -> Self
```

**Parameters:**
- `other`: The other info value to combine with

**Returns:**
- The combined info value

##### `Info::len(&self) -> usize`

Returns the length represented by this info.

```rust
fn len(&self) -> usize
```

**Returns:**
- The length represented by this info

## Examples

### Basic Usage

```rust
use btree::{BTree, Leaf, Info};

// Define a simple leaf implementation
#[derive(Clone, Debug)]
struct SimpleLeaf {
    data: Vec<u8>,
}

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

fn main() {
    let mut tree: BTree<SimpleLeaf> = BTree::new();
    
    // Check if empty
    assert!(tree.is_empty());
    assert_eq!(tree.len(), 0);
    
    // Add some data (this would require implementing BTree construction)
    // ...
    
    // Check length
    assert_eq!(tree.len(), expected_length);
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
```

### Working with Slices

```rust
use btree::{BTree, Leaf, Info};

fn process_slice<L: Leaf>(tree: &BTree<L>) {
    // Create a slice of the tree
    let slice = tree.slice(5, 15);
    
    // Work with the slice
    println!("Slice length: {}", slice.len());
    
    // Create a cursor for the slice
    let mut cursor = slice.cursor_start();
    
    // Process the slice...
}