# ab_glyph_rasterizer API Reference

## Overview

The `ab_glyph_rasterizer` crate provides coverage rasterization for lines, quadratic Bézier curves, and cubic Bézier curves. This API reference documents all public types, functions, and methods available in the crate.

## Structs

### `Point`

An (x, y) coordinate used to represent positions in 2D space.

```rust
#[derive(Clone, Copy, Default, PartialEq, PartialOrd)]
pub struct Point {
    pub x: f32,
    pub y: f32,
}
```

#### Methods

##### `Point::distance_to(&self, other: Point) -> f32`

Calculates the Euclidean distance between this point and another point.

```rust
pub fn distance_to(self, other: Point) -> f32
```

#### Trait Implementations

- `Debug`: Formats the point as "point(x, y)"
- `Sub`: Subtracts one point from another
- `Add`: Adds two points together
- `AddAssign`: Adds another point to this point
- `SubAssign`: Subtracts another point from this point
- `From<(F, F)>`: Creates a point from a tuple of coordinates
- `From<[F; 2]>`: Creates a point from an array of coordinates

### `Rasterizer`

Coverage rasterizer for lines, quadratic & cubic Bézier curves.

```rust
pub struct Rasterizer {
    // fields omitted
}
```

#### Methods

##### `Rasterizer::new(width: usize, height: usize) -> Self`

Allocates a new rasterizer that can draw onto a `width` x `height` alpha grid.

```rust
pub fn new(width: usize, height: usize) -> Self
```

**Parameters:**
- `width`: Width of the rasterization grid in pixels
- `height`: Height of the rasterization grid in pixels

**Returns:**
- A new `Rasterizer` instance

**Example:**
```rust
use ab_glyph_rasterizer::Rasterizer;
let mut rasterizer = Rasterizer::new(14, 38);
```

##### `Rasterizer::reset(&mut self, width: usize, height: usize)`

Resets the rasterizer to an empty `width` x `height` alpha grid. This method behaves as if the Rasterizer were re-created, with the advantage of not allocating if the total number of pixels of the grid does not increase.

```rust
pub fn reset(&mut self, width: usize, height: usize)
```

**Parameters:**
- `width`: New width of the rasterization grid in pixels
- `height`: New height of the rasterization grid in pixels

**Example:**
```rust
let mut rasterizer = Rasterizer::new(14, 38);
rasterizer.reset(12, 24);
assert_eq!(rasterizer.dimensions(), (12, 24));
```

##### `Rasterizer::clear(&mut self)`

Clears the rasterizer. This method behaves as if the Rasterizer were re-created with the same dimensions, but does not perform an allocation.

```rust
pub fn clear(&mut self)
```

**Example:**
```rust
let mut rasterizer = Rasterizer::new(14, 38);
rasterizer.clear();
```

##### `Rasterizer::dimensions(&self) -> (usize, usize)`

Returns the dimensions the rasterizer was built to draw to.

```rust
pub fn dimensions(&self) -> (usize, usize)
```

**Returns:**
- A tuple containing (width, height) of the rasterization grid

**Example:**
```rust
let rasterizer = Rasterizer::new(9, 8);
assert_eq!((9, 8), rasterizer.dimensions());
```

##### `Rasterizer::draw_line(&mut self, p0: Point, p1: Point)`

Adds a straight line from `p0` to `p1` to the outline.

```rust
pub fn draw_line(&mut self, p0: Point, p1: Point)
```

**Parameters:**
- `p0`: Starting point of the line
- `p1`: Ending point of the line

**Example:**
```rust
use ab_glyph_rasterizer::*;
let mut rasterizer = Rasterizer::new(9, 8);
rasterizer.draw_line(point(0.0, 0.48), point(1.22, 0.48));
```

##### `Rasterizer::draw_quad(&mut self, p0: Point, p1: Point, p2: Point)`

Adds a quadratic Bézier curve from `p0` to `p2` to the outline using `p1` as the control point.

```rust
pub fn draw_quad(&mut self, p0: Point, p1: Point, p2: Point)
```

**Parameters:**
- `p0`: Starting point of the curve
- `p1`: Control point that influences the curve shape
- `p2`: Ending point of the curve

**Example:**
```rust
use ab_glyph_rasterizer::*;
let mut rasterizer = Rasterizer::new(14, 38);
rasterizer.draw_quad(point(6.2, 34.5), point(7.2, 34.5), point(9.2, 34.0));
```

