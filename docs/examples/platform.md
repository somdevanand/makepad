# Platform-Specific Features Examples

## Overview
This document provides examples of how to work with platform-specific features in Makepad applications. These examples demonstrate best practices for integrating with Android, leveraging WebAssembly capabilities, and handling cross-platform considerations.

## Android Integration Examples

### Accessing Android Sensors
Example of integrating with Android sensors through JNI:

```rust
// src/android/sensor_integration.rs
use makepad_android_state::{get_java_vm, get_activity};
use jni::JNIEnv;
use jni::objects::{JClass, JObject, JString};
use jni::sys::jstring;

// Conceptual example - actual implementation would require proper error handling
fn setup_accelerometer() -> Result<(), Box<dyn std::error::Error>> {
    let java_vm = get_java_vm();
    let activity = get_activity();
    
    // In a real implementation, you would:
    // 1. Get the SensorManager from the activity
    // 2. Get the accelerometer sensor
    // 3. Register a sensor event listener
    // 4. Handle sensor events in a callback
    
    Ok(())
}

// JNI callback for sensor events
#[no_mangle]
pub extern "system" fn Java_com_example_MakepadSensorListener_onSensorChanged(
    _env: JNIEnv,
    _class: JClass,
    sensor_type: i32,
    values: JObject,
) {
    // Handle sensor data
    match sensor_type {
        1 => { // TYPE_ACCELEROMETER
            // Process accelerometer data
            // This would involve extracting values from the Java array
            // and updating application state
        }
        _ => {}
    }
}
```

### Handling Android Permissions
Example of requesting and handling Android permissions:

```rust
// src/android/permission_handler.rs
use makepad_android_state::{get_activity};

struct PermissionHandler {
    camera_granted: bool,
    location_granted: bool,
}

impl PermissionHandler {
    fn new() -> Self {
        Self {
            camera_granted: false,
            location_granted: false,
        }
    }
    
    fn request_camera_permission(&self) {
        // Conceptual example - actual implementation would use JNI
        /*
        unsafe {
            let activity = get_activity();
            // Call Android permission request API
            // ActivityCompat.requestPermissions(activity, permissions, REQUEST_CODE);
        }
        */
    }
    
    fn request_location_permission(&self) {
        // Conceptual example - actual implementation would use JNI
        /*
        unsafe {
            let activity = get_activity();
            // Call Android permission request API
        }
        */
    }
    
    fn on_permission_result(&mut self, permission: &str, granted: bool) {
        match permission {
            "android.permission.CAMERA" => {
                self.camera_granted = granted;
                if granted {
                    // Enable camera functionality
                } else {
                    // Show error or disable camera functionality
                }
            }
            "android.permission.ACCESS_FINE_LOCATION" => {
                self.location_granted = granted;
                if granted {
                    // Enable location functionality
                } else {
                    // Show error or disable location functionality
                }
            }
            _ => {}
        }
    }
}
```

### Integrating with Android Services
Example of integrating with Android services:

```rust
// src/android/service_integration.rs
use makepad_android_state::{get_java_vm, get_activity};

struct AndroidServiceManager;

impl AndroidServiceManager {
    fn start_background_service(&self) {
        // Conceptual example - actual implementation would use JNI
        /*
        unsafe {
            let activity = get_activity();
            let java_vm = get_java_vm();
            
            // Get the context
            // let context = activity.getApplicationContext();
            
            // Create an intent for the service
            // let intent = Intent::new(context, BackgroundService::class);
            
            // Start the service
            // context.startService(intent);
        }
        */
    }
    
    fn stop_background_service(&self) {
        // Conceptual example - actual implementation would use JNI
        /*
        unsafe {
            let activity = get_activity();
            let java_vm = get_java_vm();
            
            // Get the context
            // let context = activity.getApplicationContext();
            
            // Create an intent for the service
            // let intent = Intent::new(context, BackgroundService::class);
            
            // Stop the service
            // context.stopService(intent);
        }
        */
    }
}
```

## WebAssembly Integration Examples

### Interacting with Web APIs
Example of interacting with browser APIs from WebAssembly:

```rust
// src/wasm/web_api_integration.rs
use wasm_bindgen::prelude::*;
use web_sys::{console, window, Document, Element, HtmlElement};
use js_sys::Promise;

#[wasm_bindgen]
extern "C" {
    #[wasm_bindgen(js_namespace = console)]
    fn log(s: &str);
    
    #[wasm_bindgen(js_namespace = navigator)]
    fn geolocation() -> web_sys::Geolocation;
}

#[wasm_bindgen]
pub struct WebApiIntegration {
    document: Document,
}

#[wasm_bindgen]
impl WebApiIntegration {
    #[wasm_bindgen(constructor)]
    pub fn new() -> Result<WebApiIntegration, JsValue> {
        let window = window().ok_or("No global window exists")?;
        let document = window.document().ok_or("Should have a document on window")?;
        
        Ok(WebApiIntegration { document })
    }
    
    pub fn get_element_by_id(&self, id: &str) -> Option<Element> {
        self.document.get_element_by_id(id)
    }
    
    pub fn update_element_text(&self, id: &str, text: &str) -> Result<(), JsValue> {
        let element = self.get_element_by_id(id)
            .ok_or("Element not found")?;
        let html_element: HtmlElement = element.dyn_into()?;
        html_element.set_inner_html(text);
        Ok(())
    }
    
    pub fn get_current_location(&self) -> Promise {
        // This is a conceptual example - actual implementation would be more complex
        // and would require proper error handling
        /*
        let geolocation = geolocation();
        let options = web_sys::PositionOptions::new();
        options.set_enable_high_accuracy(true);
        
        geolocation.get_current_position()
        */
        Promise::resolve(&JsValue::NULL)
    }
}
```

