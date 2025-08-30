# array_vec API Reference

## Overview

The `array_vec` crate provides a vector-like data structure that stores its elements inline on the stack. This API reference documents all public types, functions, and methods available in the crate.

## Structs

### `ArrayVec<T, const N: usize>`

A vector-like data structure that stores its elements inline on the stack.

```rust
pub struct ArrayVec<T, const N: usize> {
    // fields omitted
}
```

#### Methods

##### `ArrayVec::new() -> Self`

Creates a new empty `ArrayVec`.

```rust
pub fn new() -> Self
```

**Returns:**
- A new empty `ArrayVec` with length 0

**Example:**
```rust
use array_vec::ArrayVec;

let mut vec: ArrayVec<i32, 10> = ArrayVec::new();
assert_eq!(vec.len(), 0);
assert_eq!(vec.capacity(), 10);
```

##### `ArrayVec::len(&self) -> usize`

Returns the number of elements in the vector.

```rust
pub fn len(&self) -> usize
```

**Returns:**
- The number of elements currently in the vector

**Example:**
```rust
use array_vec::ArrayVec;

let vec: ArrayVec<i32, 5> = ArrayVec::from([1, 2, 3]);
assert_eq!(vec.len(), 3);
```

##### `ArrayVec::capacity(&self) -> usize`

Returns the maximum capacity of the vector.

```rust
pub fn capacity(&self) -> usize
```

**Returns:**
- The maximum number of elements the vector can hold

**Example:**
```rust
use array_vec::ArrayVec;

let vec: ArrayVec<i32, 5> = ArrayVec::new();
assert_eq!(vec.capacity(), 5);
```

##### `ArrayVec::as_ptr(&self) -> *const T`

Returns a raw pointer to the vector's buffer.

```rust
pub fn as_ptr(&self) -> *const T
```

**Returns:**
- A raw pointer to the first element in the vector

**Safety:**
- The caller must ensure that the vector is not dropped while the pointer is in use
- The pointer is only valid for the number of elements currently in the vector

##### `ArrayVec::as_slice(&self) -> &[T]`

Returns a slice containing all elements of the vector.

```rust
pub fn as_slice(&self) -> &[T]
```

**Returns:**
- A slice containing all elements of the vector

**Example:**
```rust
use array_vec::ArrayVec;

let vec: ArrayVec<i32, 5> = ArrayVec::from([1, 2, 3]);
let slice = vec.as_slice();
assert_eq!(slice, &[1, 2, 3]);
```

##### `ArrayVec::as_mut_ptr(&mut self) -> *mut T`

Returns a raw mutable pointer to the vector's buffer.

```rust
pub fn as_mut_ptr(&mut self) -> *mut T
```

**Returns:**
- A raw mutable pointer to the first element in the vector

**Safety:**
- The caller must ensure that the vector is not dropped while the pointer is in use
- The pointer is only valid for the number of elements currently in the vector

##### `ArrayVec::as_mut_slice(&mut self) -> &mut [T]`

Returns a mutable slice containing all elements of the vector.

```rust
pub fn as_mut_slice(&mut self) -> &mut [T]
```

**Returns:**
- A mutable slice containing all elements of the vector

**Example:**
```rust
use array_vec::ArrayVec;

let mut vec: ArrayVec<i32, 5> = ArrayVec::from([1, 2, 3]);
let slice = vec.as_mut_slice();
slice[0] = 10;
assert_eq!(vec[0], 10);
```

##### `ArrayVec::push(&mut self, value: T)`

Appends an element to the back of the vector.

```rust
pub fn push(&mut self, value: T)
```

**Parameters:**
- `value`: The value to append to the vector

**Panics:**
- If the vector is at capacity

**Example:**
```rust
use array_vec::ArrayVec;

let mut vec: ArrayVec<i32, 3> = ArrayVec::new();
vec.push(1);
vec.push(2);
assert_eq!(vec.len(), 2);
```

