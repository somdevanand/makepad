# Android Integration

## Overview
Makepad provides comprehensive support for Android development through the `android_state` library and related components. This documentation covers how to integrate Makepad applications with the Android platform, manage Android-specific states, and handle JNI interactions.

## Android State Management

### Overview
The `makepad_android_state` library is responsible for holding Makepad's Android-specific context states. It provides a safe way for external crates to access Android states without depending on the entire Makepad framework.

### Key Components

#### JavaVM Instance
The JavaVM instance is initialized by the JNI layer and cannot be set by foreign code outside this crate, as it is only set once during the lifetime of the app process.

#### Makepad Activity Instance
The current Makepad Activity instance can be set by foreign code outside this crate, as the underlying Android platform may tear down and reconstruct the activity instance multiple times during the app's lifetime. However, for safety reasons, only a single caller can obtain the private "set_activity" function, ensuring that only the internal Makepad framework can set the activity instance.

### Usage
External users of this crate should only care about two functions:
1. `get_java_vm()`: Returns a pointer to the JavaVM instance, through which you can obtain the JNI environment.
2. `get_activity()`: Returns a pointer to the current Makepad Activity instance.

### Basic Usage Example
```rust
use makepad_android_state::{get_java_vm, get_activity};

// Get the JavaVM instance
let java_vm = get_java_vm();

// Get the current activity instance
let activity = get_activity();

// Use these instances to interact with Android APIs through JNI
```

### Safety Considerations
1. The JavaVM instance should only be accessed when needed and properly managed
2. Activity instances may become invalid if the Android platform recreates the activity
3. Always check for null pointers when working with these instances
4. Follow Android lifecycle best practices when interacting with these components

## JNI Integration

### Overview
Makepad applications can interact with Java/Kotlin code through the Java Native Interface (JNI). The framework provides utilities to simplify JNI interactions.

### Accessing JNI Environment
```rust
use makepad_android_state::get_java_vm;
use jni::JNIEnv;

// Get the JavaVM instance
let java_vm = get_java_vm();

// Get the JNI environment
// Note: This is a conceptual example - actual implementation may vary
// let env = java_vm.get_env();
```

### Calling Java Methods
```rust
// Conceptual example of calling Java methods through JNI
// Actual implementation would require proper error handling and type conversions

/*
unsafe {
    // Get the JNI environment
    let env = java_vm.get_env()?;
    
    // Find the Java class
    let class = env.find_class("com/example/MyClass")?;
    
    // Get the method ID
    let method_id = env.get_static_method_id(class, "myMethod", "()V")?;
    
    // Call the static method
    env.call_static_method_unchecked(class, method_id, JavaType::Void, &[])?;
}
*/
```

### Passing Data Between Rust and Java
```rust
// Conceptual example of passing data between Rust and Java
// Actual implementation would require proper error handling and type conversions

/*
// Convert Rust string to Java string
let rust_string = "Hello from Rust!";
let java_string = env.new_string(rust_string)?;

// Call Java method with string parameter
let class = env.find_class("com/example/MyClass")?;
let method_id = env.get_static_method_id(class, "processString", "(Ljava/lang/String;)V")?;
env.call_static_method_unchecked(class, method_id, JavaType::Void, &[java_string.into()])?;
*/
```

## Activity Lifecycle Management

### Overview
Android activities have a complex lifecycle that Makepad applications need to handle properly. The framework provides utilities to manage activity state changes.

### Lifecycle Events
1. **onCreate**: Called when the activity is first created
2. **onStart**: Called when the activity becomes visible
3. **onResume**: Called when the activity starts interacting with the user
4. **onPause**: Called when the activity is partially obscured
5. **onStop**: Called when the activity is no longer visible
6. **onDestroy**: Called before the activity is destroyed
7. **onRestart**: Called after the activity has been stopped but before it starts again

### Handling Lifecycle Events
```rust
// Conceptual example of handling lifecycle events
// Actual implementation would depend on the specific Makepad Android integration

/*
struct MyApplication {
    // Application state
}

impl MyApplication {
    fn on_create(&mut self) {
        // Initialize application state
    }
    
    fn on_pause(&mut self) {
        // Save application state
    }
    
    fn on_resume(&mut self) {
        // Restore application state
    }
    
    fn on_destroy(&mut self) {
        // Clean up resources
    }
}
*/
```

## Permissions Handling

### Overview
Android requires explicit permissions for certain operations. Makepad applications need to handle permission requests properly.

### Requesting Permissions
```rust
// Conceptual example of requesting permissions
// Actual implementation would depend on the specific Makepad Android integration

/*
fn request_camera_permission() {
    // Check if permission is already granted
    // if !has_permission("CAMERA") {
    //     // Request permission
    //     request_permission("CAMERA");
    // }
}
*/
```

### Handling Permission Results
```rust
// Conceptual example of handling permission results
// Actual implementation would depend on the specific Makepad Android integration

/*
fn on_permission_result(permission: &str, granted: bool) {
    match permission {
        "CAMERA" => {
            if granted {
                // Enable camera functionality
            } else {
                // Show error or disable camera functionality
            }
        }
        _ => {}
    }
}
*/
```

## Practical Examples

### Integrating with Android Sensors
```rust
// Conceptual example of integrating with Android sensors
// Actual implementation would require proper JNI setup and error handling

/*
fn setup_accelerometer() -> Result<(), Box<dyn std::error::Error>> {
    let java_vm = get_java_vm();
    let activity = get_activity();
    
    // Use JNI to access Android sensor APIs
    // ...
    
    Ok(())
}
*/
```

### Saving and Loading Application State
```rust
// Conceptual example of saving and loading application state
// Actual implementation would depend on the specific Makepad Android integration

/*
fn save_state() {
    // Save application state to Android preferences or file system
}

fn load_state() {
    // Load application state from Android preferences or file system
}
*/
```

## Performance Considerations

1. **JNI Overhead**: Minimize JNI calls as they have performance overhead
2. **Memory Management**: Properly manage memory between Rust and Java
3. **Threading**: Be aware of Android's threading model and restrictions
4. **Battery Usage**: Optimize sensor usage and background operations

## Best Practices

1. **Handle Activity Recreation**: Properly handle cases where Android recreates the activity
2. **Save State Appropriately**: Save important state data during onPause or onSaveInstanceState
3. **Check Permissions**: Always check for required permissions before using sensitive APIs
4. **Use Background Threads**: Perform heavy operations on background threads to avoid blocking the UI
5. **Follow Android Guidelines**: Adhere to Android design and development guidelines

## Related Components
- `makepad_android_state`: Core Android state management
- `jni-sys`: Low-level JNI bindings
- `makepad_platform`: Platform abstraction layer