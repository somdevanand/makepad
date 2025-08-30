# Building User Interfaces with Makepad

## Overview
This tutorial will guide you through building a user interface with Makepad. You'll learn how to create interactive components, handle user input, and structure your UI effectively.

## Prerequisites
Before starting this tutorial, you should:
1. Have completed the Getting Started guide
2. Be familiar with Rust basics
3. Understand the basic concepts of Makepad's drawing system

## Creating a Simple Counter Application

### Project Setup
Let's create a simple counter application that demonstrates UI building concepts:

```rust
// src/main.rs
use makepad_draw::*;
use makepad_live_id::*;

fn main() {
    let mut app = CounterApp::new();
    app.run();
}

struct CounterApp {
    window: Window,
    pass: Pass,
    view: View,
    counter: i32,
    increment_button: Button,
    decrement_button: Button,
    reset_button: Button,
    counter_display: DrawText,
}

impl CounterApp {
    fn new() -> Self {
        Self {
            window: Window::new(),
            pass: Pass::new(),
            view: View::new(),
            counter: 0,
            increment_button: Button::new("Increment"),
            decrement_button: Button::new("Decrement"),
            reset_button: Button::new("Reset"),
            counter_display: DrawText::new(),
        }
    }
    
    fn handle(&mut self, event: Event) {
        match event {
            Event::Draw(event) => {
                let mut cx = Cx2d::new(&mut self.window, &event);
                self.draw(&mut cx);
            }
            Event::MouseDown(event) => {
                // Handle button clicks
                if self.increment_button.was_clicked(&event) {
                    self.counter += 1;
                } else if self.decrement_button.was_clicked(&event) {
                    self.counter -= 1;
                } else if self.reset_button.was_clicked(&event) {
                    self.counter = 0;
                }
            }
            _ => ()
        }
    
    fn draw(&mut self, cx: &mut Cx2d) {
        self.window.begin(cx);
        self.pass.begin(cx);
        self.view.begin(cx, Layout::default());
        
        // Display the counter value
        self.counter_display.text = format!("Count: {}", self.counter);
        self.counter_display.draw_text(cx, &self.counter_display.text);
        
        // Draw buttons
        self.increment_button.draw(cx);
        self.decrement_button.draw(cx);
        self.reset_button.draw(cx);
        
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

### Creating a Button Component
Let's create a reusable button component:

```rust
// src/button.rs
use makepad_draw::*;
use makepad_live_id::*;

pub struct Button {
    draw_bg: DrawQuad,
    draw_text: DrawText,
    label: String,
    rect: Rect,
}

impl Button {
    pub fn new(label: &str) -> Self {
        Self {
            draw_bg: DrawQuad::new(),
            draw_text: DrawText::new(),
            label: label.to_string(),
            rect: Rect::default(),
        }
    }
    
    pub fn draw(&mut self, cx: &mut Cx2d) {
        // Draw button background
        self.draw_bg.color = vec4(0.2, 0.6, 1.0, 1.0); // Blue background
        self.rect = self.draw_bg.draw_quad(cx, &Walk::default(), &Layout::default());
        
        // Draw button text
        self.draw_text.text = self.label.clone();
        self.draw_text.color = vec4(1.0, 1.0, 1.0, 1.0); // White text
        self.draw_text.draw_text(cx, &self.label);
    }
    
    pub fn was_clicked(&self, event: &MouseDownEvent) -> bool {
        self.rect.contains(event.pos)
    }
}
```

## Layout and Styling

### Understanding Layout
Makepad's layout system is flexible and powerful. Let's explore different layout options:

```rust
use makepad_draw::*;

// Vertical layout
let vertical_layout = Layout {
    flow: Flow::Down,
    spacing: 10.0,
    ..Default::default()
};

// Horizontal layout
let horizontal_layout = Layout {
    flow: Flow::Right,
    spacing: 10.0,
    ..Default::default()
};

// Grid-like layout
let grid_layout = Layout {
    flow: Flow::Down,
    wrap: Wrap::Right, // Wrap to next row when needed
    spacing: 10.0,
    ..Default::default()
};
```

### Styling Components
You can style components by modifying their properties:

```rust
// Styling a button
button.draw_bg.color = vec4(0.8, 0.2, 0.2, 1.0); // Red background
button.draw_bg.border_color = vec4(0.0, 0.0, 0.0, 1.0); // Black border
button.draw_bg.border_width = 2.0;
button.draw_text.color = vec4(1.0, 1.0, 1.0, 1.0); // White text
button.draw_text.font_size = 16.0;
```

## Handling User Input

### Mouse Events
Makepad provides various mouse event handlers:

```rust
fn handle_mouse_event(&mut self, event: Event) {
    match event {
        Event::MouseDown(event) => {
            // Handle mouse down
        }
        Event::MouseUp(event) => {
            // Handle mouse up
        }
        Event::MouseMove(event) => {
            // Handle mouse move
        }
        Event::MouseScroll(event) => {
            // Handle mouse scroll
        }
        _ => ()
    }
}
```

### Keyboard Events
Keyboard input can be handled as follows:

```rust
fn handle_keyboard_event(&mut self, event: Event) {
    match event {
        Event::KeyDown(event) => {
            match event.key_code {
                KeyCode::ArrowUp => {
                    // Handle up arrow
                }
                KeyCode::ArrowDown => {
                    // Handle down arrow
                }
                KeyCode::Enter => {
                    // Handle enter key
                }
                _ => ()
            }
        }
        Event::KeyUp(event) => {
            // Handle key up
        }
        _ => ()
    }
}
```

## Creating a Form Application

### Project Structure
Let's create a more complex application with a form:

```rust
// src/form_app.rs
use makepad_draw::*;
use makepad_live_id::*;