##### `ArrayVec::try_push(&mut self, value: T) -> Result<(), CapacityError>`

Attempts to append an element to the back of the vector.

```rust
pub fn try_push(&mut self, value: T) -> Result<(), CapacityError>
```

**Parameters:**
- `value`: The value to append to the vector

**Returns:**
- `Ok(())` if the element was successfully added
- `Err(CapacityError)` if the vector is at capacity

**Example:**
```rust
use array_vec::{ArrayVec, CapacityError};

let mut vec: ArrayVec<i32, 2> = ArrayVec::new();
assert!(vec.try_push(1).is_ok());
assert!(vec.try_push(2).is_ok());
assert!(vec.try_push(3).is_err());
```

##### `ArrayVec::insert(&mut self, index: usize, value: T)`

Inserts an element at position `index` within the vector.

```rust
pub fn insert(&mut self, index: usize, value: T)
```

**Parameters:**
- `index`: The index at which to insert the element
- `value`: The value to insert

**Panics:**
- If `index` is greater than the length of the vector
- If the vector is at capacity

**Example:**
```rust
use array_vec::ArrayVec;

let mut vec: ArrayVec<i32, 5> = ArrayVec::from([1, 3]);
vec.insert(1, 2);
assert_eq!(vec.as_slice(), &[1, 2, 3]);
```

##### `ArrayVec::try_insert(&mut self, index: usize, value: T) -> Result<(), CapacityError>`

Attempts to insert an element at position `index` within the vector.

```rust
pub fn try_insert(&mut self, index: usize, value: T) -> Result<(), CapacityError>
```

**Parameters:**
- `index`: The index at which to insert the element
- `value`: The value to insert

**Returns:**
- `Ok(())` if the element was successfully inserted
- `Err(CapacityError)` if the vector is at capacity

**Panics:**
- If `index` is greater than the length of the vector

##### `ArrayVec::extend_from_slice(&mut self, slice: &[T])`

Extends the vector with the contents of a slice.

```rust
pub fn extend_from_slice(&mut self, slice: &[T])
```

**Parameters:**
- `slice`: The slice whose elements to append to the vector

**Panics:**
- If the slice contains more elements than the remaining capacity

##### `ArrayVec::try_extend_from_slice(&mut self, slice: &[T]) -> Result<(), CapacityError>`

Attempts to extend the vector with the contents of a slice.

```rust
pub fn try_extend_from_slice(&mut self, slice: &[T]) -> Result<(), CapacityError>
```

**Parameters:**
- `slice`: The slice whose elements to append to the vector

**Returns:**
- `Ok(())` if the elements were successfully added
- `Err(CapacityError)` if there isn't enough capacity

##### `ArrayVec::pop(&mut self) -> Option<T>`

Removes the last element from the vector and returns it.

```rust
pub fn pop(&mut self) -> Option<T>
```

**Returns:**
- `Some(element)` if the vector was not empty
- `None` if the vector was empty

**Example:**
```rust
use array_vec::ArrayVec;

let mut vec: ArrayVec<i32, 3> = ArrayVec::from([1, 2, 3]);
assert_eq!(vec.pop(), Some(3));
assert_eq!(vec.pop(), Some(2));
assert_eq!(vec.pop(), Some(1));
assert_eq!(vec.pop(), None);
```

##### `ArrayVec::remove(&mut self, index: usize) -> T`

Removes and returns the element at position `index` within the vector.

```rust
pub fn remove(&mut self, index: usize) -> T
```

**Parameters:**
- `index`: The index of the element to remove

**Returns:**
- The removed element

**Panics:**
- If `index` is out of bounds

**Example:**
```rust
use array_vec::ArrayVec;

let mut vec: ArrayVec<i32, 5> = ArrayVec::from([1, 2, 3, 4, 5]);
assert_eq!(vec.remove(2), 3);
assert_eq!(vec.as_slice(), &[1, 2, 4, 5]);
```

