# Utility Libraries API Reference

## Data Structures

### Structs

#### `BTree<K, V>`
A B-tree implementation for efficient storage and retrieval of ordered data.

```rust
pub struct BTree<K, V> { /* fields omitted */ }
```

##### Methods

###### `BTree::new() -> Self`
Creates a new empty BTree.

```rust
pub fn new() -> Self
```

###### `BTree::insert(&mut self, key: K, value: V)`
Inserts a key-value pair into the BTree.

```rust
pub fn insert(&mut self, key: K, value: V)
```

###### `BTree::get(&self, key: &K) -> Option<&V>`
Retrieves a value by key from the BTree.

```rust
pub fn get(&self, key: &K) -> Option<&V>
```

###### `BTree::remove(&mut self, key: &K) -> Option<V>`
Removes a key-value pair from the BTree.

```rust
pub fn remove(&mut self, key: &K) -> Option<V>
```

###### `BTree::contains_key(&self, key: &K) -> bool`
Checks if a key exists in the BTree.

```rust
pub fn contains_key(&self, key: &K) -> bool
```

###### `BTree::len(&self) -> usize`
Returns the number of elements in the BTree.

```rust
pub fn len(&self) -> usize
```

###### `BTree::is_empty(&self) -> bool`
Checks if the BTree is empty.

```rust
pub fn is_empty(&self) -> bool
```

#### `BTreeVec<T>`
A B-tree based vector implementation.

```rust
pub struct BTreeVec<T> { /* fields omitted */ }
```

##### Methods

###### `BTreeVec::new() -> Self`
Creates a new empty BTreeVec.

```rust
pub fn new() -> Self
```

###### `BTreeVec::push(&mut self, value: T)`
Adds a value to the BTreeVec.

```rust
pub fn push(&mut self, value: T)
```

###### `BTreeVec::insert(&mut self, index: usize, value: T)`
Inserts a value at a specific index.

```rust
pub fn insert(&mut self, index: usize, value: T)
```

###### `BTreeVec::remove(&mut self, index: usize) -> T`
Removes and returns the element at a specific index.

```rust
pub fn remove(&mut self, index: usize) -> T
```

###### `BTreeVec::get(&self, index: usize) -> Option<&T>`
Retrieves a value by index.

```rust
pub fn get(&self, index: usize) -> Option<&T>
```

###### `BTreeVec::len(&self) -> usize`
Returns the number of elements in the BTreeVec.

```rust
pub fn len(&self) -> usize
```

###### `BTreeVec::is_empty(&self) -> bool`
Checks if the BTreeVec is empty.

```rust
pub fn is_empty(&self) -> bool
```

#### `ArrayVec<T, const CAP: usize>`
A vector-like data structure that stores its elements inline on the stack.

```rust
pub struct ArrayVec<T, const CAP: usize> { /* fields omitted */ }
```

##### Methods

###### `ArrayVec::new() -> Self`
Creates a new empty ArrayVec.

```rust
pub fn new() -> Self
```

###### `ArrayVec::push(&mut self, value: T) -> Result<(), PushError>`
Adds a value to the ArrayVec.

```rust
pub fn push(&mut self, value: T) -> Result<(), PushError>
```

###### `ArrayVec::pop(&mut self) -> Option<T>`
Removes and returns the last element.

```rust
pub fn pop(&mut self) -> Option<T>
```

###### `ArrayVec::insert(&mut self, index: usize, value: T) -> Result<(), InsertError>`
Inserts a value at a specific index.

```rust
pub fn insert(&mut self, index: usize, value: T) -> Result<(), InsertError>
```

###### `ArrayVec::remove(&mut self, index: usize) -> T`
Removes and returns the element at a specific index.

```rust
pub fn remove(&mut self, index: usize) -> T
```

###### `ArrayVec::get(&self, index: usize) -> Option<&T>`
Retrieves a value by index.

```rust
pub fn get(&self, index: usize) -> Option<&T>
```

###### `ArrayVec::len(&self) -> usize`
Returns the number of elements in the ArrayVec.

```rust
pub fn len(&self) -> usize
```

###### `ArrayVec::is_empty(&self) -> bool`
Checks if the ArrayVec is empty.

```rust
pub fn is_empty(&self) -> bool
```

###### `ArrayVec::capacity(&self) -> usize`
Returns the maximum capacity of the ArrayVec.

```rust
pub fn capacity(&self) -> usize
```

### Enums

#### `PushError`
Error type for push operations on ArrayVec.

```rust
pub enum PushError {
    Full,
}
```

#### `InsertError`
Error type for insert operations on ArrayVec.

```rust
pub enum InsertError {
    Full,
    IndexOutOfRange,
}
```

## Encoding/Decoding

### Structs

#### `Base64DecodeError`
Error type for base64 decoding operations.

```rust
#[derive(Debug)]
pub enum Base64DecodeError {
    WrongPadding,
    InvalidCharacter,
}
```

### Constants

#### `BASE64_STANDARD`
Standard base64 character set.

```rust
pub const BASE64_STANDARD: [u8; 64]
```

#### `BASE64_URL_SAFE`
URL-safe base64 character set.

```rust
pub const BASE64_URL_SAFE: [u8; 64]
```

### Functions

#### `base64_encode(inp: &[u8], table: &[u8; 64]) -> Vec<u8>`
Encodes binary data using the specified character table.

