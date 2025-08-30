# Encoding and Decoding

## Overview
Makepad provides several libraries for encoding and decoding data, including base64 encoding, serialization/deserialization, and hashing. These utilities are essential for data transmission, storage, and integrity verification.

## Base64 Encoding/Decoding

### Overview
The base64 library provides functions for encoding binary data into ASCII strings and decoding base64 strings back to binary data.

### Basic Usage
```rust
use makepad_base64::{base64_encode, base64_decode, BASE64_STANDARD, BASE64_URL_SAFE};

// Encoding binary data
let data = b"Hello, Makepad!";
let encoded = base64_encode(data, &BASE64_STANDARD);
println!("Encoded: {}", String::from_utf8(encoded).unwrap());

// Decoding base64 data
let decoded = base64_decode(&encoded).unwrap();
println!("Decoded: {}", String::from_utf8(decoded).unwrap());
```

### Available Character Sets
- `BASE64_STANDARD`: Standard base64 character set (`+`, `/`)
- `BASE64_URL_SAFE`: URL-safe base64 character set (`-`, `_`)

### Functions
- `base64_encode(data, table)`: Encodes binary data using the specified character table
- `base64_decode(data)`: Decodes base64-encoded data

### Error Handling
The `base64_decode` function returns a `Result` that can contain:
- `Ok(Vec<u8>)`: Successfully decoded data
- `Err(Base64DecodeError)`: Decoding error with variants:
  - `WrongPadding`: Incorrect padding in the input
  - `InvalidCharacter`: Invalid character in the input

### Practical Examples

#### Encoding Image Data
```rust
use makepad_base64::{base64_encode, BASE64_STANDARD};

fn encode_image_data(image_bytes: &[u8]) -> String {
    let encoded = base64_encode(image_bytes, &BASE64_STANDARD);
    String::from_utf8(encoded).unwrap()
}
```

#### Decoding Configuration Data
```rust
use makepad_base64::{base64_decode, Base64DecodeError};

fn decode_config_data(encoded: &str) -> Result<Vec<u8>, Base64DecodeError> {
    base64_decode(encoded.as_bytes())
}
```

## Serialization/Deserialization (micro_serde)

### Overview
The `micro_serde` library provides lightweight serialization and deserialization functionality for Rust data structures. It's designed to be simple and efficient, with support for binary and JSON formats.

### Binary Serialization (SerBin/DeBin)
```rust
use makepad_micro_serde::{SerBin, DeBin};

#[derive(SerBin, DeBin, Debug, PartialEq)]
struct Person {
    name: String,
    age: u32,
}

// Serialization
let person = Person {
    name: "Alice".to_string(),
    age: 30,
};

let serialized = person.serialize_bin();

// Deserialization
let deserialized: Person = Person::deserialize_bin(&serialized).unwrap();
assert_eq!(person, deserialized);
```

### JSON Serialization (SerJson/DeJson)
```rust
use makepad_micro_serde::{SerJson, DeJson};

#[derive(SerJson, DeJson, Debug, PartialEq)]
struct Person {
    name: String,
    age: u32,
}

// Serialization
let person = Person {
    name: "Alice".to_string(),
    age: 30,
};

let json_string = person.serialize_json();

// Deserialization
let deserialized: Person = Person::deserialize_json(&json_string).unwrap();
assert_eq!(person, deserialized);
```

### Supported Data Types
- Basic types: `u8`, `u16`, `u32`, `u64`, `i8`, `i16`, `i32`, `i64`, `f32`, `f64`, `bool`
- Strings: `String`, `&str`
- Collections: `Vec<T>`, arrays `[T; N]`
- Option types: `Option<T>`
- Structs and enums (with derive macros)
- Custom types that implement the serialization traits

### Derive Macros
- `#[derive(SerBin, DeBin)]`: For binary serialization/deserialization
- `#[derive(SerJson, DeJson)]`: For JSON serialization/deserialization

### Error Handling
Deserialization functions return `Result<T, Error>` where `Error` contains information about what went wrong during deserialization.

## Hashing (digest)

### Overview
The `digest` library provides hashing functionality, including SHA-1 implementation.

### SHA-1 Hashing
```rust
use makepad_digest::sha1::{Sha1, Digest};

// Create a SHA-1 hasher instance
let mut hasher = Sha1::new();

// Process input data
hasher.update(b"Hello, Makepad!");

// Get the result
let result = hasher.finalize();
println!("SHA-1 hash: {:x}", result);
```

### Generic Digest Trait
The library implements the generic `Digest` trait, allowing for consistent usage across different hash algorithms.

### Functions
- `update(data)`: Adds data to the hasher
- `finalize()`: Finalizes the hash computation and returns the result
- `finalize_reset()`: Finalizes and resets the hasher for reuse
- `reset()`: Resets the hasher to its initial state

### Practical Examples

#### File Integrity Checking
```rust
use makepad_digest::sha1::{Sha1, Digest};
use std::fs;

fn check_file_integrity(filepath: &str) -> [u8; 20] {
    let mut hasher = Sha1::new();
    let contents = fs::read(filepath).expect("Failed to read file");
    hasher.update(&contents);
    hasher.finalize().into()
}
```

#### Password Hashing (for demonstration only)
```rust
use makepad_digest::sha1::{Sha1, Digest};

fn hash_password(password: &str) -> [u8; 20] {
    let mut hasher = Sha1::new();
    hasher.update(password.as_bytes());
    hasher.finalize().into()
}
```

## Performance Considerations

1. **Base64 Encoding**: Efficient for encoding binary data for text-based transmission
2. **Binary Serialization**: More compact than JSON but less human-readable
3. **JSON Serialization**: Human-readable but more verbose than binary
4. **Hashing**: Computationally intensive but essential for data integrity

## Best Practices

1. **Choose Appropriate Encoding**: Use base64 for binary-to-text encoding, JSON for human-readable data
2. **Handle Errors Gracefully**: Always check return values from decoding/hashing operations
3. **Use Secure Hashing**: For security-sensitive applications, consider using SHA-256 or higher
4. **Validate Input Data**: Always validate data before deserialization to prevent errors

## Related Components
- `makepad_base64`: Base64 encoding/decoding
- `makepad_micro_serde`: Serialization/deserialization
- `makepad_digest`: Hashing algorithms