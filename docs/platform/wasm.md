# WebAssembly Support

## Overview
Makepad provides comprehensive support for WebAssembly (WASM), enabling the deployment of Makepad applications to web browsers. This documentation covers how to compile Makepad applications to WebAssembly, interact with JavaScript, and handle web-specific considerations.

## WASM Bridge Functionality

### Overview
The `wasm_bridge` library provides functionality for bridging Rust code with JavaScript in WebAssembly environments. It enables seamless communication between Rust and JavaScript, allowing Makepad applications to leverage web APIs and interact with the browser environment.

### Basic Concepts

#### Exporting Rust Functions to JavaScript
Functions can be exported from Rust to JavaScript using the `#[wasm_bindgen]` attribute:

```rust
use wasm_bindgen::prelude::*;

#[wasm_bindgen]
pub fn greet(name: &str) -> String {
    format!("Hello, {}!", name)
}
```

#### Importing JavaScript Functions to Rust
JavaScript functions can be imported to Rust using the `#[wasm_bindgen]` attribute:

```rust
use wasm_bindgen::prelude::*;

#[wasm_bindgen]
extern "C" {
    fn alert(s: &str);
    
    #[wasm_bindgen(js_namespace = console)]
    fn log(s: &str);
}
```

### Memory Management
WebAssembly has a linear memory model that requires careful management when passing data between Rust and JavaScript:

```rust
use wasm_bindgen::prelude::*;

#[wasm_bindgen]
pub fn process_data(data: &[u8]) -> Vec<u8> {
    // Process the data
    data.iter().map(|x| x * 2).collect()
}
```

## Web Integration Patterns

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

### Event Handling
Event handling in WebAssembly requires registering callbacks that can be invoked from JavaScript:

```rust
use wasm_bindgen::prelude::*;
use web_sys::Event;

#[wasm_bindgen]
pub fn setup_event_listener() -> Result<(), JsValue> {
    let window = web_sys::window().unwrap();
    let document = window.document().unwrap();
    let button = document.get_element_by_id("my-button").unwrap();
    
    let closure = Closure::wrap(Box::new(move |event: Event| {
        web_sys::console::log_1(&"Button clicked!".into());
    }) as Box<dyn FnMut(Event)>);
    
    button.add_event_listener_with_callback("click", closure.as_ref().unchecked_ref())?;
    closure.forget(); // Prevent the closure from being dropped
    
    Ok(())
}
```

### Canvas Rendering
Makepad's drawing system can be integrated with HTML5 Canvas for web rendering:

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

## Performance Considerations

### Memory Copying
Minimize memory copying between Rust and JavaScript by using appropriate data structures:

```rust
use wasm_bindgen::prelude::*;
use js_sys::Uint8Array;

#[wasm_bindgen]
pub fn process_large_buffer(buffer: &Uint8Array) -> Uint8Array {
    // Process buffer without unnecessary copying
    let data = buffer.to_vec();
    let processed: Vec<u8> = data.iter().map(|x| x.wrapping_mul(2)).collect();
    Uint8Array::from(processed.as_slice())
}
```

### Batch Operations
Batch operations to reduce the number of JavaScript calls:

```rust
use wasm_bindgen::prelude::*;

#[wasm_bindgen]
pub fn batch_update(elements: &[ElementData]) {
    for element in elements {
        // Update multiple elements in a single call
        update_element(element.id.as_str(), element.content.as_str());
    }
}

#[wasm_bindgen]
struct ElementData {
    id: String,
    content: String,
}
```

## Web-Specific Features

### Local Storage
Interact with browser local storage for persistent data:

```rust
use wasm_bindgen::prelude::*;

#[wasm_bindgen]
extern "C" {
    #[wasm_bindgen(js_namespace = localStorage)]
    fn setItem(key: &str, value: &str);
    
    #[wasm_bindgen(js_namespace = localStorage)]
    fn getItem(key: &str) -> String;
}

#[wasm_bindgen]
pub fn save_user_preference(key: &str, value: &str) {
    setItem(key, value);
}

#[wasm_bindgen]
pub fn load_user_preference(key: &str) -> String {
    getItem(key)
}
```

