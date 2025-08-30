# Data Structures

## Overview
Makepad provides several custom data structures that are optimized for performance and specific use cases within the framework. These include BTree implementations, array vectors, and other specialized collections.

## BTree Implementation

### BTree
The `BTree` implementation in Makepad is a specialized B-tree data structure designed for efficient storage and retrieval of ordered data.

#### Basic Usage
```rust
use makepad_btree::BTree;

let mut tree = BTree::new();
tree.insert(1, "one");
tree.insert(2, "two");
tree.insert(3, "three");

if let Some(value) = tree.get(&2) {
    println!("Found value: {}", value);
}
```

#### Key Features
- Efficient insertion, deletion, and lookup operations (O(log n))
- Maintains sorted order of elements
- Memory efficient storage

#### Methods
- `insert(key, value)`: Inserts a key-value pair
- `get(key)`: Retrieves a value by key
- `remove(key)`: Removes a key-value pair
- `contains_key(key)`: Checks if a key exists
- `len()`: Returns the number of elements
- `is_empty()`: Checks if the tree is empty

### BTreeVec
`BTreeVec` is a B-tree based vector implementation that provides efficient insertion and deletion while maintaining order.

#### Basic Usage
```rust
use makepad_btree_vec::BTreeVec;

let mut vec = BTreeVec::new();
vec.push(1);
vec.push(3);
vec.push(2);

// Elements are automatically sorted
for item in vec.iter() {
    println!("{}", item);
}
```

#### Key Features
- Maintains sorted order of elements
- Efficient insertion and deletion (O(log n))
- Index-based access
- Memory efficient for large datasets

#### Methods
- `push(value)`: Adds a value to the vector
- `insert(index, value)`: Inserts a value at a specific index
- `remove(index)`: Removes a value at a specific index
- `get(index)`: Retrieves a value by index
- `len()`: Returns the number of elements
- `is_empty()`: Checks if the vector is empty

## ArrayVec

### Overview
`ArrayVec` is a vector-like data structure that stores its elements inline on the stack, with a fixed maximum capacity known at compile time.

#### Basic Usage
```rust
use makepad_array_vec::ArrayVec;

// Create an ArrayVec with a capacity of 10 elements
let mut vec: ArrayVec<i32, 10> = ArrayVec::new();
vec.push(1);
vec.push(2);
vec.push(3);

for item in vec.iter() {
    println!("{}", item);
}
```

#### Key Features
- Stack-allocated storage (no heap allocation)
- Fixed maximum capacity
- No reallocation overhead
- Similar API to std::Vec

#### Methods
- `push(value)`: Adds a value to the vector
- `pop()`: Removes and returns the last element
- `insert(index, value)`: Inserts a value at a specific index
- `remove(index)`: Removes and returns the element at a specific index
- `get(index)`: Retrieves a value by index
- `len()`: Returns the number of elements
- `is_empty()`: Checks if the vector is empty
- `capacity()`: Returns the maximum capacity

#### Performance Benefits
- Zero heap allocation
- Better cache locality
- No reallocation overhead
- Predictable memory usage

## Custom Data Structures

### LiveIdMap
A specialized HashMap implementation optimized for Live IDs.

#### Basic Usage
```rust
use makepad_live_id::{LiveId, LiveIdMap};

let mut map: LiveIdMap<LiveId, String> = LiveIdMap::default();
let id = LiveId::from_str("my_key");
map.insert(id, "my_value".to_string());

if let Some(value) = map.get(&id) {
    println!("Found value: {}", value);
}
```

#### Key Features
- Optimized for Live ID keys
- Efficient lookup and insertion
- Memory efficient storage

### Other Specialized Structures
Makepad also includes other specialized data structures for specific use cases:
- Custom allocators for graphics memory
- Ring buffers for streaming data
- Sparse arrays for efficient storage of mostly empty data

## Performance Considerations

1. **Choose the Right Structure**: Select the appropriate data structure based on your access patterns
2. **Stack vs Heap**: Use ArrayVec for small, fixed-size collections to avoid heap allocation
3. **BTree for Ordered Data**: Use BTree or BTreeVec when you need sorted data or range queries
4. **Capacity Planning**: For ArrayVec, choose a capacity that balances memory usage with functionality

## Best Practices

1. **Prefer Stack Allocation**: Use ArrayVec when the maximum size is known and reasonable
2. **Consider Memory Layout**: Be aware of how different structures organize data in memory
3. **Use Appropriate Methods**: Use methods like `reserve()` for Vec-like structures when you know the size in advance
4. **Handle Errors Gracefully**: Check return values for operations that might fail (e.g., pushing to a full ArrayVec)

## Related Components
- `makepad_btree`: B-tree implementation
- `makepad_btree_vec`: B-tree based vector
- `makepad_array_vec`: Stack-allocated vector
- `makepad_live_id`: Live ID system with specialized collections