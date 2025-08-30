# android_state Documentation

## Overview

The `android_state` crate is responsible for holding Makepad's Android-specific context states. It provides a safe way to manage and access the JavaVM instance and the current Makepad Activity instance in Android applications built with the Makepad framework.

## Purpose and Use Cases

This crate serves as a central repository for Android-specific state information that is needed by the Makepad framework and applications. It handles two critical pieces of state:

1. **JavaVM Instance**: The Java Virtual Machine instance initialized by the JNI layer
2. **Makepad Activity Instance**: The current Makepad Activity instance

## Key Features

- **Thread-Safe State Management**: Uses mutexes to ensure safe access to state across threads
- **Single Initialization Guarantee**: Ensures the JavaVM is only set once during the app's lifetime
- **Controlled Activity Management**: Provides controlled access to set the activity instance
- **Raw Pointer Access**: Offers efficient access to JNI objects through raw pointers
- **Android Lifecycle Awareness**: Handles activity recreation scenarios (e.g., device rotation)

## Getting Started

The `android_state` crate is typically used internally by the Makepad framework. External users should generally use the [`robius-android-env`](https://github.com/project-robius/robius-android-env) crate instead of using this crate directly.

For those who need to use it directly, add it to your `Cargo.toml`:

```toml
[dependencies]
android_state = "0.1"
```

## Core Concepts

### JavaVM Management

The JavaVM instance is initialized by the JNI layer and can only be set once during the lifetime of the app process. This crate provides safe access to this instance:

```rust
use android_state::get_java_vm;

// Get the JavaVM instance
let java_vm = get_java_vm();
if !java_vm.is_null() {
    // Use the JavaVM for JNI operations
}
```

### Activity Management

The Makepad Activity instance can be set by foreign code outside this crate, as the underlying Android platform may tear down and reconstruct the activity instance multiple times during the app's lifetime. However, for safety reasons, only a single caller can obtain the private "set_activity" function.

```rust
use android_state::get_activity;

// Get the current Makepad Activity instance
let activity = get_activity();
if !activity.is_null() {
    // Use the activity instance
    // Note: Do not cache this pointer as it may become invalid
}
```

## Important Considerations

### Activity Lifecycle

The activity instance may be destroyed and recreated behind the scenes upon certain system actions, such as:
- Device rotation
- App put into split screen
- App resized or moved

Because of this, callers should not cache or re-use the returned activity pointer. Instead, re-call the `get_activity()` function whenever the activity instance is needed.

### Safety Guarantees

The crate provides several safety guarantees:
- The JavaVM instance is only set once
- Only one caller can obtain the activity setter function
- Raw pointer access is provided with appropriate safety warnings

## API Overview

The crate exposes a minimal API focused on the two main state access functions:

1. `get_java_vm()`: Returns a pointer to the JavaVM instance
2. `get_activity()`: Returns a pointer to the current Makepad Activity instance

All other functions are intended for Makepad-internal use only.

## Examples

### Basic Usage

```rust
use android_state::{get_java_vm, get_activity};

// Get the JavaVM instance
let java_vm = get_java_vm();
if java_vm.is_null() {
    panic!("JavaVM not initialized");
}

// Get the current activity instance
let activity = get_activity();
if activity.is_null() {
    panic!("Activity not initialized");
}

// Use these instances for JNI operations
```

### Checking Initialization

```rust
use android_state::{get_java_vm, get_activity};

fn check_android_state() {
    let java_vm = get_java_vm();
    let activity = get_activity();
    
    if java_vm.is_null() {
        println!("JavaVM not initialized");
    } else {
        println!("JavaVM is available");
    }
    
    if activity.is_null() {
        println!("Activity not initialized");
    } else {
        println!("Activity is available");
    }
}
```

## Integration with Makepad Framework

This crate is designed to work seamlessly with the Makepad framework's Android integration. It handles the low-level state management that allows the framework to interact with the Android platform through JNI.

## Best Practices

1. **Always check for null pointers** when accessing JavaVM or Activity instances
2. **Never cache activity pointers** as they may become invalid
3. **Re-call `get_activity()`** whenever you need the activity instance
4. **Use `robius-android-env`** for most Android development needs instead of this low-level crate
5. **Handle initialization errors** gracefully in your application

## Troubleshooting

### Null Pointers

If `get_java_vm()` or `get_activity()` returns a null pointer, it means the respective instance has not been initialized yet. This typically happens when:
- The JNI_OnLoad function hasn't been called yet
- The activity hasn't been set by the framework
- There's an issue with the Android application lifecycle

### Activity Recreation Issues

If you're experiencing issues with activity recreation:
- Ensure you're not caching activity pointers
- Re-call `get_activity()` each time you need the instance
- Handle null activity pointers gracefully