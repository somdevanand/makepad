# Common UI Patterns Examples

## Overview
This document provides examples of common UI patterns implemented with Makepad. These examples demonstrate best practices for creating reusable, efficient, and maintainable UI components.

## Button Component

### Basic Button
A simple, reusable button component:

```rust
use makepad_draw::*;
use makepad_live_id::*;

pub struct Button {
    draw_bg: DrawQuad,
    draw_text: DrawText,
    label: String,
    rect: Rect,
    hover: bool,
    pressed: bool,
}

impl Button {
    pub fn new(label: &str) -> Self {
        Self {
            draw_bg: DrawQuad::new(),
            draw_text: DrawText::new(),
            label: label.to_string(),
            rect: Rect::default(),
            hover: false,
            pressed: false,
        }
    }
    
    pub fn draw(&mut self, cx: &mut Cx2d) {
        // Determine button color based on state
        let bg_color = if self.pressed {
            vec4(0.1, 0.4, 0.8, 1.0) // Darker when pressed
        } else if self.hover {
            vec4(0.3, 0.7, 1.0, 1.0) // Lighter when hovered
        } else {
            vec4(0.2, 0.6, 1.0, 1.0) // Normal state
        };
        
        self.draw_bg.color = bg_color;
        self.rect = self.draw_bg.draw_quad(cx, &Walk::default(), &Layout::default());
        
        // Draw button text
        self.draw_text.text = self.label.clone();
        self.draw_text.color = vec4(1.0, 1.0, 1.0, 1.0); // White text
        self.draw_text.draw_text(cx, &self.label);
    }
    
    pub fn handle_mouse_down(&mut self, pos: Vec2) -> bool {
        if self.rect.contains(pos) {
            self.pressed = true;
            true
        } else {
            false
        }
    }
    
    pub fn handle_mouse_up(&mut self, pos: Vec2) -> bool {
        let was_pressed = self.pressed;
        self.pressed = false;
        
        if was_pressed && self.rect.contains(pos) {
            true // Button was clicked
        } else {
            false
        }
    }
    
    pub fn handle_mouse_move(&mut self, pos: Vec2) {
        self.hover = self.rect.contains(pos);
        if !self.hover {
            self.pressed = false;
        }
    }
}
```

## Input Field Component

### Text Input
A text input field with focus management:

```rust
use makepad_draw::*;
use makepad_live_id::*;

pub struct TextInput {
    draw_bg: DrawQuad,
    draw_text: DrawText,
    value: String,
    rect: Rect,
    is_focused: bool,
    cursor_pos: usize,
}

impl TextInput {
    pub fn new() -> Self {
        Self {
            draw_bg: DrawQuad::new(),
            draw_text: DrawText::new(),
            value: String::new(),
            rect: Rect::default(),
            is_focused: false,
            cursor_pos: 0,
        }
    }
    
    pub fn draw(&mut self, cx: &mut Cx2d) {
        // Draw background
        self.draw_bg.color = if self.is_focused {
            vec4(1.0, 1.0, 0.8, 1.0) // Light yellow when focused
        } else {
            vec4(1.0, 1.0, 1.0, 1.0) // White when not focused
        };
        self.rect = self.draw_bg.draw_quad(cx, &Walk::default(), &Layout::default());
        
        // Draw text
        self.draw_text.text = self.value.clone();
        self.draw_text.color = vec4(0.0, 0.0, 0.0, 1.0); // Black text
        self.draw_text.draw_text(cx, &self.value);
        
        // Draw cursor if focused
        if self.is_focused {
            self.draw_cursor(cx);
        }
    }
    
    fn draw_cursor(&mut self, cx: &mut Cx2d) {
        // Simplified cursor drawing
        let mut cursor_quad = DrawQuad::new();
        cursor_quad.color = vec4(0.0, 0.0, 0.0, 1.0); // Black cursor
        // Position cursor based on cursor_pos
        cursor_quad.draw_quad(cx, &Walk::default(), &Layout::default());
    }
    
    pub fn handle_click(&mut self, pos: Vec2) -> bool {
        if self.rect.contains(pos) {
            self.is_focused = true;
            true
        } else {
            self.is_focused = false;
            false
        }
    }
    
    pub fn handle_key(&mut self, input: &str) {
        if self.is_focused {
            self.value.push_str(input);
            self.cursor_pos = self.value.len();
        }
    }
    
    pub fn handle_backspace(&mut self) {
        if self.is_focused && self.cursor_pos > 0 {
            self.value.remove(self.cursor_pos - 1);
            self.cursor_pos -= 1;
        }
    }
    
    pub fn get_value(&self) -> &str {
        &self.value
    }
}
```

