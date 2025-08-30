# Platform-Specific Libraries API Reference

## Android Integration

### Structs

#### `JavaVM`
Java Virtual Machine instance.

```rust
pub struct JavaVM { /* fields omitted */ }
```

#### `Activity`
Android Activity instance.

```rust
pub struct Activity { /* fields omitted */ }
```

### Functions

#### `get_java_vm() -> *mut JavaVM`
Returns a pointer to the JavaVM instance.

```rust
pub fn get_java_vm() -> *mut JavaVM
```

#### `get_activity() -> *mut Activity`
Returns a pointer to the current Makepad Activity instance.

```rust
pub fn get_activity() -> *mut Activity
```

### Enums

#### `AndroidStateError`
Error types for Android state operations.

```rust
#[derive(Debug)]
pub enum AndroidStateError {
    NotInitialized,
    InvalidState,
}
```

## WebAssembly Support

### Structs

#### `WebComponent`
A web-based UI component.

```rust
#[wasm_bindgen]
pub struct WebComponent { /* fields omitted */ }
```

##### Methods

###### `WebComponent::new(id: &str) -> WebComponent`
Creates a new WebComponent.

```rust
#[wasm_bindgen(constructor)]
pub fn new(id: &str) -> WebComponent
```

###### `WebComponent::set_text(&self, text: &str)`
Sets the text content of the component.

```rust
#[wasm_bindgen]
pub fn set_text(&self, text: &str)
```

###### `WebComponent::append_to_body(&self)`
Appends the component to the document body.

```rust
#[wasm_bindgen]
pub fn append_to_body(&self)
```

### Functions

#### `greet(name: &str) -> String`
Greets a user with a message.

```rust
#[wasm_bindgen]
pub fn greet(name: &str) -> String
```

#### `process_data(data: &[u8]) -> Vec<u8>`
Processes binary data.

```rust
#[wasm_bindgen]
pub fn process_data(data: &[u8]) -> Vec<u8>
```

#### `setup_event_listener() -> Result<(), JsValue>`
Sets up an event listener for a button.

```rust
#[wasm_bindgen]
pub fn setup_event_listener() -> Result<(), JsValue>
```

#### `draw_to_canvas(canvas: &HtmlCanvasElement) -> Result<(), JsValue>`
Draws to an HTML5 Canvas element.

```rust
#[wasm_bindgen]
pub fn draw_to_canvas(canvas: &HtmlCanvasElement) -> Result<(), JsValue>
```

#### `fetch_data(url: &str) -> Promise`
Fetches data from a URL.

```rust
#[wasm_bindgen]
pub fn fetch_data(url: &str) -> Promise
```

### Traits

#### `WasmBindgen`
Trait for WebAssembly bindings.

```rust
pub trait WasmBindgen {
    fn to_js_value(&self) -> JsValue;
    fn from_js_value(value: &JsValue) -> Result<Self, JsValue> where Self: Sized;
}
```

### Modules

#### `web_sys`
Bindings to web APIs.

##### Structs

###### `Document`
HTML document object.

```rust
pub struct Document { /* fields omitted */ }
```

###### `Element`
HTML element object.

```rust
pub struct Element { /* fields omitted */ }
```

###### `HtmlElement`
HTML element object with additional properties.

```rust
pub struct HtmlElement { /* fields omitted */ }
```

###### `CanvasRenderingContext2d`
2D rendering context for canvas elements.

```rust
pub struct CanvasRenderingContext2d { /* fields omitted */ }
```

###### `WebSocket`
WebSocket connection object.

```rust
pub struct WebSocket { /* fields omitted */ }
```

##### Functions

###### `window() -> Option<Window>`
Returns the global window object.

```rust
pub fn window() -> Option<Window>
```

###### `document() -> Option<Document>`
Returns the global document object.

```rust
pub fn document() -> Option<Document>
```

###### `get_element_by_id(id: &str) -> Option<Element>`
Gets an element by its ID.

```rust
pub fn get_element_by_id(id: &str) -> Option<Element>
```

#### `js_sys`
Bindings to JavaScript built-in objects.

