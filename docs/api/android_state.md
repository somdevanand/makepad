# android_state API Reference

## Overview

The `android_state` crate provides management of Android-specific context states for the Makepad framework. It handles the JavaVM instance initialized by the JNI layer and the current Makepad Activity instance.

This API reference documents all public types, functions, and methods available in the crate.

## Functions

### `get_java_vm() -> *mut jni_sys::JavaVM`

Returns a raw pointer to the JavaVM instance initialized by the JNI layer.

If the JavaVM instance has not been initialized, this returns a null pointer.

```rust
pub fn get_java_vm() -> *mut jni_sys::JavaVM
```

**Returns:**
- A raw pointer to the JavaVM instance, or null if not initialized

**Safety:**
- The caller is responsible for ensuring safe usage of the returned pointer
- The pointer may become invalid if the JVM is destroyed

**Example:**
```rust
use android_state::get_java_vm;

let java_vm = get_java_vm();
if !java_vm.is_null() {
    // Use the JavaVM for JNI operations
}
```

### `get_activity() -> jni_sys::jobject`

Returns a raw pointer to the main Makepad Activity instance.

Note that the caller should not cache or re-use the returned activity pointer, but should instead re-call this function whenever the activity instance is needed. This is because the activity instance may be destroyed and recreated behind the scenes upon certain system actions, e.g., when the device is rotated, the app is put into split screen, resized/moved, etc.

If the Activity instance has not been initialized, this returns a null pointer.

```rust
pub fn get_activity() -> jni_sys::jobject
```

**Returns:**
- A raw pointer to the Activity instance, or null if not initialized

**Safety:**
- The caller is responsible for ensuring safe usage of the returned pointer
- The pointer may become invalid when the activity is recreated
- Do not cache this pointer; call this function each time you need the activity

**Example:**
```rust
use android_state::get_activity;

let activity = get_activity();
if !activity.is_null() {
    // Use the activity instance
    // Note: Do not cache this pointer
}
```

### `get_activity_setter_fn() -> Option<unsafe fn(jni_sys::jobject)>`

Returns a function that can be used to set the current Makepad Activity instance.

This will return `Some` only once, which guarantees that only the internal Makepad framework can obtain the function to set the activity instance.

```rust
pub fn get_activity_setter_fn() -> Option<unsafe fn(jni_sys::jobject)>
```

**Returns:**
- `Some(function)` if the setter function is available, `None` otherwise

**Note:**
- This function is intended for Makepad-internal use only
- It will only return `Some` on the first call
- External users should not use this function

## Static Items

### `JNI_OnLoad(vm: *mut jni_sys::JavaVM, _: std::ffi::c_void) -> jni_sys::jint`

JNI function called when the native library is loaded. This function initializes the JavaVM instance.

This function is marked with `#[no_mangle]` and `#[doc(hidden)]`.

```rust
#[no_mangle]
pub unsafe extern "C" fn JNI_OnLoad(
    vm: *mut jni_sys::JavaVM,
    _: std::ffi::c_void,
) -> jni_sys::jint
```

**Parameters:**
- `vm`: Pointer to the JavaVM instance
- `_`: Unused parameter

**Returns:**
- JNI version constant (JNI_VERSION_1_6)

### `jni_on_load(vm: *mut std::ffi::c_void)`

Alternative JNI load function that initializes the JavaVM instance.

```rust
extern "C" fn jni_on_load(vm: *mut std::ffi::c_void)
```

**Parameters:**
- `vm`: Pointer to the JavaVM instance as a c_void pointer

## Constants

The crate uses several static variables to maintain state:

### `ACTIVITY`

Static variable holding the current Activity instance.

```rust
static mut ACTIVITY: jni_sys::jobject
```

### `VM`

Static variable holding the JavaVM instance.

```rust
static mut VM: *mut jni_sys::JavaVM
```

### `SET_ACTIVITY_FN`

Static mutex-protected variable holding the activity setter function.

```rust
static SET_ACTIVITY_FN: Mutex<Option<unsafe fn(jni_sys::jobject)>>
```

## Safety Considerations

This crate deals with raw pointers and JNI interfaces, which require careful handling:

1. **Raw Pointers**: The functions return raw pointers that may be null or invalid
2. **Thread Safety**: The crate uses mutexes to ensure thread-safe access to state
3. **Activity Lifecycle**: Activity pointers may become invalid when the activity is recreated
4. **Single Initialization**: The JavaVM is only initialized once during the app's lifetime

## Usage Guidelines

### For External Users

External users should generally use the [`robius-android-env`](https://github.com/project-robius/robius-android-env) crate instead of using this crate directly. If direct usage is required, only use the two public functions:

1. `get_java_vm()`: Returns a pointer to the JavaVM instance
2. `get_activity()`: Returns a pointer to the current Makepad Activity instance

### For Makepad Framework Developers

Framework developers may need to use the internal functions for setting up the Android environment. The `get_activity_setter_fn()` function provides controlled access to set the activity instance.

## Error Handling

The crate does not perform explicit error handling. Instead, it relies on the caller to check for null pointers and handle initialization states appropriately.

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
```

### Checking Initialization Status

```rust
use android_state::{get_java_vm, get_activity};

fn check_initialization() {
    match (get_java_vm().is_null(), get_activity().is_null()) {
        (true, true) => println!("Neither JavaVM nor Activity initialized"),
        (true, false) => println!("JavaVM not initialized, Activity is ready"),
        (false, true) => println!("JavaVM initialized, Activity not ready"),
        (false, false) => println!("Both JavaVM and Activity are initialized"),
    }
}
```

### Safe Activity Usage

```rust
use android_state::get_activity;

fn safe_activity_usage() {
    // Always get a fresh activity pointer when needed
    let activity = get_activity();
    if activity.is_null() {
        // Handle the case where activity is not available
        return;
    }
    
    // Use the activity for JNI calls
    // ...
    
    // Do not store the activity pointer for later use
    // Get a fresh pointer each time you need it
}