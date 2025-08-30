# Getting Started with Makepad

## Overview
This guide will help you get started with Makepad, a Rust-based framework for building cross-platform applications with a focus on performance and ease of use. By the end of this guide, you'll have created your first Makepad application.

## Prerequisites
Before you begin, ensure you have the following installed:
- Rust (latest stable version)
- Cargo (Rust's package manager)
- Git

## Installation

### Installing Rust
If you don't have Rust installed, you can install it using rustup:

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

Follow the on-screen instructions to complete the installation.

### Creating a New Makepad Project
To create a new Makepad project, use Cargo:

```bash
cargo new my_makepad_app
cd my_makepad_app
```

Add Makepad as a dependency in your `Cargo.toml` file:

```toml
[dependencies]
makepad = "1.0.0"
```

## Your First Application

### Basic Application Structure
Create a simple application that displays "Hello, Makepad!" on the screen.

```rust
// src/main.rs
use makepad_draw::*;
use makepad_live_id::*;

fn main() {
    // Initialize the application
    let mut app = App::new();
    
    // Run the application
    app.run();
}

struct App {
    // Application state
}

impl App {
    fn new() -> Self {
        Self {
            // Initialize state
        }
    }
    
    fn run(&mut self) {
        // Main application loop
        loop {
            // Handle events
            // Draw UI
            // Update state
        }
    }
}
```

### Adding a Simple UI
Let's add a simple UI that displays text:

```rust
use makepad_draw::*;
use makepad_live_id::*;

fn main() {
    let mut app = App::new();
    app.run();
}

struct App {
    window: Window,
    pass: Pass,
    view: View,
    text: DrawText,
}

impl App {
    fn new() -> Self {
        Self {
            window: Window::new(),
            pass: Pass::new(),
            view: View::new(),
            text: DrawText::new(),
        }
    }
    
    fn handle(&mut self, event: Event) {
        match event {
            Event::Draw(event) => {
                let mut cx = Cx2d::new(&mut self.window, &event);
                self.draw(&mut cx);
            }
            _ => ()
        }
    }
    
    fn draw(&mut self, cx: &mut Cx2d) {
        self.window.begin(cx);
        self.pass.begin(cx);
        self.view.begin(cx, Layout::default());
        
        // Draw text
        self.text.draw_text(cx, "Hello, Makepad!");
        
        self.view.end(cx);
        self.pass.end(cx);
        self.window.end(cx);
    }
    
    fn run(&mut self) {
        while let Some(event) = self.window.recv_event() {
            self.handle(event);
        }
    }
}
```

## Core Concepts

### Live IDs
Makepad uses Live IDs for efficient identification of UI elements and resources:

```rust
use makepad_live_id::*;

// Create a Live ID from a string
let id = LiveId::from_str("my_component");

// Use the live_id! macro for convenience
let id = live_id!("my_component");
```

### Drawing System
Makepad's drawing system is based on immediate mode rendering:

```rust
use makepad_draw::*;

// Create drawing objects
let mut draw_quad = DrawQuad::new();
let mut draw_text = DrawText::new();

// Configure properties
draw_quad.color = vec4(1.0, 0.0, 0.0, 1.0); // Red color

// Draw in the render loop
draw_quad.draw_quad(cx, &Walk::default(), &Layout::default());
draw_text.draw_text(cx, "Hello, Makepad!");
```

### Layout System
Makepad uses a flexible layout system:

```rust
use makepad_draw::*;

let layout = Layout {
    flow: Flow::Down,           // Stack elements vertically
    align: Align::Center,       // Center align children
    padding: Padding {          // Add padding around content
        left: 10.0,
        top: 10.0,
        right: 10.0,
        bottom: 10.0,
    },
    ..Default::default()
};
```

## Building and Running

### Building the Application
To build your Makepad application, use Cargo:

```bash
cargo build
```

### Running the Application
To run your Makepad application:

```bash
cargo run
```

### Building for Different Platforms
Makepad supports multiple platforms. To build for a specific platform:

```bash
# Build for WebAssembly
cargo build --target wasm32-unknown-unknown

# Build for Android (requires Android NDK)
cargo build --target aarch64-linux-android
```

## Next Steps
Now that you've created your first Makepad application, you can explore more advanced topics:

1. **UI Components**: Learn how to create custom UI components
2. **State Management**: Understand how to manage application state
3. **Platform Integration**: Explore platform-specific features
4. **Performance Optimization**: Learn techniques for optimizing your applications

## Troubleshooting

### Common Issues
1. **Missing Dependencies**: Ensure all required dependencies are installed
2. **Compilation Errors**: Check that you're using a compatible Rust version
3. **Runtime Issues**: Verify that your system meets the requirements

### Getting Help
If you encounter issues, you can:
1. Check the API documentation
2. Visit the Makepad community forums
3. File an issue on the GitHub repository

## Conclusion
You've now created your first Makepad application and learned the basic concepts. Continue exploring the documentation to build more complex and feature-rich applications with Makepad.