### Canvas Rendering Integration
Example of integrating with HTML5 Canvas:

```rust
// src/wasm/canvas_integration.rs
use wasm_bindgen::prelude::*;
use web_sys::{CanvasRenderingContext2d, HtmlCanvasElement, ImageData};

#[wasm_bindgen]
pub struct CanvasRenderer {
    context: CanvasRenderingContext2d,
}

#[wasm_bindgen]
impl CanvasRenderer {
    #[wasm_bindgen(constructor)]
    pub fn new(canvas: &HtmlCanvasElement) -> Result<CanvasRenderer, JsValue> {
        let context = canvas
            .get_context("2d")?
            .unwrap()
            .dyn_into::<CanvasRenderingContext2d>()?;
        
        Ok(CanvasRenderer { context })
    }
    
    pub fn draw_rectangle(&self, x: f64, y: f64, width: f64, height: f64) -> Result<(), JsValue> {
        self.context.begin_path();
        self.context.rect(x, y, width, height);
        self.context.set_fill_style(&"blue".into());
        self.context.fill();
        Ok(())
    }
    
    pub fn draw_circle(&self, x: f64, y: f64, radius: f64) -> Result<(), JsValue> {
        self.context.begin_path();
        self.context.arc(x, y, radius, 0.0, 2.0 * std::f64::consts::PI)?;
        self.context.set_fill_style(&"red".into());
        self.context.fill();
        Ok(())
    }
    
    pub fn draw_image_data(&self, image_data: &ImageData, x: f64, y: f64) -> Result<(), JsValue> {
        self.context.put_image_data(image_data, x, y)
    }
}
```

### Web Storage Integration
Example of using browser storage APIs:

```rust
// src/wasm/storage_integration.rs
use wasm_bindgen::prelude::*;

#[wasm_bindgen]
extern "C" {
    #[wasm_bindgen(js_namespace = localStorage)]
    fn setItem(key: &str, value: &str);
    
    #[wasm_bindgen(js_namespace = localStorage)]
    fn getItem(key: &str) -> Option<String>;
    
    #[wasm_bindgen(js_namespace = localStorage)]
    fn removeItem(key: &str);
    
    #[wasm_bindgen(js_namespace = sessionStorage)]
    fn setItem_session(key: &str, value: &str);
    
    #[wasm_bindgen(js_namespace = sessionStorage)]
    fn getItem_session(key: &str) -> Option<String>;
}

#[wasm_bindgen]
pub struct StorageManager;

#[wasm_bindgen]
impl StorageManager {
    pub fn save_to_local_storage(key: &str, value: &str) {
        setItem(key, value);
    }
    
    pub fn load_from_local_storage(key: &str) -> Option<String> {
        getItem(key)
    }
    
    pub fn remove_from_local_storage(key: &str) {
        removeItem(key);
    }
    
    pub fn save_to_session_storage(key: &str, value: &str) {
        setItem_session(key, value);
    }
    
    pub fn load_from_session_storage(key: &str) -> Option<String> {
        getItem_session(key)
    }
}
```

## Cross-Platform Examples

### Unified File System Access
Example of creating a unified interface for file system access:

```rust
// src/platform/file_system.rs
use std::fs;

#[cfg(not(target_arch = "wasm32"))]
pub struct FileSystem;

#[cfg(not(target_arch = "wasm32"))]
impl FileSystem {
    pub fn save_file(path: &str, data: &[u8]) -> Result<(), std::io::Error> {
        fs::write(path, data)
    }
    
    pub fn load_file(path: &str) -> Result<Vec<u8>, std::io::Error> {
        fs::read(path)
    }
    
    pub fn file_exists(path: &str) -> bool {
        std::path::Path::new(path).exists()
    }
}

#[cfg(target_arch = "wasm32")]
pub struct FileSystem;

#[cfg(target_arch = "wasm32")]
impl FileSystem {
    pub fn save_file(path: &str, data: &[u8]) -> Result<(), Box<dyn std::error::Error>> {
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
    
    pub fn load_file(path: &str) -> Result<Vec<u8>, Box<dyn std::error::Error>> {
        use wasm_bindgen::prelude::*;
        
        #[wasm_bindgen]
        extern "C" {
            #[wasm_bindgen(js_namespace = localStorage)]
            fn getItem(key: &str) -> Option<String>;
        }
        
        if let Some(data_str) = getItem(path) {
            let data = data_str.into_bytes();
            Ok(data)
        } else {
            Err("File not found".into())
        }
    }
    
    pub fn file_exists(path: &str) -> bool {
        use wasm_bindgen::prelude::*;
        
        #[wasm_bindgen]
        extern "C" {
            #[wasm_bindgen(js_namespace = localStorage)]
            fn getItem(key: &str) -> Option<String>;
        }
        
        getItem(path).is_some()
    }
}
```