struct FormApp {
    window: Window,
    pass: Pass,
    view: View,
    name_input: TextInput,
    email_input: TextInput,
    submit_button: Button,
    message_display: DrawText,
}

struct TextInput {
    draw_bg: DrawQuad,
    draw_text: DrawText,
    value: String,
    rect: Rect,
    is_focused: bool,
}

impl TextInput {
    fn new() -> Self {
        Self {
            draw_bg: DrawQuad::new(),
            draw_text: DrawText::new(),
            value: String::new(),
            rect: Rect::default(),
            is_focused: false,
        }
    }
    
    fn draw(&mut self, cx: &mut Cx2d) {
        // Draw background
        self.draw_bg.color = if self.is_focused {
            vec4(1.0, 1.0, 0.8, 1.0) // Light yellow when focused
        } else {
            vec4(1.0, 1.0, 1.0, 1.0) // White when not focused
        };
        self.rect = self.draw_bg.draw_quad(cx, &Walk::default(), &Layout::default());
        
        // Draw text
        self.draw_text.text = self.value.clone();
        self.draw_text.draw_text(cx, &self.value);
    }
    
    fn handle_click(&mut self, pos: Vec2) -> bool {
        if self.rect.contains(pos) {
            self.is_focused = true;
            true
        } else {
            self.is_focused = false;
            false
        }
    }
    
    fn handle_key(&mut self, key: &str) {
        if self.is_focused {
            self.value.push_str(key);
        }
    }
}
```

### Handling Form Events
```rust
impl FormApp {
    fn handle(&mut self, event: Event) {
        match event {
            Event::Draw(event) => {
                let mut cx = Cx2d::new(&mut self.window, &event);
                self.draw(&mut cx);
            }
            Event::MouseDown(event) => {
                // Handle input focus
                self.name_input.handle_click(event.pos);
                self.email_input.handle_click(event.pos);
                
                // Handle submit button
                if self.submit_button.was_clicked(&event) {
                    self.submit_form();
                }
            }
            Event::TextInput(event) => {
                // Handle text input
                self.name_input.handle_key(&event.input);
                self.email_input.handle_key(&event.input);
            }
            _ => ()
        }
    
    fn submit_form(&mut self) {
        let name = &self.name_input.value;
        let email = &self.email_input.value;
        
        if name.is_empty() || email.is_empty() {
            self.message_display.text = "Please fill in all fields".to_string();
        } else {
            self.message_display.text = format!("Hello, {}! We'll contact you at {}.", name, email);
        }
    }
}
```

## Best Practices

### Component Design
1. **Keep components focused**: Each component should have a single responsibility
2. **Use composition**: Build complex UIs by composing simpler components
3. **Encapsulate state**: Keep component state internal and expose only necessary methods
4. **Handle events properly**: Ensure components respond appropriately to user input

### Performance Considerations
1. **Minimize redraws**: Only redraw components that have changed
2. **Reuse draw objects**: Reuse `DrawQuad`, `DrawText`, etc. instances when possible
3. **Optimize layouts**: Use efficient layout configurations
4. **Batch operations**: Group similar operations together

### Code Organization
1. **Separate concerns**: Keep UI logic separate from business logic
2. **Use modules**: Organize code into logical modules
3. **Follow naming conventions**: Use clear, descriptive names for components and functions
4. **Document your code**: Add comments to explain complex logic

## Advanced Topics

### Custom Widgets
You can create custom widgets by combining existing drawing primitives:

```rust
pub struct ProgressBar {
    draw_bg: DrawQuad,
    draw_fill: DrawQuad,
    progress: f32, // 0.0 to 1.0
}

impl ProgressBar {
    pub fn new() -> Self {
        Self {
            draw_bg: DrawQuad::new(),
            draw_fill: DrawQuad::new(),
            progress: 0.0,
        }
    }
    
    pub fn set_progress(&mut self, progress: f32) {
        self.progress = progress.clamp(0.0, 1.0);
    }
    
    pub fn draw(&mut self, cx: &mut Cx2d) {
        // Draw background
        self.draw_bg.color = vec4(0.8, 0.8, 0.8, 1.0);
        let bg_rect = self.draw_bg.draw_quad(cx, &Walk::default(), &Layout::default());
        
        // Draw fill
        self.draw_fill.color = vec4(0.2, 0.8, 0.2, 1.0);
        let fill_width = bg_rect.size.x * self.progress;
        let fill_layout = Layout {
            walk: Walk {
                abs_pos: Some(bg_rect.pos),
                width: Size::Fixed(fill_width),
                height: Size::Fill,
                margin: Margin::default(),
            },
            ..Default::default()
        };
        self.draw_fill.draw_quad(cx, &Walk::default(), &fill_layout);
    }
}
```

## Conclusion
You've now learned how to build user interfaces with Makepad, including creating custom components, handling user input, and organizing your code effectively. Continue exploring the documentation to learn about more advanced features and techniques.