##### `ArrayVec::splice<I>(&mut self, range: impl RangeBounds<usize>, replace_with: I) -> Splice<'_, T, N, <I as IntoIterator>::IntoIter>`

Replaces the elements in the specified range with elements from an iterator.

```rust
pub fn splice<I>(
    &mut self,
    range: impl RangeBounds<usize>,
    replace_with: I,
) -> Splice<'_, T, N, <I as IntoIterator>::IntoIter>
where
    I: IntoIterator<Item = T>,
```

**Parameters:**
- `range`: The range of elements to replace
- `replace_with`: The iterator providing replacement elements

**Returns:**
- A `Splice` iterator that yields the removed elements

##### `ArrayVec::drain(&mut self, range: impl RangeBounds<usize>) -> Drain<'_, T, N>`

Creates a draining iterator that removes the specified range of elements.

```rust
pub fn drain(&mut self, range: impl RangeBounds<usize>) -> Drain<'_, T, N>
```

**Parameters:**
- `range`: The range of elements to remove

**Returns:**
- A `Drain` iterator that yields the removed elements

##### `ArrayVec::truncate(&mut self, new_len: usize)`

Shortens the vector, keeping the first `new_len` elements.

```rust
pub fn truncate(&mut self, new_len: usize)
```

**Parameters:**
- `new_len`: The new length of the vector

**Note:**
- If `new_len` is greater than or equal to the current length, this has no effect

##### `ArrayVec::clear(&mut self)`

Clears the vector, removing all elements.

```rust
pub fn clear(&mut self)
```

**Note:**
- This does not change the capacity of the vector

##### `ArrayVec::split_off(&mut self, index: usize) -> Self`

Splits the vector into two at the given index.

```rust
pub fn split_off(&mut self, index: usize) -> Self
```

**Parameters:**
- `index`: The index at which to split

**Returns:**
- A new `ArrayVec` containing elements from `index` to the end

**Panics:**
- If `index` is greater than the length of the vector

#### Trait Implementations

- `Clone`: Clone implementation for `ArrayVec` where `T` implements `Clone`
- `Debug`: Debug implementation for `ArrayVec` where `T` implements `Debug`
- `Default`: Default implementation that creates an empty `ArrayVec`
- `Deref<Target = [T]>`: Allows using slice methods directly on `ArrayVec`
- `DerefMut`: Allows using mutable slice methods directly on `ArrayVec`
- `Extend<T>`: Allows extending the vector with elements from an iterator
- `From<[T; N]>`: Creates an `ArrayVec` from an array
- `FromIterator<T>`: Creates an `ArrayVec` from an iterator
- `Eq`: Equality implementation for `ArrayVec` where `T` implements `Eq`
- `Hash`: Hash implementation for `ArrayVec` where `T` implements `Hash`
- `IntoIterator`: Allows iterating over references to elements
- `IntoIterator for &mut ArrayVec<T, N>`: Allows iterating over mutable references to elements
- `PartialEq`: Partial equality implementation for `ArrayVec` where `T` implements `PartialEq`
- `Ord`: Ordering implementation for `ArrayVec` where `T` implements `Ord`
- `PartialOrd`: Partial ordering implementation for `ArrayVec` where `T` implements `PartialOrd`
- `TryFrom<&[T]>`: Attempts to create an `ArrayVec` from a slice

### `CapacityError`

Error type indicating that an operation failed due to insufficient capacity.

```rust
#[derive(Clone, Copy, Debug, Eq, PartialEq)]
pub struct CapacityError;
```

#### Trait Implementations

- `Clone`: Creates a copy of the value
- `Copy`: Copies the value instead of moving it
- `Debug`: Formats the value for debugging
- `Eq`: Equality relation
- `PartialEq`: Partial equality relation

### `Splice<'a, T, const N: usize, I>`

A splicing iterator that removes elements and yields them while replacing them with new elements.

```rust
pub struct Splice<'a, T, const N: usize, I>
where
    I: Iterator<Item = T>,
{
    // fields omitted
}
```