### Platform-Specific UI Components
Example of creating platform-specific UI components:

```rust
// src/platform/ui_components.rs
use makepad_draw::*;

#[cfg(target_os = "android")]
pub struct PlatformButton {
    draw_bg: DrawQuad,
    draw_text: DrawText,
}

#[cfg(target_os = "android")]
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

#[cfg(target_arch = "wasm32")]
pub struct PlatformButton {
    draw_bg: DrawQuad,
    draw_text: DrawText,
}

#[cfg(target_arch = "wasm32")]
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

#[cfg(not(any(target_os = "android", target_arch = "wasm32")))]
pub struct PlatformButton {
    draw_bg: DrawQuad,
    draw_text: DrawText,
}

#[cfg(not(any(target_os = "android", target_arch = "wasm32")))]
impl PlatformButton {
    pub fn new() -> Self {
        Self {
            draw_bg: DrawQuad::new(),
            draw_text: DrawText::new(),
        }
    }
    
    pub fn draw(&mut self, cx: &mut Cx2d, label: &str) {
        // Desktop-style button
        self.draw_bg.color = vec4(0.8, 0.8, 0.8, 1.0); // Gray
        self.draw_bg.draw_quad(cx, &Walk::default(), &Layout::default());
        
        self.draw_text.text = label.to_string();
        self.draw_text.color = vec4(0.0, 0.0, 0.0, 1.0); // Black text
        self.draw_text.draw_text(cx, label);
    }
}
```

### Platform Detection and Feature Flags
Example of platform detection and conditional compilation:

```rust
// src/platform/detection.rs
pub struct PlatformInfo {
    pub name: String,
    pub version: String,
    pub is_mobile: bool,
    pub is_web: bool,
}

pub fn get_platform_info() -> PlatformInfo {
    PlatformInfo {
        #[cfg(target_os = "android")]
        name: "Android".to_string(),
        #[cfg(target_arch = "wasm32")]
        name: "Web".to_string(),
        #[cfg(target_os = "windows")]
        name: "Windows".to_string(),
        #[cfg(target_os = "macos")]
        name: "macOS".to_string(),
        #[cfg(target_os = "linux")]
        name: "Linux".to_string(),
        #[cfg(not(any(target_os = "android", target_arch = "wasm32", target_os = "windows", target_os = "macos", target_os = "linux")))]
        name: "Unknown".to_string(),
        
        version: env!("CARGO_PKG_VERSION").to_string(),
        
        #[cfg(target_os = "android")]
        is_mobile: true,
        #[cfg(not(target_os = "android"))]
        is_mobile: false,
        
        #[cfg(target_arch = "wasm32")]
        is_web: true,
        #[cfg(not(target_arch = "wasm32"))]
        is_web: false,
    }
}

// Conditional feature implementation
#[cfg(target_os = "android")]
mod android_features {
    pub fn platform_specific_feature() {
        // Android-specific implementation
    }
}

#[cfg(target_arch = "wasm32")]
mod web_features {
    pub fn platform_specific_feature() {
        // Web-specific implementation
    }
}

#[cfg(not(any(target_os = "android", target_arch = "wasm32")))]
mod desktop_features {
    pub fn platform_specific_feature() {
        // Desktop-specific implementation
    }
}

pub fn use_platform_feature() {
    #[cfg(target_os = "android")]
    android_features::platform_specific_feature();
    
    #[cfg(target_arch = "wasm32")]
    web_features::platform_specific_feature();
    
    #[cfg(not(any(target_os = "android", target_arch = "wasm32")))]
    desktop_features::platform_specific_feature();
}
```

## Best Practices for Platform Integration

### Error Handling
1. **Graceful Degradation**: Handle platform-specific errors gracefully
2. **Feature Detection**: Check for feature availability before using platform-specific APIs
3. **Fallback Implementations**: Provide fallback implementations for unsupported features

### Security Considerations
1. **Permission Management**: Properly request and handle permissions
2. **Data Validation**: Validate data received from platform APIs
3. **Secure Communication**: Use secure protocols for network communication

### Performance Optimization
1. **Minimize Platform Calls**: Batch operations and minimize calls between Rust and platform APIs
2. **Asynchronous Operations**: Use async operations to avoid blocking the main thread
3. **Resource Management**: Properly manage platform resources and clean up when done

### Testing and Debugging
1. **Platform-Specific Testing**: Test on all target platforms
2. **Logging**: Implement comprehensive logging for debugging
3. **Error Reporting**: Provide clear error messages and reporting mechanisms

By following these examples and best practices, you can effectively integrate platform-specific features into your Makepad applications while maintaining a consistent codebase across platforms.