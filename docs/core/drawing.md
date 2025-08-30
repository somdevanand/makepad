# Drawing System

## Overview
The Drawing system in Makepad provides a comprehensive 2D and 3D rendering framework for creating graphical user interfaces and visualizations. It includes functionality for rendering shapes, text, images, and custom shaders, with support for both 2D and 3D graphics.

## 2D Rendering (Cx2d)

### Basic Concepts
The 2D rendering system is centered around the `Cx2d` context, which provides methods for drawing various graphical elements.

### Drawing Quads
Quads are fundamental rectangular shapes that can be drawn with various styles and effects.

```rust
use makepad_draw::*;

// Begin drawing with a Cx2d context
fn draw_example(cx: &mut Cx2d) {
    // Create a DrawQuad instance
    let mut draw_quad = DrawQuad::new();
    
    // Configure the quad's properties
    draw_quad.draw_quad(
        cx,
        &Walk::default(), // Layout instructions
        &Layout::default() // Positioning
    );
}
```

### Drawing Text
Text rendering is handled through the `DrawText` component.

```rust
use makepad_draw::*;

fn draw_text_example(cx: &mut Cx2d) {
    let mut draw_text = DrawText::new();
    
    // Configure text properties
    draw_text.text = "Hello, Makepad!".to_string();
    draw_text.color = vec4(1.0, 1.0, 1.0, 1.0); // White color
    
    // Draw the text
    draw_text.draw_text(cx, "Hello, Makepad!");
}
```

### Drawing Icons
Icons can be rendered using the `DrawIcon` component.

```rust
use makepad_draw::*;

fn draw_icon_example(cx: &mut Cx2d) {
    let mut draw_icon = DrawIcon::new();
    
    // Configure icon properties
    draw_icon.icon = live_id!("my_icon");
    
    // Draw the icon
    draw_icon.draw_icon(cx, Some(live_id!("my_icon")));
}
```

### Drawing Lines
Lines can be drawn using the `DrawLine` component.

```rust
use makepad_draw::*;

fn draw_line_example(cx: &mut Cx2d) {
    let mut draw_line = DrawLine::new();
    
    // Configure line properties
    draw_line.color = vec4(1.0, 0.0, 0.0, 1.0); // Red color
    draw_line.line_width = 2.0;
    
    // Draw the line
    draw_line.draw_line(cx, vec2(0.0, 0.0), vec2(100.0, 100.0));
}
```

## 3D Rendering (Cx3d)

### Basic 3D Concepts
The 3D rendering system builds upon the 2D system but adds depth and perspective capabilities.

### 3D Drawing Context
```rust
use makepad_draw::*;

fn draw_3d_example(cx: &mut Cx3d) {
    // 3D drawing operations would go here
    // This typically involves setting up cameras, lights, and 3D geometry
}
```

## Layout System

### Walk and Layout
The layout system uses `Walk` and `Layout` structures to control positioning and sizing of elements.

```rust
use makepad_draw::*;

let walk = Walk {
    abs_pos: Option(vec2(10.0, 10.0)), // Absolute position
    margin: Margin::default(),         // Margins around the element
    width: Size::Fixed(100.0),         // Fixed width
    height: Size::Fixed(50.0),         // Fixed height
};

let layout = Layout {
    flow: Flow::Right,                 // Layout flow direction
    align: Align::Center,              // Alignment
    padding: Padding::default(),       // Padding inside the element
    ..Default::default()
};
```

### Size Specifications
```rust
use makepad_draw::*;

// Fixed size
let fixed_size = Size::Fixed(100.0);

// Percentage of parent size
let percentage_size = Size::Percentage(50.0);

// Fit to content
let fit_size = Size::Fit;

// Fill available space
let fill_size = Size::Fill;
```

## Shader System

### Custom Shaders
Makepad allows for custom shader creation for advanced visual effects.

```rust
// Example of a custom shader definition (typically in a .glsl file)
/*
uniform vec4 color;
varying vec2 tex_coord;

fn vertex() -> vec4 {
    return vec4(
        (clip_pos.x - 0.5) * 2.0,
        (clip_pos.y - 0.5) * -2.0,
        0.0,
        1.0
    );
}

fn pixel() -> vec4 {
    return color;
}
*/
```

### Using Shaders
```rust
use makepad_draw::*;

// Apply a shader to a draw object
fn apply_shader_example(draw_quad: &mut DrawQuad) {
    // Shader application would typically happen through live design
    // or direct shader assignment
}
```

## Geometry Generation

### Basic Geometry
The geometry system provides utilities for generating common shapes.

```rust
use makepad_draw::*;

// Generate a quad geometry
let quad_geometry = GeometryQuad2D::new();

// Custom geometry generation
let mut geometry_gen = GeometryGen::new();
// Add vertices, indices, and other geometry data
```

## Text Rendering

### Text Properties
```rust
use makepad_draw::*;

let mut draw_text = DrawText::new();
draw_text.text = "Sample text".to_string();
draw_text.font_size = 16.0;
draw_text.color = vec4(0.0, 0.0, 0.0, 1.0); // Black color
draw_text.wrap = TextWrap::Word; // Word wrapping
draw_text.align = TextAlign::Center; // Center alignment
```

### Font Handling
```rust
// Font loading and management is typically handled through the live design system
// Fonts can be specified by name or loaded from files
```

## Practical Examples

### Simple UI Component
```rust
use makepad_draw::*;

fn draw_button(cx: &mut Cx2d, label: &str) {
    // Draw button background
    let mut draw_quad = DrawQuad::new();
    draw_quad.color = vec4(0.2, 0.6, 1.0, 1.0); // Blue background
    draw_quad.draw_quad(cx, &Walk::default(), &Layout::default());
    
    // Draw button text
    let mut draw_text = DrawText::new();
    draw_text.text = label.to_string();
    draw_text.color = vec4(1.0, 1.0, 1.0, 1.0); // White text
    draw_text.draw_text(cx, label);
}
```

### Custom Drawing Function
```rust
use makepad_draw::*;

fn draw_custom_shape(cx: &mut Cx2d) {
    // Draw a colored rectangle
    let mut draw_quad = DrawQuad::new();
    draw_quad.color = vec4(1.0, 0.0, 0.0, 1.0); // Red color
    draw_quad.draw_quad(cx, &Walk::default(), &Layout::default());
    
    // Draw a line across the shape
    let mut draw_line = DrawLine::new();
    draw_line.color = vec4(0.0, 0.0, 0.0, 1.0); // Black line
    draw_line.line_width = 1.0;
    draw_line.draw_line(cx, vec2(0.0, 0.0), vec2(100.0, 100.0));
}
```

## Performance Considerations

1. **Batch Drawing**: Group similar drawing operations to minimize state changes
2. **Reuse Draw Objects**: Reuse `DrawQuad`, `DrawText`, etc. instances when possible
3. **Efficient Layout**: Use appropriate `Walk` and `Layout` settings to avoid unnecessary calculations
4. **Texture Atlas**: Use texture atlases for icons and images to reduce texture switches

## Best Practices

1. **Separate Concerns**: Keep drawing logic separate from application logic
2. **Use Live Design**: Leverage the live design system for styling and layout
3. **Optimize Redraws**: Only redraw elements that have changed
4. **Handle Different Screen Sizes**: Use relative sizing and layout for responsive designs

## Related Components
- `CxDraw`: Core drawing context
- `DrawList2d`: Manages lists of 2D draw operations
- `GeometryGen`: Generates geometric shapes
- `Nav`: Navigation and focus management
- `Overlay`: Overlay elements and tooltips