```rust
pub fn base64_encode(inp: &[u8], table: &[u8; 64]) -> Vec<u8>
```

#### `base64_decode(input: &[u8]) -> Result<Vec<u8>, Base64DecodeError>`
Decodes base64-encoded data.

```rust
pub fn base64_decode(input: &[u8]) -> Result<Vec<u8>, Base64DecodeError>
```

### Traits

#### `SerBin`
Trait for binary serialization.

```rust
pub trait SerBin {
    fn serialize_bin(&self) -> Vec<u8>;
}
```

#### `DeBin`
Trait for binary deserialization.

```rust
pub trait DeBin: Sized {
    fn deserialize_bin(data: &[u8]) -> Result<Self, Error>;
}
```

#### `SerJson`
Trait for JSON serialization.

```rust
pub trait SerJson {
    fn serialize_json(&self) -> String;
}
```

#### `DeJson`
Trait for JSON deserialization.

```rust
pub trait DeJson: Sized {
    fn deserialize_json(data: &str) -> Result<Self, Error>;
}
```

### Derive Macros

#### `SerBin`
Derive macro for binary serialization.

```rust
#[derive(SerBin)]
```

#### `DeBin`
Derive macro for binary deserialization.

```rust
#[derive(DeBin)]
```

#### `SerJson`
Derive macro for JSON serialization.

```rust
#[derive(SerJson)]
```

#### `DeJson`
Derive macro for JSON deserialization.

```rust
#[derive(DeJson)]
```

## Hashing (Digest)

### Structs

#### `Sha1`
SHA-1 hasher implementation.

```rust
pub struct Sha1 { /* fields omitted */ }
```

##### Methods

###### `Sha1::new() -> Self`
Creates a new SHA-1 hasher instance.

```rust
pub fn new() -> Self
```

###### `Sha1::update(&mut self, data: impl AsRef<[u8]>)`
Adds data to the hasher.

```rust
pub fn update(&mut self, data: impl AsRef<[u8]>)
```

###### `Sha1::finalize(self) -> [u8; 20]`
Finalizes the hash computation and returns the result.

```rust
pub fn finalize(self) -> [u8; 20]
```

###### `Sha1::finalize_reset(&mut self) -> [u8; 20]`
Finalizes and resets the hasher for reuse.

```rust
pub fn finalize_reset(&mut self) -> [u8; 20]
```

###### `Sha1::reset(&mut self)`
Resets the hasher to its initial state.

```rust
pub fn reset(&mut self)
```

### Traits

#### `Digest`
Generic digest trait for hash algorithms.

```rust
pub trait Digest {
    fn update(&mut self, data: impl AsRef<[u8]>);
    fn finalize(self) -> Vec<u8>;
    fn finalize_reset(&mut self) -> Vec<u8>;
    fn reset(&mut self);
}
```

## Concurrency

### Structs

#### `Future`
Trait for asynchronous computations.

```rust
pub trait Future {
    type Output;
    fn poll(self: Pin<&mut Self>, cx: &mut Context<'_>) -> Poll<Self::Output>;
}
```

#### `Poll<T>`
Result of polling a future.

```rust
pub enum Poll<T> {
    Ready(T),
    Pending,
}
```

#### `Context<'a>`
Context for polling futures.

```rust
pub struct Context<'a> {
    waker: &'a Waker,
}
```

#### `Waker`
Handle for waking up a task.

```rust
pub struct Waker { /* fields omitted */ }
```

#### `RawWaker`
Low-level waker representation.

```rust
pub struct RawWaker { /* fields omitted */ }
```

#### `RawWakerVTable`
Virtual function table for RawWaker.

```rust
pub struct RawWakerVTable { /* fields omitted */ }
```

### Modules

#### `channel`
Channel implementations for message passing.

##### Structs

###### `Sender<T>`
Sender half of a channel.

```rust
pub struct Sender<T> { /* fields omitted */ }
```

###### `Receiver<T>`
Receiver half of a channel.

```rust
pub struct Receiver<T> { /* fields omitted */ }
```

##### Functions

###### `channel<T>(capacity: usize) -> (Sender<T>, Receiver<T>)`
Creates a bounded channel.

```rust
pub fn channel<T>(capacity: usize) -> (Sender<T>, Receiver<T>)
```

###### `unbounded<T>() -> (UnboundedSender<T>, UnboundedReceiver<T>)`
Creates an unbounded channel.

```rust
pub fn unbounded<T>() -> (UnboundedSender<T>, UnboundedReceiver<T>)
```

#### `stream`
Stream processing utilities.

##### Traits

###### `Stream`
Trait for asynchronous streams.

```rust
pub trait Stream {
    type Item;
    fn poll_next(self: Pin<&mut Self>, cx: &mut Context<'_>) -> Poll<Option<Self::Item>>;
}
```

##### Functions

###### `StreamExt`
Extension trait for streams with utility methods.

```rust
pub trait StreamExt: Stream {
    fn next(&mut self) -> Next<'_, Self> where Self: Unpin;
    fn map<T, F>(self, f: F) -> Map<Self, F> where F: FnMut(Self::Item) -> T;
    fn filter<P>(self, predicate: P) -> Filter<Self, P> where P: FnMut(&Self::Item) -> bool;
    fn for_each<Fut, F>(self, f: F) -> ForEach<Self, Fut, F> where F: FnMut(Self::Item) -> Fut, Fut: Future<Output = ()>;
}