#### Trait Implementations

- `Debug`: Formats the value for debugging
- `Drop`: Custom cleanup logic
- `Iterator<Item = T>`: Yields removed elements
- `DoubleEndedIterator<Item = T>`: Yields removed elements from both ends

### `Drain<'a, T, const N: usize>`

A draining iterator that removes elements and yields them.

```rust
pub struct Drain<'a, T, const N: usize> {
    // fields omitted
}
```

#### Methods

##### `Drain::next(&mut self) -> Option<T>`

Advances the iterator and returns the next value.

```rust
fn next(&mut self) -> Option<Self::Item>
```

##### `Drain::next_back(&mut self) -> Option<T>`

Removes and returns an element from the back of the iterator.

```rust
fn next_back(&mut self) -> Option<T>
```

#### Trait Implementations

- `Debug`: Formats the value for debugging
- `Drop`: Custom cleanup logic
- `Iterator<Item = T>`: Yields removed elements
- `DoubleEndedIterator<Item = T>`: Yields removed elements from both ends

## Unsafe Methods

### `ArrayVec::set_len(&mut self, len: usize)`

Sets the length of the vector directly.

```rust
pub unsafe fn set_len(&mut self, len: usize)
```

**Parameters:**
- `len`: The new length of the vector

**Safety:**
- The caller must ensure that `len` is less than or equal to the capacity
- The caller must ensure that all elements at indices `len` and beyond are properly initialized if `len` is decreased, or properly uninitialized if `len` is increased

### `ArrayVec::pop_unchecked(&mut self) -> T`

Removes and returns the last element from the vector without checking if it's empty.

```rust
pub unsafe fn pop_unchecked(&mut self) -> T
```

**Returns:**
- The last element in the vector

**Safety:**
- The caller must ensure that the vector is not empty

### `ArrayVec::remove_unchecked(&mut self, index: usize) -> T`

Removes and returns the element at position `index` without bounds checking.

```rust
pub unsafe fn remove_unchecked(&mut self, index: usize) -> T
```

**Parameters:**
- `index`: The index of the element to remove

**Returns:**
- The removed element

**Safety:**
- The caller must ensure that `index` is less than the length of the vector

## Examples

### Basic Usage

```rust
use array_vec::ArrayVec;

let mut vec: ArrayVec<i32, 5> = ArrayVec::new();
vec.push(1);
vec.push(2);
vec.push(3);

assert_eq!(vec.len(), 3);
assert_eq!(vec.capacity(), 5);
assert_eq!(vec[0], 1);

vec.pop();
assert_eq!(vec.len(), 2);
```

### Error Handling

```rust
use array_vec::{ArrayVec, CapacityError};

let mut vec: ArrayVec<i32, 2> = ArrayVec::new();
assert!(vec.try_push(1).is_ok());
assert!(vec.try_push(2).is_ok());
match vec.try_push(3) {
    Ok(()) => println!("Successfully added element"),
    Err(CapacityError) => println!("Vector is at capacity"),
}
```

### Iterator Usage

```rust
use array_vec::ArrayVec;

let vec: ArrayVec<i32, 5> = ArrayVec::from([1, 2, 3, 4, 5]);

// Iterate over elements
for element in &vec {
    println!("{}", element);
}

// Collect from iterator
let vec2: ArrayVec<i32, 3> = (0..3).collect();
assert_eq!(vec2.as_slice(), &[0, 1, 2]);
```

### Splicing

```rust
use array_vec::ArrayVec;

let mut vec: ArrayVec<i32, 10> = ArrayVec::from([1, 2, 3, 4, 5]);
let new_elements = vec![10, 20, 30];

// Replace elements at indices 1..3
let removed: Vec<i32> = vec.splice(1..3, new_elements).collect();
assert_eq!(removed, vec![2, 3]);
assert_eq!(vec.as_slice(), &[1, 10, 20, 30, 4, 5]);