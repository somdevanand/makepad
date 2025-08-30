# btree_vec API Reference

## Overview

The `btree_vec` crate provides an implementation of a B-tree based vector data structure. It combines the benefits of a B-tree's efficient insertion, deletion, and search operations with the familiar interface of a vector. This API reference documents all public types, traits, and methods available in the crate.

## Structs

### `BTreeVec<T, M>`

A B-tree based vector implementation.

```rust
pub struct BTreeVec<T, M = DefaultMetric>
where
    M: Metric<T>,
{
    // fields omitted
}
```

#### Methods

##### `BTreeVec::new() -> Self`

Creates a new empty BTreeVec.

```rust
pub fn new() -> Self
```

**Returns:**
- A new empty BTreeVec

**Example:**
```rust
use btree_vec::BTreeVec;

let vec: BTreeVec<i32> = BTreeVec::new();
```

##### `BTreeVec::is_empty(&self) -> bool`

Returns true if the BTreeVec is empty.

```rust
pub fn is_empty(&self) -> bool
```

**Returns:**
- `true` if the BTreeVec contains no elements, `false` otherwise

##### `BTreeVec::len(&self) -> usize`

Returns the number of elements in the BTreeVec.

```rust
pub fn len(&self) -> usize
```

**Returns:**
- The number of elements in the BTreeVec

##### `BTreeVec::measure(&self) -> M::Measure`

Returns the aggregate measure for the entire BTreeVec.

```rust
pub fn measure(&self) -> M::Measure
```

**Returns:**
- The aggregate measure for all elements in the BTreeVec

##### `BTreeVec::measure_to(&self, end: usize) -> M::Measure`

Returns the aggregate measure for the BTreeVec up to the specified index.

```rust
pub fn measure_to(&self, end: usize) -> M::Measure
```

**Parameters:**
- `end`: The index up to which to calculate the aggregate measure

**Returns:**
- The aggregate measure for elements up to the specified index

##### `BTreeVec::search_by(&self, f: impl FnMut(M::Measure) -> bool) -> Option<(usize, M::Measure)>`

Searches for a position in the BTreeVec using a predicate function.

```rust
pub fn search_by(&self, f: impl FnMut(M::Measure) -> bool) -> Option<(usize, M::Measure)>
```

**Parameters:**
- `f`: A predicate function that takes cumulative measure and returns a boolean

**Returns:**
- `Some((index, measure))` if a position satisfying the predicate is found
- `None` if no such position exists

##### `BTreeVec::front(&self) -> Option<&T>`

Returns a reference to the first element of the BTreeVec, or None if it is empty.

```rust
pub fn front(&self) -> Option<&T>
```

**Returns:**
- `Some(&T)` if the BTreeVec is not empty
- `None` if the BTreeVec is empty

##### `BTreeVec::back(&self) -> Option<&T>`

Returns a reference to the last element of the BTreeVec, or None if it is empty.

```rust
pub fn back(&self) -> Option<&T>
```

**Returns:**
- `Some(&T)` if the BTreeVec is not empty
- `None` if the BTreeVec is empty

##### `BTreeVec::get(&self, index: usize) -> Option<&T>`

Returns a reference to the element at the specified index, or None if the index is out of bounds.

```rust
pub fn get(&self, index: usize) -> Option<&T>
```

**Parameters:**
- `index`: The index of the element to retrieve

**Returns:**
- `Some(&T)` if the index is valid
- `None` if the index is out of bounds

##### `BTreeVec::cursor_start(&self) -> Cursor<'_, T, M>`

Creates a cursor positioned at the start of the BTreeVec.

```rust
pub fn cursor_start(&self) -> Cursor<'_, T, M>
```

**Returns:**
- A cursor positioned at the start of the BTreeVec

##### `BTreeVec::cursor_end(&self) -> Cursor<'_, T, M>`

Creates a cursor positioned at the end of the BTreeVec.

```rust
pub fn cursor_end(&self) -> Cursor<'_, T, M>
```

**Returns:**
- A cursor positioned at the end of the BTreeVec

##### `BTreeVec::cursor(&self, index: usize) -> Cursor<'_, T, M>`

Creates a cursor positioned at the specified index.

```rust
pub fn cursor(&self, index: usize) -> Cursor<'_, T, M>
```

**Parameters:**
- `index`: The index at which to position the cursor

**Returns:**
- A cursor positioned at the specified index

##### `BTreeVec::chunks(&self) -> Chunks<'_, T, M>`

Returns an iterator over the chunks of the BTreeVec.

```rust
pub fn chunks(&self) -> Chunks<'_, T, M>
```

**Returns:**
- An iterator over the chunks of the BTreeVec

##### `BTreeVec::iter(&self) -> Iter<'_, T, M>`

Returns an iterator over the elements of the BTreeVec.

```rust
pub fn iter(&self) -> Iter<'_, T, M>
```

**Returns:**
- An iterator over the elements of the BTreeVec

##### `BTreeVec::iter_rev(&self) -> IterRev<'_, T, M>`

Returns a reverse iterator over the elements of the BTreeVec.