### Fetch API
Make HTTP requests using the browser's Fetch API:

```rust
use wasm_bindgen::prelude::*;
use wasm_bindgen_futures::JsFuture;
use web_sys::{Request, RequestInit, Response};

#[wasm_bindgen]
pub async fn fetch_data(url: &str) -> Result<String, JsValue> {
    let mut opts = RequestInit::new();
    opts.method("GET");
    
    let request = Request::new_with_str_and_init(url, &opts)?;
    
    let window = web_sys::window().unwrap();
    let resp_value = JsFuture::from(window.fetch_with_request(&request)).await?;
    let resp: Response = resp_value.dyn_into().unwrap();
    
    let text = JsFuture::from(resp.text()?).await?;
    Ok(text.as_string().unwrap())
}
```

### WebSockets
Establish WebSocket connections for real-time communication:

```rust
use wasm_bindgen::prelude::*;
use web_sys::WebSocket;

#[wasm_bindgen]
pub fn connect_websocket(url: &str) -> Result<WebSocket, JsValue> {
    let ws = WebSocket::new(url)?;
    
    // Set up event handlers
    // ws.set_onopen(Some(on_open.as_ref().unchecked_ref()));
    // ws.set_onmessage(Some(on_message.as_ref().unchecked_ref()));
    // ws.set_onclose(Some(on_close.as_ref().unchecked_ref()));
    // ws.set_onerror(Some(on_error.as_ref().unchecked_ref()));
    
    Ok(ws)
}
```

## Practical Examples

### Creating a Web-Based UI Component
```rust
use wasm_bindgen::prelude::*;
use web_sys::{Element, HtmlElement};

#[wasm_bindgen]
pub struct WebComponent {
    element: HtmlElement,
}

#[wasm_bindgen]
impl WebComponent {
    #[wasm_bindgen(constructor)]
    pub fn new(id: &str) -> WebComponent {
        let window = web_sys::window().unwrap();
        let document = window.document().unwrap();
        let element = document.create_element("div").unwrap();
        element.set_id(id);
        
        WebComponent {
            element: element.dyn_into().unwrap(),
        }
    }
    
    pub fn set_text(&self, text: &str) {
        self.element.set_inner_html(text);
    }
    
    pub fn append_to_body(&self) {
        let window = web_sys::window().unwrap();
        let document = window.document().unwrap();
        let body = document.body().unwrap();
        body.append_child(&self.element).unwrap();
    }
}
```

### Handling User Input
```rust
use wasm_bindgen::prelude::*;
use web_sys::Event;

#[wasm_bindgen]
pub fn setup_input_handler(input_id: &str) {
    let window = web_sys::window().unwrap();
    let document = window.document().unwrap();
    let input = document.get_element_by_id(input_id).unwrap();
    
    let closure = Closure::wrap(Box::new(move |event: Event| {
        // Handle input event
        web_sys::console::log_1(&"Input changed!".into());
    }) as Box<dyn FnMut(Event)>);
    
    input.add_event_listener_with_callback("input", closure.as_ref().unchecked_ref()).unwrap();
    closure.forget();
}
```

## Best Practices

1. **Minimize JavaScript Calls**: Batch operations and minimize the number of calls between Rust and JavaScript
2. **Handle Errors Gracefully**: Always handle potential JavaScript exceptions and errors
3. **Optimize Memory Usage**: Be mindful of memory allocation and deallocation in the WebAssembly environment
4. **Use Appropriate Data Types**: Choose data types that minimize conversion overhead between Rust and JavaScript
5. **Follow Web Standards**: Adhere to web development best practices and standards

## Related Components
- `wasm_bridge`: Core WebAssembly bridging functionality
- `wasm_bindgen`: Rust-to-JavaScript bindings
- `web_sys`: Bindings to web APIs
- `js_sys`: Bindings to JavaScript built-in objects and functions