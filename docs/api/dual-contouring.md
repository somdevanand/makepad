# dual-contouring API Reference

## Overview

The `dual-contouring` crate provides an implementation of the dual contouring algorithm for generating polygonal meshes from signed distance functions (SDFs). This API reference documents all public types, traits, and functions available in the crate.

## Modules

### `difference`

Provides the `Difference` struct for performing constructive solid geometry (CSG) difference operations on SDFs.

### `lerp`

Provides the `Lerp` trait for linear interpolation operations.

### `octree`

Provides the octree implementation used in the dual contouring algorithm.

### `sdf3`

Provides the `Sdf3` trait for defining 3D signed distance functions.

### `sdf_grid3`

Provides the `SdfGrid3` struct for sampling SDFs on a 3D grid.

### `sphere`

Provides the `Sphere` struct as an example implementation of an SDF.

### `vector3`

Provides the `Vector3` struct for 3D vector operations.

## Structs

### `Vector3`

A 3D vector with x, y, and z components.

```rust
#[derive(Clone, Copy, Debug)]
pub struct Vector3 {
    pub x: f32,
    pub y: f32,
    pub z: f32,
}
```

#### Methods

##### `Vector3::dot(self, other: Self) -> f32`

Computes the dot product of two vectors.

```rust
pub fn dot(self, other: Self) -> f32
```

**Parameters:**
- `other`: The other vector to compute the dot product with

**Returns:**
- The dot product of the two vectors

##### `Vector3::length(self) -> f32`

Computes the length (magnitude) of the vector.

```rust
pub fn length(self) -> f32
```

**Returns:**
- The length of the vector

##### `Vector3::normalize(self) -> Vector3`

Returns a normalized version of the vector (unit vector).

```rust
pub fn normalize(self) -> Vector3
```

**Returns:**
- A unit vector in the same direction as this vector

#### Trait Implementations

- `Add`: Adds two vectors component-wise
- `Sub`: Subtracts two vectors component-wise
- `Div<f32>`: Divides each component by a scalar
- `Lerp`: Linearly interpolates between two vectors

### `Sphere`

A sphere defined by a center point and radius.

```rust
pub struct Sphere {
    pub center: Vector3,
    pub radius: f32,
}
```

#### Trait Implementations

- `Sdf3`: Implements the SDF trait for sphere shapes

### `Difference<A, B>`

A constructive solid geometry (CSG) difference operation between two SDFs.

```rust
pub struct Difference<A: Sdf3, B: Sdf3>(pub A, pub B);
```

#### Trait Implementations

- `Sdf3`: Implements the SDF trait for difference operations

### `SdfGrid3`

A 3D grid of SDF values used in the dual contouring algorithm.

```rust
pub struct SdfGrid3 {
    // fields omitted
}
```

#### Methods

##### `SdfGrid3::from_sdf(sdf: &impl Sdf3, min: Vector3, max: Vector3, resolution: usize) -> SdfGrid3`

Creates a grid by sampling an SDF over a 3D region.

```rust
pub fn from_sdf(sdf: &impl Sdf3, min: Vector3, max: Vector3, resolution: usize) -> SdfGrid3
```

**Parameters:**
- `sdf`: The SDF to sample
- `min`: The minimum corner of the sampling region
- `max`: The maximum corner of the sampling region
- `resolution`: The number of samples along each axis

**Returns:**
- A grid containing sampled SDF values

### `Cell`

An octree cell used in the dual contouring algorithm.

```rust
pub enum Cell {
    Leaf {
        depth: usize,
        signs: [bool; 8],
        vertex: Option<Vector3>,
    },
    Branch {
        child_cells: Box<[Cell; 8]>,
    },
}
```

#### Methods

##### `Cell::traverse_leaf_edges(&self, f: &mut impl FnMut(Edge))`

Traverses all leaf edges in the octree and applies a function to each.

```rust
pub fn traverse_leaf_edges(&self, f: &mut impl FnMut(Edge))
```

**Parameters:**
- `f`: A function to apply to each leaf edge

### `Edge<'a>`

An edge in the octree structure.

```rust
pub struct Edge<'a> {
    // fields omitted
}
```

#### Methods

##### `Edge::has_sign_change(self) -> Option<bool>`

Determines if there is a sign change (surface crossing) along this edge.

```rust
pub fn has_sign_change(self) -> Option<bool>
```

**Returns:**
- `Some(true)` if there is a sign change
- `Some(false)` if there is no sign change
- `None` if this edge is not a leaf edge

##### `Edge::vertices(self) -> Option<Vec<Vector3>>`

Returns the vertices associated with this edge.

