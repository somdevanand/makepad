# Live ID System

## Overview
The Live ID system is a core component of the Makepad library that provides unique identifiers for live objects. It's designed to efficiently create, manage, and compare identifiers within the Makepad framework.

## Purpose and Use Cases
Live IDs serve several important purposes in the Makepad framework:

1. **Unique Identification**: Each Live ID is guaranteed to be unique within the system
2. **Efficient Comparison**: IDs can be compared quickly using simple integer operations
3. **Memory Efficiency**: IDs are stored as compact 64-bit integers
4. **Debugging Support**: Human-readable strings can be associated with IDs for debugging

## Creating and Managing Live IDs

### Basic ID Creation
Live IDs can be created from strings using the `from_str` method:

```rust
use makepad_live_id::LiveId;

let id = LiveId::from_str("my_component");
```

### ID Properties
Live IDs have several useful properties:

- `is_unique()`: Checks if the ID is a unique identifier
- `is_ident()`: Checks if the ID is a hash-based identifier
- `is_empty()`: Checks if the ID is empty (value of 0)

### ID Operations
You can perform various operations on Live IDs:

```rust
let id1 = LiveId::from_str("component_a");
let id2 = LiveId::from_str("component_b");

// Create a new ID by appending strings
let combined_id = id1.str_append("sub_component");

// Create a new ID by combining with another ID
let merged_id = id1.id_append(id2);
```

## Macros for ID Generation
The Live ID system provides several macros to simplify ID generation:

### `live_id!` Macro
Creates a Live ID from a string literal:

```rust
use makepad_live_id::live_id;

let id = live_id!("my_component");
```

### `id!` Macro
Creates an array of Live IDs from a sequence of identifiers:

```rust
use makepad_live_id::id;

let ids = id!(component_a.component_b.component_c);
```

### `ids!` Macro
Creates a nested array of Live IDs:

```rust
use makepad_live_id::ids;

let id_groups = ids!(group_a.item_1.item_2, group_b.item_3.item_4);
```

## Interning and Lookup Tables

### String Interning
Live IDs support string interning to associate human-readable strings with IDs:

```rust
let id = LiveId::from_str_with_lut("my_component").unwrap();
```

### Interning Control
You can control whether strings are interned using the `InternLiveId` enum:

```rust
use makepad_live_id::{LiveId, InternLiveId};

let id = LiveId::from_str_with_intern("my_component", InternLiveId::Yes);
```

## Practical Examples

### Using Live IDs in Components
```rust
use makepad_live_id::LiveId;

struct Component {
    id: LiveId,
    name: String,
}

impl Component {
    fn new(name: &str) -> Self {
        Self {
            id: LiveId::from_str(name),
            name: name.to_string(),
        }
    }
    
    fn get_id(&self) -> LiveId {
        self.id
    }
}
```

### Comparing Live IDs
```rust
let id1 = LiveId::from_str("component_a");
let id2 = LiveId::from_str("component_b");

if id1 == id2 {
    println!("IDs are equal");
} else {
    println!("IDs are different");
}
```

## Best Practices

1. **Use Macros When Possible**: The `live_id!` macro is more convenient than calling `LiveId::from_str` directly
2. **Consider Interning**: Only intern strings that you need to retrieve as strings later
3. **Leverage ID Operations**: Use `str_append` and `id_append` to create hierarchical IDs
4. **Check ID Properties**: Use the property methods to understand the nature of your IDs

## Performance Considerations

- Live IDs are 64-bit integers, making them very efficient to store and compare
- Hash-based IDs use a high bit marker to distinguish them from unique IDs
- String interning has a small memory overhead but provides debugging benefits
- ID generation is deterministic and fast

## Error Handling
When using `from_str_with_lut`, be aware that it can return a collision error if the same ID string maps to different stored strings:

```rust
match LiveId::from_str_with_lut("my_component") {
    Ok(id) => println!("Created ID: {:?}", id),
    Err(stored_string) => println!("Collision with stored string: {}", stored_string),
}
```

## Related Components
- `LiveIdMap`: A specialized HashMap implementation optimized for Live IDs
- `LiveIdInterner`: The internal system for managing string interning