# Working with Platform-Specific Features

## Overview
This tutorial will guide you through using platform-specific features in Makepad applications. You'll learn how to integrate with Android, leverage WebAssembly capabilities, and handle cross-platform considerations.

## Prerequisites
Before starting this tutorial, you should:
1. Have completed the Getting Started guide
2. Be familiar with basic Makepad concepts
3. Understand the basics of the target platforms you want to work with

## Android Integration

### Setting Up Android Development
To develop Android applications with Makepad, you'll need to set up the Android NDK and other tools:

1. Install Android Studio
2. Install the Android NDK
3. Set up environment variables:
   ```bash
   export ANDROID_NDK_HOME=/path/to/android/ndk
   export ANDROID_HOME=/path/to/android/sdk
   ```

### Accessing Android-Specific Features
Makepad provides access to Android-specific features through the `android_state` library:

```rust
// src/android_integration.rs
use makepad_android_state::{get_java_vm, get_activity};

fn access_android_features() {
    // Get the JavaVM instance
    let java_vm = get_java_vm();
    
    // Get the current activity instance
    let activity = get_activity();
    
    // Use these instances to interact with Android APIs through JNI
    // This is a conceptual example - actual implementation would require
    // proper JNI setup and error handling
}
```

### Handling Android Permissions
Android requires explicit permissions for certain operations:

```rust
// Conceptual example of requesting permissions
// Actual implementation would depend on the specific Makepad Android integration

fn request_camera_permission() {
    // Check if permission is already granted
    // if !has_permission("CAMERA") {
    //     // Request permission
    //     request_permission("CAMERA");
    // }
}

fn on_permission_result(permission: &str, granted: bool) {
    match permission {
        "CAMERA" => {
            if granted {
                // Enable camera functionality
                enable_camera();
            } else {
                // Show error or disable camera functionality
                show_permission_error("Camera permission is required for this feature.");
            }
        }
        _ => {}
    }
}

fn enable_camera() {
    // Enable camera functionality
}

fn show_permission_error(message: &str) {
    // Show error message to user
}
```

### Integrating with Android Sensors
You can integrate with Android sensors through JNI:

```rust
// Conceptual example of integrating with Android sensors
// Actual implementation would require proper JNI setup and error handling

fn setup_accelerometer() -> Result<(), Box<dyn std::error::Error>> {
    let java_vm = get_java_vm();
    let activity = get_activity();
    
    // Use JNI to access Android sensor APIs
    // This would involve:
    // 1. Getting the SensorManager
    // 2. Getting the accelerometer sensor
    // 3. Registering a sensor event listener
    
    Ok(())
}
```

## WebAssembly Integration

### Setting Up WebAssembly Development
To develop WebAssembly applications with Makepad, you'll need to install the wasm32 target:

```bash
rustup target add wasm32-unknown-unknown
```

### Exporting Functions to JavaScript
You can export Rust functions to JavaScript using the `wasm_bindgen` attribute:

```rust
// src/wasm_integration.rs
use wasm_bindgen::prelude::*;

#[wasm_bindgen]
pub fn greet(name: &str) -> String {
    format!("Hello, {}! Welcome to Makepad on the web!", name)
}

#[wasm_bindgen]
pub fn process_data(data: &[u8]) -> Vec<u8> {
    // Process the data
    data.iter().map(|x| x * 2).collect()
}
```

### Importing JavaScript Functions to Rust
You can import JavaScript functions to Rust using the `wasm_bindgen` attribute:

```rust
use wasm_bindgen::prelude::*;

#[wasm_bindgen]
extern "C" {
    fn alert(s: &str);
    
    #[wasm_bindgen(js_namespace = console)]
    fn log(s: &str);
}

#[wasm_bindgen]
pub fn show_message(message: &str) {
    alert(message);
    log(&format!("Message shown: {}", message));
}
```

### DOM Manipulation
Makepad applications can manipulate the DOM through JavaScript bindings:

