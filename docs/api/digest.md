# digest API Reference

## Overview

The `digest` crate provides implementations of cryptographic hash functions. Currently, it includes an implementation of the SHA-1 hash algorithm. This API reference documents all public types and methods available in the crate.

## Modules

### `sha1`

The SHA-1 hash implementation module.

```rust
pub mod sha1;
```

## Structs

### `Sha1`

A SHA-1 hash generator.

```rust
pub struct Sha1 {
    // fields omitted
}
```

#### Methods

##### `Sha1::new() -> Sha1`

Creates a new SHA-1 hasher instance.

```rust
pub fn new() -> Sha1
```

**Returns:**
- A new SHA-1 hasher with initialized state

**Example:**
```rust
use digest::sha1::Sha1;

let hasher = Sha1::new();
```

##### `Sha1::update(&mut self, bytes: &[u8])`

Adds data to the hasher.

```rust
pub fn update(&mut self, bytes: &[u8])
```

**Parameters:**
- `bytes`: The data to be hashed

**Example:**
```rust
use digest::sha1::Sha1;

let mut hasher = Sha1::new();
hasher.update(b"Hello, world!");
```

##### `Sha1::finalise(self) -> [u8; U8_STATE_LEN]`

Finalizes the hash computation and returns the result.

```rust
pub fn finalise(self) -> [u8; U8_STATE_LEN]
```

**Returns:**
- A 20-byte array containing the SHA-1 hash

**Example:**
```rust
use digest::sha1::Sha1;

let mut hasher = Sha1::new();
hasher.update(b"Hello, world!");
let hash = hasher.finalise();
```

#### Trait Implementations

- `Default`: Creates a new SHA-1 hasher with default initialization

## Constants

### `STATE_LEN`

The length of the SHA-1 state in 32-bit words.

```rust
pub const STATE_LEN: usize = 5;
```

### `BLOCK_LEN`

The length of the SHA-1 block in 32-bit words.

```rust
pub const BLOCK_LEN: usize = 16;
```

### `U8_BLOCK_LEN`

The length of the SHA-1 block in bytes.

```rust
pub const U8_BLOCK_LEN: usize = 64;
```

### `U8_STATE_LEN`

The length of the SHA-1 state in bytes.

```rust
pub const U8_STATE_LEN: usize = 20;
```

### `K0`, `K1`, `K2`, `K3`

The SHA-1 round constants.

```rust
pub const K0: u32 = 0x5A827999u32;
pub const K1: u32 = 0x6ED9EBA1u32;
pub const K2: u32 = 0x8F1BBCDCu32;
pub const K3: u32 = 0xCA62C1D6u32;
```

### `SHA1_INIT_STATE`

The initial state for SHA-1 hashing.

```rust
pub const SHA1_INIT_STATE: [u32; STATE_LEN] = [0x67452301, 0xEFCDAB89, 0x98BADCFE, 0x10325476, 0xC3D2E1F0];
```

## Functions

### `sha1_digest_bytes(state: &mut[u32; STATE_LEN], bytes: &[u8; U8_BLOCK_LEN])`

Processes a single 512-bit block of data with the SHA-1 algorithm.

```rust
pub fn sha1_digest_bytes(state: &mut[u32; STATE_LEN], bytes: &[u8; U8_BLOCK_LEN])
```

**Parameters:**
- `state`: The current SHA-1 state to update
- `bytes`: The 64-byte block to process

### `sha1_state_to_bytes(state: &[u32; STATE_LEN]) -> [u8; U8_STATE_LEN]`

Converts the SHA-1 state to a byte array in big-endian format.

```rust
pub fn sha1_state_to_bytes(state: &[u32; STATE_LEN]) -> [u8; U8_STATE_LEN]
```

**Parameters:**
- `state`: The SHA-1 state to convert

**Returns:**
- A 20-byte array containing the SHA-1 hash in big-endian format

## Examples

### Basic Usage

```rust
use digest::sha1::Sha1;

fn main() {
    // Create a new hasher
    let mut hasher = Sha1::new();
    
    // Add data to hash
    hasher.update(b"Hello, world!");
    
    // Finalize and get the hash
    let hash = hasher.finalise();
    
    // Print the hash
    println!("SHA-1 hash: {:x?}", hash);
}
```

### Hashing Multiple Inputs

```rust
use digest::sha1::Sha1;

fn main() {
    let mut hasher = Sha1::new();
    
    // Hash multiple inputs
    hasher.update(b"Hello, ");
    hasher.update(b"world!");
    
    let hash = hasher.finalise();
    println!("SHA-1 hash: {:x?}", hash);
}
```

### Comparing Hashes

```rust
use digest::sha1::Sha1;

fn hash_data(data: &[u8]) -> [u8; 20] {
    let mut hasher = Sha1::new();
    hasher.update(data);
    hasher.finalise()
}

fn main() {
    let hash1 = hash_data(b"Hello, world!");
    let hash2 = hash_data(b"Hello, world!");
    let hash3 = hash_data(b"Hello, Rust!");
    
    // Compare hashes
    assert_eq!(hash1, hash2);
    assert_ne!(hash1, hash3);
}
```

### Working with Hexadecimal

```rust
use digest::sha1::Sha1;

fn main() {
    let mut hasher = Sha1::new();
    hasher.update(b"Hello, world!");
    let hash = hasher.finalise();
    
    // Convert to hexadecimal string
    let hex_string: String = hash.iter().map(|b| format!("{:02x}", b)).collect();
    println!("SHA-1 hash: {}", hex_string);
}
```

### File Hashing

```rust
use digest::sha1::Sha1;
use std::fs::File;
use std::io::Read;

fn hash_file(filename: &str) -> std::io::Result<[u8; 20]> {
    let mut file = File::open(filename)?;
    let mut hasher = Sha1::new();
    let mut buffer = [0; 1024];
    
    loop {
        let bytes_read = file.read(&mut buffer)?;
        if bytes_read == 0 {
            break;
        }
        hasher.update(&buffer[..bytes_read]);
    }
    
    Ok(hasher.finalise())
}

fn main() -> std::io::Result<()> {
    match hash_file("example.txt") {
        Ok(hash) => {
            let hex_string: String = hash.iter().map(|b| format!("{:02x}", b)).collect();
            println!("File hash: {}", hex_string);
        }
        Err(e) => println!("Error: {}", e),
    }
    
    Ok(())
}