```rust
pub fn vertices(self) -> Option<Vec<Vector3>>
```

**Returns:**
- `Some(vertices)` if this is a leaf edge
- `None` if this is not a leaf edge

## Traits

### `Sdf3`

The trait for 3D signed distance functions.

```rust
pub trait Sdf3 {
    fn distance(&self, p: Vector3) -> f32;
    
    fn normal(&self, p: Vector3) -> Vector3 { ... }
}
```

#### Required Methods

##### `Sdf3::distance(&self, p: Vector3) -> f32`

Computes the signed distance from a point to the surface.

```rust
fn distance(&self, p: Vector3) -> f32
```

**Parameters:**
- `p`: The point to compute the distance to

**Returns:**
- The signed distance to the surface (negative inside, positive outside)

#### Provided Methods

##### `Sdf3::normal(&self, p: Vector3) -> Vector3`

Computes the normal vector at a point on the surface.

```rust
fn normal(&self, p: Vector3) -> Vector3
```

**Parameters:**
- `p`: The point to compute the normal at

**Returns:**
- The normalized normal vector at the point

### `Lerp`

The trait for types that support linear interpolation.

```rust
pub trait Lerp {
    fn lerp(self, other: Self, t: f32) -> Self;
}
```

#### Required Methods

##### `Lerp::lerp(self, other: Self, t: f32) -> Self`

Linearly interpolates between two values.

```rust
fn lerp(self, other: Self, t: f32) -> Self
```

**Parameters:**
- `other`: The other value to interpolate to
- `t`: The interpolation parameter (0.0 to 1.0)

**Returns:**
- The interpolated value

## Functions

### `from_grid(grid: &SdfGrid3) -> Cell`

Creates an octree from an SDF grid.

```rust
pub fn from_grid(grid: &SdfGrid3) -> Cell
```

**Parameters:**
- `grid`: The SDF grid to convert to an octree

**Returns:**
- The root cell of the generated octree

## Examples

### Basic Usage

```rust
use dual_contouring::sphere::Sphere;
use dual_contouring::vector3::Vector3;
use dual_contouring::sdf3::Sdf3;
use dual_contouring::sdf_grid3::SdfGrid3;
use dual_contouring::octree;

fn main() {
    // Create a sphere
    let sphere = Sphere {
        center: Vector3 { x: 0.0, y: 0.0, z: 0.0 },
        radius: 1.0,
    };

    // Create a grid from the SDF
    let grid = SdfGrid3::from_sdf(
        &sphere,
        Vector3 { x: -2.0, y: -2.0, z: -2.0 },
        Vector3 { x: 2.0, y: 2.0, z: 2.0 },
        8, // Resolution
    );

    // Generate the octree
    let octree_cell = octree::from_grid(&grid);
    
    println!("Generated octree from sphere SDF");
}
```

### Working with Vector3

```rust
use dual_contouring::vector3::Vector3;

fn main() {
    // Create vectors
    let v1 = Vector3 { x: 1.0, y: 2.0, z: 3.0 };
    let v2 = Vector3 { x: 4.0, y: 5.0, z: 6.0 };

    // Arithmetic operations
    let sum = v1 + v2;
    let diff = v1 - v2;
    let scaled = v1 / 2.0;

    println!("Sum: ({}, {}, {})", sum.x, sum.y, sum.z);
    println!("Difference: ({}, {}, {})", diff.x, diff.y, diff.z);
    println!("Scaled: ({}, {}, {})", scaled.x, scaled.y, scaled.z);

    // Vector methods
    let dot_product = v1.dot(v2);
    let length = v1.length();
    let normalized = v1.normalize();

    println!("Dot product: {}", dot_product);
    println!("Length: {}", length);
    println!("Normalized: ({}, {}, {})", normalized.x, normalized.y, normalized.z);
}
```

### CSG Operations

```rust
use dual_contouring::sphere::Sphere;
use dual_contouring::vector3::Vector3;
use dual_contouring::difference::Difference;
use dual_contouring::sdf3::Sdf3;

fn main() {
    // Create two spheres
    let sphere1 = Sphere {
        center: Vector3 { x: 0.0, y: 0.0, z: 0.0 },
        radius: 1.0,
    };

    let sphere2 = Sphere {
        center: Vector3 { x: 0.5, y: 0.0, z: 0.0 },
        radius: 1.0,
    };

    // Create a difference (sphere1 minus sphere2)
    let difference = Difference(sphere1, sphere2);

    // Get the distance to a point
    let distance = difference.distance(Vector3 { x: 0.0, y: 0.0, z: 0.0 });
    
    println!("Distance at origin: {}", distance);
}