## List Component

### Scrollable List
A scrollable list component for displaying multiple items:

```rust
use makepad_draw::*;
use makepad_live_id::*;

pub struct ScrollableList {
    draw_bg: DrawQuad,
    items: Vec<String>,
    scroll_offset: f32,
    item_height: f32,
    visible_items: usize,
    total_height: f32,
    rect: Rect,
}

impl ScrollableList {
    pub fn new() -> Self {
        Self {
            draw_bg: DrawQuad::new(),
            items: Vec::new(),
            scroll_offset: 0.0,
            item_height: 30.0,
            visible_items: 10,
            total_height: 0.0,
            rect: Rect::default(),
        }
    }
    
    pub fn add_item(&mut self, item: String) {
        self.items.push(item);
        self.total_height = self.items.len() as f32 * self.item_height;
    }
    
    pub fn draw(&mut self, cx: &mut Cx2d) {
        // Draw background
        self.draw_bg.color = vec4(0.9, 0.9, 0.9, 1.0);
        self.rect = self.draw_bg.draw_quad(cx, &Walk::default(), &Layout::default());
        
        // Calculate visible range
        let start_index = (self.scroll_offset / self.item_height) as usize;
        let end_index = (start_index + self.visible_items).min(self.items.len());
        
        // Draw visible items
        let mut draw_text = DrawText::new();
        for (i, item) in self.items[start_index..end_index].iter().enumerate() {
            let y_pos = (i as f32 * self.item_height) - (self.scroll_offset % self.item_height);
            
            draw_text.text = item.clone();
            draw_text.color = vec4(0.0, 0.0, 0.0, 1.0);
            // Position text at y_pos
            draw_text.draw_text(cx, item);
        }
    }
    
    pub fn handle_scroll(&mut self, delta: f32) {
        let max_scroll = (self.total_height - (self.visible_items as f32 * self.item_height)).max(0.0);
        self.scroll_offset = (self.scroll_offset + delta).clamp(0.0, max_scroll);
    }
}
```

## Modal Dialog Component

### Popup Dialog
A modal dialog component for displaying important information or requesting user input:

```rust
use makepad_draw::*;
use makepad_live_id::*;

pub struct ModalDialog {
    draw_overlay: DrawQuad,
    draw_bg: DrawQuad,
    draw_text: DrawText,
    title: String,
    message: String,
    is_open: bool,
    rect: Rect,
    ok_button: Button,
    cancel_button: Button,
}

impl ModalDialog {
    pub fn new(title: &str, message: &str) -> Self {
        Self {
            draw_overlay: DrawQuad::new(),
            draw_bg: DrawQuad::new(),
            draw_text: DrawText::new(),
            title: title.to_string(),
            message: message.to_string(),
            is_open: false,
            rect: Rect::default(),
            ok_button: Button::new("OK"),
            cancel_button: Button::new("Cancel"),
        }
    }
    
    pub fn open(&mut self) {
        self.is_open = true;
    }
    
    pub fn close(&mut self) {
        self.is_open = false;
    }
    
    pub fn is_open(&self) -> bool {
        self.is_open
    }
    
    pub fn draw(&mut self, cx: &mut Cx2d) {
        if !self.is_open {
            return;
        }
        
        // Draw semi-transparent overlay
        self.draw_overlay.color = vec4(0.0, 0.0, 0.0, 0.5);
        self.draw_overlay.draw_quad(cx, &Walk::default(), &Layout::default());
        
        // Draw dialog background
        self.draw_bg.color = vec4(1.0, 1.0, 1.0, 1.0);
        self.rect = self.draw_bg.draw_quad(cx, &Walk::default(), &Layout::default());
        
        // Draw title
        let mut title_text = DrawText::new();
        title_text.text = self.title.clone();
        title_text.color = vec4(0.0, 0.0, 0.0, 1.0);
        title_text.font_size = 18.0;
        title_text.draw_text(cx, &self.title);
        
        // Draw message
        self.draw_text.text = self.message.clone();
        self.draw_text.color = vec4(0.0, 0.0, 0.0, 1.0);
        self.draw_text.draw_text(cx, &self.message);
        
        // Draw buttons
        self.ok_button.draw(cx);
        self.cancel_button.draw(cx);
    }
    
    pub fn handle_mouse_down(&mut self, pos: Vec2) -> bool {
        if !self.is_open {
            return false;
        }
        
        if self.ok_button.handle_mouse_down(pos) || self.cancel_button.handle_mouse_down(pos) {
            return true;
        }
        
        // Check if click is outside dialog (close dialog)
        if !self.rect.contains(pos) {
            self.close();
            return true;
        }
        
        false
    }
    
    pub fn handle_mouse_up(&mut self, pos: Vec2) -> bool {
        if !self.is_open {
            return false;
        }
        
        if self.ok_button.handle_mouse_up(pos) {
            self.close();
            return true;
        }
        
        if self.cancel_button.handle_mouse_up(pos) {
            self.close();
            return true;
        }
        
        false
    }
}
```