```rust
use wasm_bindgen::prelude::*;
use web_sys::{Document, Element, HtmlElement};

#[wasm_bindgen]
extern "C" {
    #[wasm_bindgen(js_namespace = document)]
    fn get_element_by_id(id: &str) -> Element;
}

#[wasm_bindgen]
pub fn update_element_text(id: &str, text: &str) {
    let element = get_element_by_id(id);
    let html_element: HtmlElement = element.dyn_into().unwrap();
    html_element.set_inner_html(text);
}
```

### Canvas Rendering
You can integrate Makepad's drawing system with HTML5 Canvas:

```rust
use wasm_bindgen::prelude::*;
use web_sys::{CanvasRenderingContext2d, HtmlCanvasElement};

#[wasm_bindgen]
pub fn draw_to_canvas(canvas: &HtmlCanvasElement) -> Result<(), JsValue> {
    let context = canvas
        .get_context("2d")?
        .unwrap()
        .dyn_into::<CanvasRenderingContext2d>()?;
    
    context.begin_path();
    context.move_to(0.0, 0.0);
    context.line_to(100.0, 100.0);
    context.stroke();
    
    Ok(())
}
```

## Cross-Platform Considerations

### Platform Detection
You can detect the current platform at runtime:

```rust
use makepad_platform::*;

fn get_platform_info() -> String {
    if cfg!(target_os = "android") {
        "Android".to_string()
    } else if cfg!(target_arch = "wasm32") {
        "Web".to_string()
    } else if cfg!(target_os = "windows") {
        "Windows".to_string()
    } else if cfg!(target_os = "macos") {
        "macOS".to_string()
    } else if cfg!(target_os = "linux") {
        "Linux".to_string()
    } else {
        "Unknown".to_string()
    }
}
```

### Conditional Compilation
Use conditional compilation to include platform-specific code:

```rust
#[cfg(target_os = "android")]
mod android_specific {
    pub fn platform_feature() {
        // Android-specific implementation
    }
}

#[cfg(target_arch = "wasm32")]
mod web_specific {
    pub fn platform_feature() {
        // Web-specific implementation
    }
}

#[cfg(not(any(target_os = "android", target_arch = "wasm32")))]
mod desktop_specific {
    pub fn platform_feature() {
        // Desktop-specific implementation
    }
}

fn use_platform_feature() {
    #[cfg(target_os = "android")]
    android_specific::platform_feature();
    
    #[cfg(target_arch = "wasm32")]
    web_specific::platform_feature();
    
    #[cfg(not(any(target_os = "android", target_arch = "wasm32")))]
    desktop_specific::platform_feature();
}
```

### File System Access
Handle file system access differently based on the platform:

```rust
use std::fs;

#[cfg(not(target_arch = "wasm32"))]
fn save_data(data: &[u8], path: &str) -> Result<(), std::io::Error> {
    fs::write(path, data)
}

#[cfg(target_arch = "wasm32")]
fn save_data(data: &[u8], path: &str) -> Result<(), Box<dyn std::error::Error>> {
    // In WebAssembly, use browser storage instead
    use wasm_bindgen::prelude::*;
    
    #[wasm_bindgen]
    extern "C" {
        #[wasm_bindgen(js_namespace = localStorage)]
        fn setItem(key: &str, value: &str);
    }
    
    let data_str = std::str::from_utf8(data)?;
    setItem(path, data_str);
    Ok(())
}
```

## Practical Examples

### Cross-Platform Data Storage
Create a unified interface for data storage across platforms:

