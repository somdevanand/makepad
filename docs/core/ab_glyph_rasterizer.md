# ab_glyph_rasterizer Documentation

## Overview

The `ab_glyph_rasterizer` crate provides coverage rasterization for lines, quadratic Bézier curves, and cubic Bézier curves. It is particularly useful for drawing OpenType font glyphs. The crate efficiently converts vector outlines into pixel coverage values that can be used for anti-aliased rendering.

## Purpose and Use Cases

This crate is designed to rasterize vector graphics into pixel coverage values. It's especially useful for:

- Rendering font glyphs from vector outlines
- Converting Bézier curves to rasterized pixel data
- Generating anti-aliased graphics for UI elements
- Creating coverage maps for vector graphics

## Key Features

- **Line Rasterization**: Efficiently rasterizes straight lines
- **Quadratic Bézier Curves**: Supports quadratic Bézier curve rasterization
- **Cubic Bézier Curves**: Supports cubic Bézier curve rasterization
- **Anti-aliasing**: Produces smooth, anti-aliased output
- **Optimized Performance**: Uses SIMD instructions when available
- **No Standard Library Required**: Can work in `no_std` environments with the `libm` feature

## Getting Started

To use the `ab_glyph_rasterizer` crate, first add it to your `Cargo.toml`:

```toml
[dependencies]
ab_glyph_rasterizer = "0.1"
```

Then, in your Rust code:

```rust
use ab_glyph_rasterizer::{Rasterizer, point};

// Create a new rasterizer with specified dimensions
let mut rasterizer = Rasterizer::new(100, 100);

// Draw vector shapes
rasterizer.draw_line(point(10.0, 10.0), point(90.0, 90.0));
rasterizer.draw_quad(point(20.0, 20.0), point(50.0, 10.0), point(80.0, 20.0));
rasterizer.draw_cubic(
    point(10.0, 80.0),
    point(30.0, 90.0),
    point(70.0, 70.0),
    point(90.0, 80.0)
);

// Process the rasterized pixels
rasterizer.for_each_pixel(|index, alpha| {
    // Use the alpha coverage value (0.0 to 1.0)
    // index is the pixel position in the buffer
    // alpha is the coverage value
});
```

## Core Concepts

### Points

Points are represented by the `Point` struct with `x` and `y` f32 coordinates. You can create points using the `point()` function:

```rust
use ab_glyph_rasterizer::{point, Point};

let p1: Point = point(10.5, 20.3);
let p2 = Point { x: 5.0, y: 15.0 };
```

### Rasterizer

The `Rasterizer` is the main struct that handles the rasterization process. It maintains an internal buffer for accumulating coverage values.

## Working with Different Curve Types

### Lines

Lines are the simplest shape to rasterize:

```rust
rasterizer.draw_line(point(0.0, 0.0), point(100.0, 100.0));
```

### Quadratic Bézier Curves

Quadratic Bézier curves are defined by three points: start, control, and end:

```rust
rasterizer.draw_quad(
    point(10.0, 50.0),  // Start point
    point(50.0, 10.0),  // Control point
    point(90.0, 50.0)   // End point
);
```

### Cubic Bézier Curves

Cubic Bézier curves are defined by four points: start, two control points, and end:

```rust
rasterizer.draw_cubic(
    point(10.0, 50.0),   // Start point
    point(30.0, 10.0),   // First control point
    point(70.0, 90.0),   // Second control point
    point(90.0, 50.0)    // End point
);
```

## Processing Rasterized Output

After drawing all shapes, you can process the rasterized output using the pixel iteration methods:

```rust
// Process pixels with linear index
rasterizer.for_each_pixel(|index, alpha| {
    // index: linear pixel index (0 to width*height-1)
    // alpha: coverage value (0.0 to 1.0)
});

// Process pixels with 2D coordinates
rasterizer.for_each_pixel_2d(|x, y, alpha| {
    // x, y: pixel coordinates
    // alpha: coverage value (0.0 to 1.0)
});
```

## Performance Considerations

The crate automatically uses SIMD optimizations when available (AVX2 or SSE4.2 on x86/x86_64 with the `std` feature). For `no_std` environments, you can enable the `libm` feature for floating-point operations.

## Feature Flags

- `std`: Enables standard library features and runtime SIMD detection (default)
- `libm`: Uses libm for floating-point operations in `no_std` environments

## Examples

### Basic Rasterization

```rust
use ab_glyph_rasterizer::{Rasterizer, point};

fn main() {
    // Create a 64x64 rasterizer
    let mut rasterizer = Rasterizer::new(64, 64);
    
    // Draw a triangle using lines
    rasterizer.draw_line(point(10.0, 10.0), point(50.0, 10.0));
    rasterizer.draw_line(point(50.0, 10.0), point(30.0, 50.0));
    rasterizer.draw_line(point(30.0, 50.0), point(10.0, 10.0));
    
    // Convert to pixel buffer
    let mut pixels = vec![0u8; 64 * 64];
    rasterizer.for_each_pixel(|index, alpha| {
        pixels[index] = (alpha * 255.0) as u8;
    });
}
```

### Font Glyph Rasterization

```rust
use ab_glyph_rasterizer::{Rasterizer, point};

fn rasterize_glyph_outline(outline: &[(bool, Vec<[f32; 2]>)], width: usize, height: usize) -> Vec<u8> {
    let mut rasterizer = Rasterizer::new(width, height);
    
    // Process each contour in the glyph outline
    for (is_closed, points) in outline {
        if points.len() < 2 {
            continue;
        }
        
        let mut i = 0;
        while i + 1 < points.len() {
            if i + 2 < points.len() && points[i + 2][0] != -1.0 {
                // Quadratic Bézier curve
                rasterizer.draw_quad(
                    point(points[i][0], points[i][1]),
                    point(points[i + 1][0], points[i + 1][1]),
                    point(points[i + 2][0], points[i + 2][1])
                );
                i += 2;
            } else {
                // Straight line
                rasterizer.draw_line(
                    point(points[i][0], points[i][1]),
                    point(points[i + 1][0], points[i + 1][1])
                );
                i += 1;
            }
        }
        
        // Close the contour if needed
        if *is_closed && points.len() > 2 {
            rasterizer.draw_line(
                point(points[points.len() - 1][0], points[points.len() - 1][1]),
                point(points[0][0], points[0][1])
            );
        }
    }
    
    // Convert to pixel buffer
    let mut pixels = vec![0u8; width * height];
    rasterizer.for_each_pixel(|index, alpha| {
        pixels[index] = (alpha * 255.0) as u8;
    });
    
    pixels
}