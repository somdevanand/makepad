# base64 API Reference

## Overview

The `base64` crate provides functionality for encoding and decoding data using the Base64 encoding scheme. This API reference documents all public types, functions, and constants available in the crate.

## Constants

### `BASE64_STANDARD`

The standard Base64 character set.

```rust
pub const BASE64_STANDARD: [u8; 64]
```

This constant contains the standard Base64 alphabet:
- Uppercase letters: A-Z (26 characters)
- Lowercase letters: a-z (26 characters)
- Digits: 0-9 (10 characters)
- Special characters: + and / (2 characters)

### `BASE64_URL_SAFE`

The URL-safe Base64 character set.

```rust
pub const BASE64_URL_SAFE: [u8; 64]
```

This constant contains the URL-safe Base64 alphabet:
- Uppercase letters: A-Z (26 characters)
- Lowercase letters: a-z (26 characters)
- Digits: 0-9 (10 characters)
- Special characters: - and _ (2 characters)

## Functions

### `base64_encode(inp: &[u8], table: &[u8; 64]) -> Vec<u8>`

Encodes binary data using the specified character table.

```rust
pub fn base64_encode(inp: &[u8], table: &[u8; 64]) -> Vec<u8>
```

**Parameters:**
- `inp`: The binary data to encode
- `table`: The character table to use for encoding (e.g., `BASE64_STANDARD` or `BASE64_URL_SAFE`)

**Returns:**
- A `Vec<u8>` containing the Base64-encoded data

**Example:**
```rust
use base64::{base64_encode, BASE64_STANDARD, BASE64_URL_SAFE};

let data = b"Hello, World!";

// Standard Base64 encoding
let encoded_standard = base64_encode(data, &BASE64_STANDARD);
println!("{}", String::from_utf8(encoded_standard).unwrap());

// URL-safe Base64 encoding
let encoded_url_safe = base64_encode(data, &BASE64_URL_SAFE);
println!("{}", String::from_utf8(encoded_url_safe).unwrap());
```

### `base64_decode(input: &[u8]) -> Result<Vec<u8>, Base64DecodeError>`

Decodes Base64-encoded data.

```rust
pub fn base64_decode(input: &[u8]) -> Result<Vec<u8>, Base64DecodeError>
```

**Parameters:**
- `input`: The Base64-encoded data to decode

**Returns:**
- `Ok(Vec<u8>)` containing the decoded binary data
- `Err(Base64DecodeError)` if decoding fails

**Example:**
```rust
use base64::{base64_decode, base64_encode, BASE64_STANDARD};

let data = b"Hello, World!";
let encoded = base64_encode(data, &BASE64_STANDARD);
let decoded = base64_decode(&encoded).unwrap();
assert_eq!(data, decoded.as_slice());
```

## Enums

### `Base64DecodeError`

Error type for Base64 decoding operations.

```rust
#[derive(Debug)]
pub enum Base64DecodeError {
    WrongPadding,
    InvalidCharacter,
}
```

#### Variants

##### `WrongPadding`

Indicates that the input length is not a multiple of 4, which is required for valid Base64 data.

##### `InvalidCharacter`

Indicates that the input contains characters that are not valid in Base64 encoding.

## Implementation Details

### Encoding Process

The encoding function processes input data in chunks of 3 bytes, converting them to 4 Base64 characters:

1. For each 3-byte chunk, 24 bits are processed
2. These 24 bits are divided into 4 groups of 6 bits each
3. Each 6-bit group is used as an index into the character table
4. For the final chunk (if less than 3 bytes), padding characters (=) are added

### Decoding Process

The decoding function processes input data in chunks of 4 Base64 characters:

1. Each character is converted to its 6-bit value using a lookup table
2. Four 6-bit values are combined to form three 8-bit bytes
3. Padding characters (=) at the end are handled appropriately
4. Invalid characters or incorrect padding result in errors

## Examples

### Basic Encoding

```rust
use base64::{base64_encode, BASE64_STANDARD};

let data = b"Hello, World!";
let encoded = base64_encode(data, &BASE64_STANDARD);
assert_eq!(encoded, b"SGVsbG8sIFdvcmxkIQ==");
```

### URL-Safe Encoding

```rust
use base64::{base64_encode, BASE64_URL_SAFE};

let data = b"Hello, World!";
let encoded = base64_encode(data, &BASE64_URL_SAFE);
assert_eq!(encoded, b"SGVsbG8sIFdvcmxkIQ==");
```

Note: The URL-safe encoding produces the same output as standard encoding for this particular input because it doesn't contain characters that differ between the two alphabets.

### Decoding with Error Handling

```rust
use base64::{base64_decode, Base64DecodeError};

// Valid Base64
match base64_decode(b"SGVsbG8sIFdvcmxkIQ==") {
    Ok(data) => println!("Decoded: {}", String::from_utf8(data).unwrap()),
    Err(Base64DecodeError::WrongPadding) => println!("Wrong padding"),
    Err(Base64DecodeError::InvalidCharacter) => println!("Invalid character"),
}

// Invalid Base64 with wrong padding
match base64_decode(b"SGVsbG8sIFdvcmxkIQ=") {
    Ok(data) => println!("Decoded: {:?}", data),
    Err(Base64DecodeError::WrongPadding) => println!("Wrong padding"),
    Err(Base64DecodeError::InvalidCharacter) => println!("Invalid character"),
}

// Invalid Base64 with invalid characters
match base64_decode(b"SGVsbG8s!IFdvcmxkIQ==") {
    Ok(data) => println!("Decoded: {:?}", data),
    Err(Base64DecodeError::WrongPadding) => println!("Wrong padding"),
    Err(Base64DecodeError::InvalidCharacter) => println!("Invalid character"),
}
```

### Round-Trip Encoding and Decoding

```rust
use base64::{base64_encode, base64_decode, BASE64_STANDARD};

fn round_trip_test(data: &[u8]) {
    let encoded = base64_encode(data, &BASE64_STANDARD);
    let decoded = base64_decode(&encoded).expect("Decoding failed");
    assert_eq!(data, decoded.as_slice());
}

round_trip_test(b"");
round_trip_test(b"A");
round_trip_test(b"AB");
round_trip_test(b"ABC");
round_trip_test(b"Hello, World!");
```

### Working with Different Data Types

```rust
use base64::{base64_encode, base64_decode, BASE64_STANDARD};

// Working with vectors
let data = vec![1, 2, 3, 4, 5];
let encoded = base64_encode(&data, &BASE64_STANDARD);
let decoded = base64_decode(&encoded).unwrap();
assert_eq!(data, decoded);

// Working with strings
let text = "Hello, 世界!";
let encoded = base64_encode(text.as_bytes(), &BASE64_STANDARD);
let decoded = base64_decode(&encoded).unwrap();
let decoded_text = String::from_utf8(decoded).unwrap();
assert_eq!(text, decoded_text);