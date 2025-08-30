# base64 Documentation

## Overview

The `base64` crate provides functionality for encoding and decoding data using the Base64 encoding scheme. Base64 is commonly used to encode binary data as ASCII text, making it suitable for transmission over text-based protocols like email or inclusion in text-based formats like JSON or XML.

## Purpose and Use Cases

Base64 encoding is useful in various scenarios:

- Encoding binary data for transmission over text-based protocols
- Embedding binary data in JSON, XML, or other text-based formats
- Storing binary data in systems that only support text
- Encoding data for use in URLs or filenames
- Working with web APIs that require Base64-encoded data

## Key Features

- **Standard Base64 Encoding**: Supports the standard Base64 character set
- **URL-Safe Base64 Encoding**: Supports URL-safe Base64 character set
- **Efficient Implementation**: Optimized for performance
- **Error Handling**: Provides clear error messages for invalid input
- **No External Dependencies**: Minimal implementation with no external dependencies

## Getting Started

To use the `base64` crate, add it to your `Cargo.toml`:

```toml
[dependencies]
base64 = "0.1"
```

Then, in your Rust code:

```rust
use base64::{base64_encode, base64_decode, BASE64_STANDARD};

// Encode binary data
let data = b"Hello, World!";
let encoded = base64_encode(data, &BASE64_STANDARD);
println!("Encoded: {}", String::from_utf8(encoded).unwrap());

// Decode Base64 data
let decoded = base64_decode(&encoded).unwrap();
println!("Decoded: {}", String::from_utf8(decoded).unwrap());
```

## Core Concepts

### Base64 Encoding

Base64 encoding converts binary data into a text representation using 64 different characters. The standard Base64 alphabet includes:

- Uppercase letters: A-Z (26 characters)
- Lowercase letters: a-z (26 characters)
- Digits: 0-9 (10 characters)
- Special characters: + and / (2 characters)

The URL-safe variant replaces + and / with - and _ respectively.

### Character Sets

The crate provides two predefined character sets:

1. `BASE64_STANDARD`: Uses the standard Base64 alphabet with '+' and '/'
2. `BASE64_URL_SAFE`: Uses the URL-safe Base64 alphabet with '-' and '_'

## Basic Operations

### Encoding Data

```rust
use base64::{base64_encode, BASE64_STANDARD, BASE64_URL_SAFE};

// Encode using standard Base64
let data = b"Hello, World!";
let encoded_standard = base64_encode(data, &BASE64_STANDARD);

// Encode using URL-safe Base64
let encoded_url_safe = base64_encode(data, &BASE64_URL_SAFE);
```

### Decoding Data

```rust
use base64::{base64_decode, base64_encode, BASE64_STANDARD};

// First encode some data
let data = b"Hello, World!";
let encoded = base64_encode(data, &BASE64_STANDARD);

// Then decode it back
let decoded = base64_decode(&encoded).unwrap();
assert_eq!(data, decoded.as_slice());
```

## Error Handling

The `base64_decode` function returns a `Result` that can contain two types of errors:

1. `Base64DecodeError::WrongPadding`: Input length is not a multiple of 4
2. `Base64DecodeError::InvalidCharacter`: Input contains invalid characters

```rust
use base64::{base64_decode, Base64DecodeError};

match base64_decode(b"invalid_base64!") {
    Ok(data) => println!("Decoded: {:?}", data),
    Err(Base64DecodeError::WrongPadding) => println!("Input has incorrect padding"),
    Err(Base64DecodeError::InvalidCharacter) => println!("Input contains invalid characters"),
}
```

## Performance Considerations

The implementation is designed to be efficient:

- Minimal memory allocations
- Optimized for both encoding and decoding
- No external dependencies that could impact performance

## Advanced Features

### Custom Character Sets

You can define your own character sets for encoding:

```rust
use base64::base64_encode;

const CUSTOM_BASE64: [u8; 64] = [
    b'0', b'1', b'2', b'3', b'4', b'5', b'6', b'7', b'8', b'9',
    b'A', b'B', b'C', b'D', b'E', b'F', b'G', b'H', b'I', b'J',
    b'K', b'L', b'M', b'N', b'O', b'P', b'Q', b'R', b'S', b'T',
    b'U', b'V', b'W', b'X', b'Y', b'Z', b'a', b'b', b'c', b'd',
    b'e', b'f', b'g', b'h', b'i', b'j', b'k', b'l', b'm', b'n',
    b'o', b'p', b'q', b'r', b's', b't', b'u', b'v', b'w', b'x',
    b'y', b'z', b'+', b'/'
];

let data = b"Hello, World!";
let encoded = base64_encode(data, &CUSTOM_BASE64);
```

## Integration with Standard Library

The crate works seamlessly with standard Rust types:

```rust
use base64::{base64_encode, base64_decode, BASE64_STANDARD};
use std::string::String;

// Working with strings
let text = "Hello, World!";
let encoded = base64_encode(text.as_bytes(), &BASE64_STANDARD);
let decoded = base64_decode(&encoded).unwrap();
let decoded_text = String::from_utf8(decoded).unwrap();
assert_eq!(text, decoded_text);

// Working with vectors
let data = vec![1, 2, 3, 4, 5];
let encoded = base64_encode(&data, &BASE64_STANDARD);
let decoded = base64_decode(&encoded).unwrap();
assert_eq!(data, decoded);
```

## Best Practices

1. **Choose the Right Character Set**: Use `BASE64_URL_SAFE` when encoding data for URLs
2. **Handle Errors Appropriately**: Always handle potential decoding errors
3. **Validate Input**: Ensure input data is properly formatted before decoding
4. **Consider Alternatives**: For large data, consider streaming or chunked processing
5. **Test Edge Cases**: Test with empty data, single bytes, and boundary conditions

## Examples

### Basic Encoding and Decoding

```rust
use base64::{base64_encode, base64_decode, BASE64_STANDARD};

fn main() {
    // Encode binary data
    let data = b"Hello, Base64!";
    let encoded = base64_encode(data, &BASE64_STANDARD);
    println!("Encoded: {}", String::from_utf8(encoded.clone()).unwrap());
    
    // Decode the data back
    let decoded = base64_decode(&encoded).unwrap();
    println!("Decoded: {}", String::from_utf8(decoded).unwrap());
    
    // Verify the round-trip
    assert_eq!(data, decoded.as_slice());
}
```

### URL-Safe Encoding

```rust
use base64::{base64_encode, BASE64_URL_SAFE};

fn encode_for_url() {
    let url = "https://example.com/path?param=value&other=123";
    let encoded = base64_encode(url.as_bytes(), &BASE64_URL_SAFE);
    println!("URL-safe encoded: {}", String::from_utf8(encoded).unwrap());
}
```

### Error Handling

```rust
use base64::{base64_decode, Base64DecodeError};

fn safe_decode(input: &[u8]) -> Result<Vec<u8>, String> {
    match base64_decode(input) {
        Ok(data) => Ok(data),
        Err(Base64DecodeError::WrongPadding) => Err("Input has incorrect padding".to_string()),
        Err(Base64DecodeError::InvalidCharacter) => Err("Input contains invalid characters".to_string()),
    }
}

fn main() {
    // Valid Base64
    let result = safe_decode(b"SGVsbG8sIFdvcmxkIQ==");
    match result {
        Ok(data) => println!("Decoded: {}", String::from_utf8(data).unwrap()),
        Err(e) => println!("Error: {}", e),
    }
    
    // Invalid Base64
    let result = safe_decode(b"Invalid_Base64!");
    match result {
        Ok(data) => println!("Decoded: {:?}", data),
        Err(e) => println!("Error: {}", e),
    }
}
```

### Working with Files

```rust
use base64::{base64_encode, base64_decode, BASE64_STANDARD};
use std::fs;

fn encode_file(file_path: &str) -> std::io::Result<String> {
    let data = fs::read(file_path)?;
    let encoded = base64_encode(&data, &BASE64_STANDARD);
    Ok(String::from_utf8(encoded).unwrap())
}

fn decode_file(encoded_data: &str, output_path: &str) -> Result<(), Box<dyn std::error::Error>> {
    let decoded = base64_decode(encoded_data.as_bytes())?;
    fs::write(output_path, decoded)?;
    Ok(())
}