## Navigation Component

### Tab Bar
A tab bar component for switching between different views:

```rust
use makepad_draw::*;
use makepad_live_id::*;

pub struct TabBar {
    tabs: Vec<Tab>,
    active_tab: usize,
    rect: Rect,
}

pub struct Tab {
    draw_bg: DrawQuad,
    draw_text: DrawText,
    label: String,
    rect: Rect,
    is_active: bool,
}

impl Tab {
    pub fn new(label: &str) -> Self {
        Self {
            draw_bg: DrawQuad::new(),
            draw_text: DrawText::new(),
            label: label.to_string(),
            rect: Rect::default(),
            is_active: false,
        }
    }
    
    pub fn draw(&mut self, cx: &mut Cx2d) {
        // Draw tab background
        self.draw_bg.color = if self.is_active {
            vec4(0.9, 0.9, 1.0, 1.0) // Light blue for active tab
        } else {
            vec4(0.8, 0.8, 0.8, 1.0) // Gray for inactive tabs
        };
        self.rect = self.draw_bg.draw_quad(cx, &Walk::default(), &Layout::default());
        
        // Draw tab label
        self.draw_text.text = self.label.clone();
        self.draw_text.color = vec4(0.0, 0.0, 0.0, 1.0);
        self.draw_text.draw_text(cx, &self.label);
    }
    
    pub fn handle_click(&mut self, pos: Vec2) -> bool {
        if self.rect.contains(pos) {
            true
        } else {
            false
        }
    }
}

impl TabBar {
    pub fn new() -> Self {
        Self {
            tabs: Vec::new(),
            active_tab: 0,
            rect: Rect::default(),
        }
    }
    
    pub fn add_tab(&mut self, label: &str) {
        self.tabs.push(Tab::new(label));
        if self.tabs.len() == 1 {
            self.tabs[0].is_active = true;
        }
    }
    
    pub fn draw(&mut self, cx: &mut Cx2d) {
        let mut layout = Layout {
            flow: Flow::Right,
            ..Default::default()
        };
        
        for (i, tab) in self.tabs.iter_mut().enumerate() {
            tab.is_active = i == self.active_tab;
            tab.draw(cx);
        }
    }
    
    pub fn handle_click(&mut self, pos: Vec2) -> Option<usize> {
        for (i, tab) in self.tabs.iter_mut().enumerate() {
            if tab.handle_click(pos) {
                self.active_tab = i;
                return Some(i);
            }
        }
        None
    }
    
    pub fn get_active_tab(&self) -> usize {
        self.active_tab
    }
}
```

## Best Practices for UI Components

### Component Design Principles
1. **Single Responsibility**: Each component should have one clear purpose
2. **Encapsulation**: Keep internal state private and expose only necessary methods
3. **Reusability**: Design components to be flexible and configurable
4. **Consistent API**: Use consistent naming and patterns across components
5. **Performance**: Minimize redraws and optimize rendering

### State Management
1. **Local State**: Keep component-specific state within the component
2. **Event Handling**: Provide clear methods for handling user interactions
3. **Lifecycle**: Implement proper initialization and cleanup
4. **Communication**: Use events or callbacks to communicate with parent components

### Styling and Customization
1. **Default Styles**: Provide sensible defaults for all visual properties
2. **Customization Points**: Allow customization of colors, sizes, and behaviors
3. **Theme Support**: Consider implementing theme support for consistent styling
4. **Responsive Design**: Design components to work well at different sizes

### Accessibility
1. **Keyboard Navigation**: Support keyboard navigation for all interactive components
2. **Screen Reader Support**: Provide appropriate labels and descriptions
3. **Focus Management**: Implement proper focus handling
4. **Contrast Ratios**: Ensure sufficient contrast for text and interactive elements

By following these patterns and examples, you can create robust, maintainable UI components that provide a great user experience in your Makepad applications.