```rust
pub fn iter_rev(&self) -> IterRev<'_, T, M>
```

**Returns:**
- A reverse iterator over the elements of the BTreeVec

##### `BTreeVec::to_vec(&self) -> Vec<T>`

Converts the BTreeVec to a standard Vec.

```rust
pub fn to_vec(&self) -> Vec<T>
```

**Returns:**
- A Vec containing all elements of the BTreeVec

##### `BTreeVec::push_front(&mut self, item: T)`

Adds an element to the front of the BTreeVec.

```rust
pub fn push_front(&mut self, item: T)
```

**Parameters:**
- `item`: The element to add

##### `BTreeVec::push_back(&mut self, item: T)`

Adds an element to the back of the BTreeVec.

```rust
pub fn push_back(&mut self, item: T)
```

**Parameters:**
- `item`: The element to add

##### `BTreeVec::insert(&mut self, index: usize, item: T)`

Inserts an element at the specified index.

```rust
pub fn insert(&mut self, index: usize, item: T)
```

**Parameters:**
- `index`: The index at which to insert the element
- `item`: The element to insert

##### `BTreeVec::prepend(&mut self, other: Self)`

Prepends another BTreeVec to this one.

```rust
pub fn prepend(&mut self, other: Self)
```

**Parameters:**
- `other`: The BTreeVec to prepend

##### `BTreeVec::append(&mut self, other: Self)`

Appends another BTreeVec to this one.

```rust
pub fn append(&mut self, other: Self)
```

**Parameters:**
- `other`: The BTreeVec to append

##### `BTreeVec::pop_front(&mut self) -> Option<T>`

Removes the first element from the BTreeVec and returns it.

```rust
pub fn pop_front(&mut self) -> Option<T>
```

**Returns:**
- `Some(T)` if the BTreeVec was not empty
- `None` if the BTreeVec was empty

##### `BTreeVec::pop_back(&mut self) -> Option<T>`

Removes the last element from the BTreeVec and returns it.

```rust
pub fn pop_back(&mut self) -> Option<T>
```

**Returns:**
- `Some(T)` if the BTreeVec was not empty
- `None` if the BTreeVec was empty

##### `BTreeVec::remove(&mut self, index: usize) -> T`

Removes and returns the element at the specified index.

```rust
pub fn remove(&mut self, index: usize) -> T
```

**Parameters:**
- `index`: The index of the element to remove

**Returns:**
- The removed element

##### `BTreeVec::remove_from(&mut self, start: usize)`

Removes all elements from the specified index onwards.

```rust
pub fn remove_from(&mut self, start: usize)
```

**Parameters:**
- `start`: The index from which to remove elements

##### `BTreeVec::remove_to(&mut self, end: usize)`

Removes all elements up to the specified index.

```rust
pub fn remove_to(&mut self, end: usize)
```

**Parameters:**
- `end`: The index up to which to remove elements

##### `BTreeVec::replace_range(&mut self, start: usize, end: usize, other: Self)`

Replaces a range of elements with another BTreeVec.

```rust
pub fn replace_range(&mut self, start: usize, end: usize, other: Self)
```

**Parameters:**
- `start`: The start index of the range to replace
- `end`: The end index of the range to replace
- `other`: The BTreeVec to insert in place of the removed range

##### `BTreeVec::split_off(&mut self, index: usize) -> Self`

Splits the BTreeVec into two at the specified index.

```rust
pub fn split_off(&mut self, index: usize) -> Self
```

**Parameters:**
- `index`: The index at which to split

**Returns:**
- A new BTreeVec containing elements from the specified index onwards

#### Trait Implementations

- `Clone`: Clone implementation for BTreeVec where T implements Clone and M implements Metric
- `From<Vec<T>>`: Creates a BTreeVec from a Vec
- `From<&Vec<T>>`: Creates a BTreeVec from a reference to a Vec
- `From<&[T]>`: Creates a BTreeVec from a slice
- `FromIterator<T>`: Creates a BTreeVec from an iterator
- `Index<usize>`: Allows indexing into the BTreeVec

### `Builder<T, M>`

A builder for constructing BTreeVec instances efficiently.

```rust
pub struct Builder<T, M>
where
    T: Clone,
    M: Metric<T>,
{
    // fields omitted
}
```

#### Methods

##### `Builder::new() -> Self`

Creates a new Builder.

```rust
pub fn new() -> Self
```

**Returns:**
- A new Builder instance

##### `Builder::finish(self) -> BTreeVec<T, M>`

Finishes building and returns the resulting BTreeVec.

```rust
pub fn finish(self) -> BTreeVec<T, M>
```

**Returns:**
- The constructed BTreeVec

##### `Builder::push(&mut self, item: T)`

Adds an item to the builder.

```rust
pub fn push(&mut self, item: T)
```

**Parameters:**
- `item`: The item to add

### `Cursor<'a, T, M>`

A cursor for traversing a BTreeVec.

```rust
pub struct Cursor<'a, T, M>
where
    M: Metric<T>,
{
    // fields omitted
}
```

#### Methods

##### `Cursor::into_chunks(self) -> Chunks<'a, T, M>`