##### `Rasterizer::draw_cubic(&mut self, p0: Point, p1: Point, p2: Point, p3: Point)`

Adds a cubic Bézier curve from `p0` to `p3` to the outline using `p1` as the control at the beginning of the curve and `p2` at the end of the curve.

```rust
pub fn draw_cubic(&mut self, p0: Point, p1: Point, p2: Point, p3: Point)
```

**Parameters:**
- `p0`: Starting point of the curve
- `p1`: First control point that influences the curve shape at the start
- `p2`: Second control point that influences the curve shape at the end
- `p3`: Ending point of the curve

**Example:**
```rust
use ab_glyph_rasterizer::*;
let mut rasterizer = Rasterizer::new(12, 20);
rasterizer.draw_cubic(
    point(10.3, 16.4),
    point(8.6, 16.9),
    point(7.7, 16.5),
    point(8.2, 15.2),
);
```

##### `Rasterizer::for_each_pixel<O: FnMut(usize, f32)>(&self, px_fn: O)`

Run a callback for each pixel `index` & `alpha`, with indices in `0..width * height`.

An `alpha` coverage value of `0.0` means the pixel is not covered at all by the glyph, whereas a value of `1.0` (or greater) means the pixel is totally covered.

```rust
pub fn for_each_pixel<O: FnMut(usize, f32)>(&self, px_fn: O)
```

**Parameters:**
- `px_fn`: Callback function that receives the pixel index and alpha coverage value

**Example:**
```rust
let (width, height) = (1, 1);
let mut rasterizer = Rasterizer::new(width, height);
let mut pixels = vec![0u8; width * height];
rasterizer.for_each_pixel(|index, alpha| {
    pixels[index] = (alpha * 255.0) as u8;
});
```

##### `Rasterizer::for_each_pixel_2d<O: FnMut(u32, u32, f32)>(&self, px_fn: O)`

Run a callback for each pixel x position, y position & alpha.

Convenience wrapper for `Rasterizer::for_each_pixel`.

```rust
pub fn for_each_pixel_2d<O: FnMut(u32, u32, f32)>(&self, px_fn: O)
```

**Parameters:**
- `px_fn`: Callback function that receives the x position, y position, and alpha coverage value

**Example:**
```rust
let mut rasterizer = Rasterizer::new(1, 1);
struct Img;
impl Img { fn set_pixel(&self, x: u32, y: u32, a: u8) {} }
let image = Img;
rasterizer.for_each_pixel_2d(|x, y, alpha| {
    image.set_pixel(x, y, (alpha * 255.0) as u8);
});
```

#### Trait Implementations

- `Debug`: Formats the rasterizer with its width and height

## Functions

### `point(x: f32, y: f32) -> Point`

Creates a new `Point` with the specified coordinates.

```rust
pub fn point(x: f32, y: f32) -> Point
```

**Parameters:**
- `x`: X coordinate
- `y`: Y coordinate

**Returns:**
- A new `Point` instance

**Example:**
```rust
use ab_glyph_rasterizer::{point, Point};
let p: Point = point(0.1, 23.2);
```

### `lerp(t: f32, p0: Point, p1: Point) -> Point`

Linear interpolation between two points.

```rust
pub fn lerp(t: f32, p0: Point, p1: Point) -> Point
```

**Parameters:**
- `t`: Interpolation factor (0.0 to 1.0)
- `p0`: First point
- `p1`: Second point

**Returns:**
- Interpolated point between p0 and p1

## Feature Flags

### `std`

Enables standard library features and runtime SIMD detection. This is the default feature.

### `libm`

Uses libm for floating-point operations in `no_std` environments. Required for `no_std` usage.

## Examples

### Basic Usage

```rust
use ab_glyph_rasterizer::Rasterizer;
let mut rasterizer = Rasterizer::new(width, height);

// draw outlines
let [l0, l1, q0, q1, q2, c0, c1, c2, c3] = [ab_glyph_rasterizer::point(0.0, 0.0); 9];
rasterizer.draw_line(l0, l1);
rasterizer.draw_quad(q0, q1, q2);
rasterizer.draw_cubic(c0, c1, c2, c3);

// iterate over the resultant pixel alphas
rasterizer.for_each_pixel(|index, alpha| {
    // Process pixel at index with alpha coverage
});