##### Structs

###### `Uint8Array`
JavaScript Uint8Array object.

```rust
pub struct Uint8Array { /* fields omitted */ }
```

###### `Promise`
JavaScript Promise object.

```rust
pub struct Promise { /* fields omitted */ }
```

##### Functions

###### `log(s: &str)`
Logs a message to the console.

```rust
pub fn log(s: &str)
```

###### `alert(s: &str)`
Shows an alert dialog.

```rust
pub fn alert(s: &str)
```

### Constants

#### `LOCAL_STORAGE_KEY`
Key for local storage operations.

```rust
pub const LOCAL_STORAGE_KEY: &str = "makepad_data";
```

## Cross-Platform Abstractions

### Traits

#### `PlatformSpecific`
Trait for platform-specific functionality.

```rust
pub trait PlatformSpecific {
    fn initialize() -> Result<(), PlatformError>;
    fn cleanup();
    fn get_platform_info() -> PlatformInfo;
}
```

### Structs

#### `PlatformInfo`
Information about the current platform.

```rust
pub struct PlatformInfo {
    pub name: String,
    pub version: String,
    pub architecture: String,
}
```

#### `PlatformError`
Error type for platform-specific operations.

```rust
#[derive(Debug)]
pub enum PlatformError {
    InitializationFailed,
    UnsupportedFeature,
    ResourceUnavailable,
}
```

### Functions

#### `get_current_platform() -> PlatformInfo`
Returns information about the current platform.

```rust
pub fn get_current_platform() -> PlatformInfo
```

#### `is_mobile() -> bool`
Checks if the current platform is a mobile device.

```rust
pub fn is_mobile() -> bool
```

#### `is_web() -> bool`
Checks if the current platform is a web browser.

```rust
pub fn is_web() -> bool
```

#### `is_desktop() -> bool`
Checks if the current platform is a desktop system.

```rust
pub fn is_desktop() -> bool
```

## File System Access

### Structs

#### `FileSystem`
Platform-agnostic file system access.

```rust
pub struct FileSystem { /* fields omitted */ }
```

##### Methods

###### `FileSystem::read_file(path: &str) -> Result<Vec<u8>, IOError>`
Reads a file from the file system.

```rust
pub fn read_file(path: &str) -> Result<Vec<u8>, IOError>
```

###### `FileSystem::write_file(path: &str, data: &[u8]) -> Result<(), IOError>`
Writes data to a file in the file system.

```rust
pub fn write_file(path: &str, data: &[u8]) -> Result<(), IOError>
```

###### `FileSystem::exists(path: &str) -> bool`
Checks if a file or directory exists.

```rust
pub fn exists(path: &str) -> bool
```

### Enums

#### `IOError`
Error types for I/O operations.

```rust
#[derive(Debug)]
pub enum IOError {
    NotFound,
    PermissionDenied,
    InvalidData,
    Other(String),
}
```

## Network Access

### Structs

#### `HttpClient`
Platform-agnostic HTTP client.

```rust
pub struct HttpClient { /* fields omitted */ }
```

##### Methods

###### `HttpClient::new() -> Self`
Creates a new HTTP client.

```rust
pub fn new() -> Self
```

###### `HttpClient::get(url: &str) -> Result<HttpResponse, NetworkError>`
Sends an HTTP GET request.

```rust
pub fn get(url: &str) -> Result<HttpResponse, NetworkError>
```

###### `HttpClient::post(url: &str, data: &[u8]) -> Result<HttpResponse, NetworkError>`
Sends an HTTP POST request.

```rust
pub fn post(url: &str, data: &[u8]) -> Result<HttpResponse, NetworkError>
```

### Structs

#### `HttpResponse`
HTTP response data.

```rust
pub struct HttpResponse {
    pub status_code: u16,
    pub headers: HashMap<String, String>,
    pub body: Vec<u8>,
}
```

### Enums

#### `NetworkError`
Error types for network operations.

```rust
#[derive(Debug)]
pub enum NetworkError {
    ConnectionFailed,
    Timeout,
    InvalidUrl,
    Other(String),
}