Converts the cursor into a chunks iterator.

```rust
pub fn into_chunks(self) -> Chunks<'a, T, M>
```

**Returns:**
- A chunks iterator

##### `Cursor::into_iter(self) -> Iter<'a, T, M>`

Converts the cursor into an iterator.

```rust
pub fn into_iter(self) -> Iter<'a, T, M>
```

**Returns:**
- An iterator

##### `Cursor::into_iter_rev(self) -> IterRev<'a, T, M>`

Converts the cursor into a reverse iterator.

```rust
pub fn into_iter_rev(self) -> IterRev<'a, T, M>
```

**Returns:**
- A reverse iterator

##### `Cursor::is_start(&self) -> bool`

Returns true if the cursor is positioned at the start of the BTreeVec.

```rust
pub fn is_start(&self) -> bool
```

**Returns:**
- `true` if the cursor is at the start, `false` otherwise

##### `Cursor::is_end(&self) -> bool`

Returns true if the cursor is positioned at the end of the BTreeVec.

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

##### `Cursor::current_chunk(&self) -> &'a [T]`

Returns the current chunk of elements.

```rust
pub fn current_chunk(&self) -> &'a [T]
```

**Returns:**
- A slice containing the current chunk of elements

##### `Cursor::current(&self) -> Option<&'a T>`

Returns the current element, or None if the cursor is at the end.

```rust
pub fn current(&self) -> Option<&'a T>
```

**Returns:**
- `Some(&T)` if the cursor is not at the end
- `None` if the cursor is at the end

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

### `Chunks<'a, T, M>`

An iterator over the chunks of a BTreeVec.

```rust
pub struct Chunks<'a, T, M>
where
    M: Metric<T>,
{
    // fields omitted
}
```

### `Iter<'a, T, M>`

An iterator over the elements of a BTreeVec.

```rust
pub struct Iter<'a, T, M>
where
    M: Metric<T>,
{
    // fields omitted
}
```

### `IterRev<'a, T, M>`

A reverse iterator over the elements of a BTreeVec.

```rust
pub struct IterRev<'a, T, M>
where
    M: Metric<T>,
{
    // fields omitted
}
```

## Traits

### `Metric<T>`

The trait for defining metrics that can be tracked by a BTreeVec.

```rust
pub trait Metric<T> {
    type Measure: Measure;
    
    fn measure(item: &T) -> Self::Measure;
}
```

#### Required Methods

##### `Metric::measure(item: &T) -> Self::Measure`

Measures an item according to this metric.

```rust
fn measure(item: &T) -> Self::Measure
```

**Parameters:**
- `item`: The item to measure

**Returns:**
- The measure of the item

### `Measure`

The trait for measures that can be combined.

```rust
pub trait Measure: Copy {
    fn empty() -> Self;
    
    fn combine(self, other: Self) -> Self;
}
```

#### Required Methods

##### `Measure::empty() -> Self`

Returns an empty measure.

```rust
fn empty() -> Self
```

**Returns:**
- An empty measure

##### `Measure::combine(self, other: Self) -> Self`

Combines two measures.

```rust
fn combine(self, other: Self) -> Self
```

**Parameters:**
- `other`: The other measure to combine with

**Returns:**
- The combined measure

### `DefaultMetric`

The default metric that doesn't track any additional information.

```rust
pub struct DefaultMetric;
```

## Examples

### Basic Usage

```rust
use btree_vec::BTreeVec;

fn main() {
    // Create a new BTreeVec
    let mut vec: BTreeVec<i32> = BTreeVec::new();
    
    // Add elements
    vec.push_back(1);
    vec.push_back(2);
    vec.push_back(3);
    
    // Check properties
    assert_eq!(vec.len(), 3);
    assert_eq!(vec[0], 1);
    assert_eq!(vec.front(), Some(&1));
    assert_eq!(vec.back(), Some(&3));
    
    // Iterate
    for item in &vec {
        println!("{}", item);
    }
}
```

### Working with Custom Metrics

```rust
use btree_vec::{BTreeVec, Metric, Measure};

// Define a metric that tracks the sum of values
struct SumMetric;

impl Metric<i32> for SumMetric {
    type Measure = i32;
    
    fn measure(item: &i32) -> Self::Measure {
        *item
    }
}

fn main() {
    let mut vec: BTreeVec<i32, SumMetric> = BTreeVec::new();
    vec.push_back(10);
    vec.push_back(20);
    vec.push_back(30);
    
    // Get the total sum
    assert_eq!(vec.measure(), 60);
}
```

### Working with Cursors

```rust
use btree_vec::BTreeVec;

fn main() {
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
}
```

### Range Operations

```rust
use btree_vec::BTreeVec;

fn main() {
    let mut vec: BTreeVec<i32> = (0..10).collect();
    let other: BTreeVec<i32> = (100..105).collect();
    
    // Replace a range
    vec.replace_range(3, 7, other);
    
    // Split the vector
    let other = vec.split_off(5);
    
    println!("First part: {:?}", vec.to_vec());
    println!("Second part: {:?}", other.to_vec());
}