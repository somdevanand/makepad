# digest Documentation

## Overview

The `digest` crate provides implementations of cryptographic hash functions. Currently, it includes an implementation of the SHA-1 hash algorithm. This crate is designed to be lightweight and focused on providing essential hashing functionality without external dependencies.

## Purpose and Use Cases

The digest crate is useful for:

- Generating SHA-1 hashes for data integrity verification
- Creating unique identifiers for data
- Implementing simple cryptographic protocols
- Working with systems that require SHA-1 hashing
- Educational purposes to understand hash function implementations

## Key Features

- **SHA-1 Implementation**: Complete implementation of the SHA-1 hash algorithm
- **No External Dependencies**: Self-contained implementation with no external dependencies
- **Simple API**: Easy-to-use interface for hashing data
- **Memory Efficient**: Minimal memory overhead during hashing
- **Standard Compliance**: Implements the standard SHA-1 algorithm as defined in RFC 3174

## Getting Started

To use the `digest` crate, add it to your `Cargo.toml`:

```toml
[dependencies]
digest = "0.1"
```

Then, in your Rust code:

```rust
use digest::sha1::Sha1;

// Create a new SHA-1 hasher
let mut hasher = Sha1::new();

// Add data to be hashed
hasher.update(b"Hello, world!");

// Finalize the hash and get the result
let hash = hasher.finalise();

// Print the hash as hexadecimal
println!("{:x?}", hash);
```

## Core Concepts

### SHA-1 Hash Algorithm

SHA-1 (Secure Hash Algorithm 1) is a cryptographic hash function that takes an input and produces a 160-bit (20-byte) hash value. While SHA-1 is no longer considered secure against well-funded attackers, it is still widely used for non-security purposes and legacy systems.

### Hasher State

The SHA-1 hasher maintains internal state during the hashing process:
- **State**: The current internal state of the hasher (160 bits)
- **Block Buffer**: Buffer for incoming data (512 bits)
- **Total Length**: Total number of bytes processed
- **Block Position**: Current position in the block buffer

## Basic Operations

### Creating a Hasher

```rust
use digest::sha1::Sha1;

// Create a new hasher
let mut hasher = Sha1::new();

// Or use the default implementation
let mut hasher: Sha1 = Default::default();
```

### Updating with Data

```rust
use digest::sha1::Sha1;

let mut hasher = Sha1::new();

// Update with a byte slice
hasher.update(b"Hello, ");

// Update with another byte slice
hasher.update(b"world!");

// Update with data from a Vec
let data = vec![1, 2, 3, 4, 5];
hasher.update(&data);
```

### Finalizing the Hash

```rust
use digest::sha1::Sha1;

let mut hasher = Sha1::new();
hasher.update(b"Hello, world!");

// Finalize and get the hash
let hash = hasher.finalise();

// The hash is a 20-byte array
assert_eq!(hash.len(), 20);
```

## Working with Hash Results

### Converting to Hexadecimal

```rust
use digest::sha1::Sha1;

let mut hasher = Sha1::new();
hasher.update(b"Hello, world!");
let hash = hasher.finalise();

// Convert to hexadecimal string
let hex_string: String = hash.iter().map(|b| format!("{:02x}", b)).collect();
println!("SHA-1 hash: {}", hex_string);
```

### Comparing Hashes

```rust
use digest::sha1::Sha1;

fn hash_data(data: &[u8]) -> [u8; 20] {
    let mut hasher = Sha1::new();
    hasher.update(data);
    hasher.finalise()
}

let hash1 = hash_data(b"Hello, world!");
let hash2 = hash_data(b"Hello, world!");
let hash3 = hash_data(b"Hello, Rust!");

// Compare hashes
assert_eq!(hash1, hash2);  // Same data produces same hash
assert_ne!(hash1, hash3);  // Different data produces different hash
```

## Performance Considerations

### Memory Usage

The SHA-1 implementation is memory efficient:
- Fixed-size internal state (20 bytes)
- Fixed-size block buffer (64 bytes)
- Minimal overhead for tracking progress

### Processing Large Data

For large data sets, the hasher processes data in 512-bit (64-byte) blocks:

```rust
use digest::sha1::Sha1;

fn hash_large_data(data: &[u8]) -> [u8; 20] {
    let mut hasher = Sha1::new();
    
    // Process data in chunks for better memory efficiency
    const CHUNK_SIZE: usize = 1024;
    for chunk in data.chunks(CHUNK_SIZE) {
        hasher.update(chunk);
    }
    
    hasher.finalise()
}
```

## Security Considerations

### SHA-1 Security Status

While included for compatibility and educational purposes, it's important to note that SHA-1 is no longer considered cryptographically secure:

- **Not Recommended for Security**: SHA-1 is vulnerable to collision attacks
- **Use SHA-2 or SHA-3**: For security-sensitive applications, use SHA-256 or higher
- **Legacy Systems**: SHA-1 is still useful for legacy systems and non-security purposes

### When to Use SHA-1

SHA-1 is appropriate for:
- Data integrity checks where security is not a concern
- Legacy system compatibility
- Educational purposes
- Non-cryptographic hash table implementations

## Best Practices

1. **Use for Non-Security Purposes**: Only use SHA-1 for non-security applications
2. **Consider Alternatives**: For security, use SHA-256 or SHA-3
3. **Process in Chunks**: For large data, process in reasonable chunks
4. **Verify Implementation**: When using for critical applications, verify the implementation against test vectors
5. **Handle Errors**: Always handle potential errors in your application logic

## Examples

### Basic Hashing

```rust
use digest::sha1::Sha1;

fn main() {
    // Hash a simple string
    let mut hasher = Sha1::new();
    hasher.update(b"Hello, world!");
    let hash = hasher.finalise();
    
    println!("SHA-1('Hello, world!'): {:x?}", hash);
    
    // Hash multiple inputs
    let mut hasher = Sha1::new();
    hasher.update(b"Hello, ");
    hasher.update(b"world!");
    let hash = hasher.finalise();
    
    println!("SHA-1('Hello, ' + 'world!'): {:x?}", hash);
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
    // Hash a file (if it exists)
    match hash_file("example.txt") {
        Ok(hash) => {
            let hex_string: String = hash.iter().map(|b| format!("{:02x}", b)).collect();
            println!("File hash: {}", hex_string);
        }
        Err(e) => println!("Error hashing file: {}", e),
    }
    
    Ok(())
}
```

### Hash Comparison Utility

```rust
use digest::sha1::Sha1;

fn hash_data(data: &[u8]) -> [u8; 20] {
    let mut hasher = Sha1::new();
    hasher.update(data);
    hasher.finalise()
}

fn verify_data(data: &[u8], expected_hash: &[u8; 20]) -> bool {
    let actual_hash = hash_data(data);
    &actual_hash == expected_hash
}

fn main() {
    let data = b"Important data that must not be tampered with";
    let hash = hash_data(data);
    
    // Store the hash for later verification
    println!("Data hash: {:x?}", hash);
    
    // Later, verify the data hasn't been modified
    if verify_data(data, &hash) {
        println!("Data integrity verified!");
    } else {
        println!("Data has been tampered with!");
    }
    
    // Check with modified data
    let modified_data = b"Important data that has been tampered with";
    if verify_data(modified_data, &hash) {
        println!("Data integrity verified!");
    } else {
        println!("Data has been tampered with!");
    }
}