```rust
pub trait DataStorage {
    fn save(&self, key: &str, data: &[u8]) -> Result<(), Box<dyn std::error::Error>>;
    fn load(&self, key: &str) -> Result<Vec<u8>, Box<dyn std::error::Error>>;
}

#[cfg(not(target_arch = "wasm32"))]
pub struct FileStorage;

#[cfg(not(target_arch = "wasm32"))]
impl DataStorage for FileStorage {
    fn save(&self, key: &str, data: &[u8]) -> Result<(), Box<dyn std::error::Error>> {
        let path = format!("{}.dat", key);
        std::fs::write(path, data)?;
        Ok(())
    }
    
    fn load(&self, key: &str) -> Result<Vec<u8>, Box<dyn std::error::Error>> {
        let path = format!("{}.dat", key);
        let data = std::fs::read(path)?;
        Ok(data)
    }
}

#[cfg(target_arch = "wasm32")]
pub struct WebStorage;

#[cfg(target_arch = "wasm32")]
impl DataStorage for WebStorage {
    fn save(&self, key: &str, data: &[u8]) -> Result<(), Box<dyn std::error::Error>> {
        use wasm_bindgen::prelude::*;
        
        #[wasm_bindgen]
        extern "C" {
            #[wasm_bindgen(js_namespace = localStorage)]
            fn setItem(key: &str, value: &str);
        }
        
        let data_str = std::str::from_utf8(data)?;
        setItem(key, data_str);
        Ok(())
    }
    
    fn load(&self, key: &str) -> Result<Vec<u8>, Box<dyn std::error::Error>> {
        use wasm_bindgen::prelude::*;
        
        #[wasm_bindgen]
        extern "C" {
            #[wasm_bindgen(js_namespace = localStorage)]
            fn getItem(key: &str) -> Option<String>;
        }
        
        if let Some(data_str) = getItem(key) {
            let data = data_str.into_bytes();
            Ok(data)
        } else {
            Err("Key not found".into())
        }
    }
}
```

### Platform-Specific UI Elements
Create platform-specific UI elements:

```rust
#[cfg(target_os = "android")]
mod android_ui {
    use makepad_draw::*;
    
    pub struct PlatformButton {
        draw_bg: DrawQuad,
        draw_text: DrawText,
    }
    
    impl PlatformButton {
        pub fn new() -> Self {
            Self {
                draw_bg: DrawQuad::new(),
                draw_text: DrawText::new(),
            }
        }
        
        pub fn draw(&mut self, cx: &mut Cx2d, label: &str) {
            // Android-style button with material design
            self.draw_bg.color = vec4(0.1, 0.5, 1.0, 1.0); // Material blue
            self.draw_bg.draw_quad(cx, &Walk::default(), &Layout::default());
            
            self.draw_text.text = label.to_string();
            self.draw_text.color = vec4(1.0, 1.0, 1.0, 1.0); // White text
            self.draw_text.draw_text(cx, label);
        }
    }
}

#[cfg(target_arch = "wasm32")]
mod web_ui {
    use makepad_draw::*;
    
    pub struct PlatformButton {
        draw_bg: DrawQuad,
        draw_text: DrawText,
    }
    
    impl PlatformButton {
        pub fn new() -> Self {
            Self {
                draw_bg: DrawQuad::new(),
                draw_text: DrawText::new(),
            }
        }
        
        pub fn draw(&mut self, cx: &mut Cx2d, label: &str) {
            // Web-style button with flat design
            self.draw_bg.color = vec4(0.2, 0.7, 0.2, 1.0); // Flat green
            self.draw_bg.draw_quad(cx, &Walk::default(), &Layout::default());
            
            self.draw_text.text = label.to_string();
            self.draw_text.color = vec4(1.0, 1.0, 1.0, 1.0); // White text
            self.draw_text.draw_text(cx, label);
        }
    }
}
```

## Best Practices

### Platform Abstraction
1. **Create unified interfaces**: Define traits for platform-specific functionality
2. **Use conditional compilation**: Implement platform-specific code with `#[cfg]` attributes
3. **Handle errors gracefully**: Account for platform-specific error conditions
4. **Test on all platforms**: Ensure your code works correctly on all target platforms

### Performance Considerations
1. **Minimize platform calls**: Batch operations and minimize calls between Rust and platform APIs
2. **Optimize for each platform**: Use platform-specific optimizations where appropriate
3. **Handle threading carefully**: Be aware of platform threading models and restrictions
4. **Manage memory efficiently**: Properly manage memory between Rust and platform environments

### Security Considerations
1. **Validate inputs**: Always validate data received from platform APIs
2. **Handle permissions properly**: Request and check permissions as needed
3. **Sanitize data**: Clean data before passing it between environments
4. **Follow platform guidelines**: Adhere to platform security best practices

## Conclusion
You've now learned how to work with platform-specific features in Makepad applications, including Android integration and WebAssembly capabilities. By following these patterns, you can create cross-platform applications that take full advantage of each platform's unique features while maintaining a consistent codebase.