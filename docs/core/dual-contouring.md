# dual-contouring Documentation

## Overview

The `dual-contouring` crate provides an implementation of the dual contouring algorithm for generating polygonal meshes from signed distance functions (SDFs). Dual contouring is a technique for extracting surfaces from implicit functions that produces high-quality meshes with sharp features and smooth surfaces.

## Purpose and Use Cases

The dual-contouring crate is useful for:

- Generating 3D meshes from mathematical descriptions of shapes
- Creating procedural geometry in game engines or CAD applications
- Converting implicit surfaces to polygonal meshes for rendering
- Implementing constructive solid geometry (CSG) operations
- Scientific visualization of scalar fields
- Educational purposes to understand mesh generation algorithms

## Key Features

- **Dual Contouring Algorithm**: Complete implementation of the dual contouring algorithm
- **Signed Distance Functions**: Support for defining shapes using SDFs
- **Octree-based Refinement**: Adaptive refinement using octree data structures
- **Sharp Feature Preservation**: Maintains sharp edges and corners in the generated meshes
- **Constructive Solid Geometry**: Support for combining shapes using CSG operations
- **No External Dependencies**: Self-contained implementation with minimal dependencies

## Getting Started

To use the `dual-contouring` crate, you'll typically work with signed distance functions and the dual contouring algorithm to generate meshes. Here's a basic example:

```rust
// Note: This is a conceptual example as the crate is primarily a binary
// The actual API would be used in a similar way:

use dual_contouring::sphere::Sphere;
use dual_contouring::vector3::Vector3;
use dual_contouring::sdf3::Sdf3;
use dual_contouring::sdf_grid3::SdfGrid3;
use dual_contouring::octree;

// Define a sphere
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
let octree = octree::from_grid(&grid);

// Extract vertices and faces (conceptual)
// The actual implementation would traverse the octree to extract mesh data
```

## Core Concepts

### Signed Distance Functions (SDFs)

Signed Distance Functions are mathematical functions that return the shortest distance from a point to a surface. The sign indicates whether the point is inside (negative) or outside (positive) the shape.

```rust
// Example SDF for a sphere
fn sphere_sdf(center: Vector3, radius: f32, point: Vector3) -> f32 {
    (point - center).length() - radius
}
```

### Dual Contouring Algorithm

Dual contouring is a technique for extracting polygonal meshes from implicit surfaces. Unlike marching cubes, dual contouring can preserve sharp features by placing vertices at points that best represent the surface.

The algorithm works by:
1. Creating an octree representation of the SDF
2. For each leaf node, determining if the surface passes through it
3. Computing a vertex position that best represents the surface in that cell
4. Generating polygons by connecting vertices across cell boundaries

### Octree Representation

The implementation uses an octree data structure for adaptive refinement. Each node in the octree represents a cubic region of space, and can be either:
- A leaf node containing SDF values at the corners
- A branch node with 8 child nodes

## Basic Operations

### Defining Shapes with SDFs

```rust
use dual_contouring::sphere::Sphere;
use dual_contouring::vector3::Vector3;
use dual_contouring::sdf3::Sdf3;

// Create a sphere
let sphere = Sphere {
    center: Vector3 { x: 0.0, y: 0.0, z: 0.0 },
    radius: 1.0,
};

// Get the distance to a point
let distance = sphere.distance(Vector3 { x: 0.5, y: 0.5, z: 0.5 });
```

### Combining Shapes with CSG

```rust
use dual_contouring::sphere::Sphere;
use dual_contouring::vector3::Vector3;
use dual_contouring::difference::Difference;
use dual_contouring::sdf3::Sdf3;

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
```

### Generating Meshes

```rust
use dual_contouring::sphere::Sphere;
use dual_contouring::vector3::Vector3;
use dual_contouring::sdf3::Sdf3;
use dual_contouring::sdf_grid3::SdfGrid3;
use dual_contouring::octree;

// Define a shape
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

// Extract mesh data (conceptual)
// In practice, you would traverse the octree to extract vertices and faces
```

## Working with Vector3

The crate provides a `Vector3` struct for 3D vector operations:

```rust
use dual_contouring::vector3::Vector3;

// Create vectors
let v1 = Vector3 { x: 1.0, y: 2.0, z: 3.0 };
let v2 = Vector3 { x: 4.0, y: 5.0, z: 6.0 };

// Vector operations
let sum = v1 + v2;
let diff = v1 - v2;
let scaled = v1 / 2.0;

// Vector methods
let dot_product = v1.dot(v2);
let length = v1.length();
let normalized = v1.normalize();
```

## Performance Considerations

### Octree Resolution

The resolution parameter in `SdfGrid3::from_sdf` affects both quality and performance:
- Higher resolution produces more detailed meshes but takes longer to compute
- Lower resolution is faster but may miss fine details

### Memory Usage

The octree structure is memory-efficient:
- Only subdivides regions where the surface is present
- Leaf nodes store minimal data (SDF values at corners)
- Branch nodes store references to child nodes

### Computational Complexity

The algorithm complexity depends on:
- The complexity of the SDF being evaluated
- The resolution of the grid
- The amount of surface detail in the shape

## Best Practices

1. **Choose Appropriate Resolution**: Balance quality and performance based on your needs
2. **Use Simple SDFs**: Complex SDFs will slow down the algorithm
3. **Combine Shapes Efficiently**: Use CSG operations to create complex shapes from simple primitives
4. **Cache SDF Values**: If evaluating the same points multiple times, consider caching results
5. **Validate Mesh Output**: Always check that generated meshes are valid for your use case

## Examples

### Basic Sphere Generation

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
    
    // In a real implementation, you would traverse the octree
    // to extract vertices and faces for the mesh
    println!("Generated octree from sphere SDF");
}
```

### CSG Difference Operation

```rust
use dual_contouring::sphere::Sphere;
use dual_contouring::vector3::Vector3;
use dual_contouring::difference::Difference;
use dual_contouring::sdf3::Sdf3;
use dual_contouring::sdf_grid3::SdfGrid3;
use dual_contouring::octree;

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

    // Create a grid from the SDF
    let grid = SdfGrid3::from_sdf(
        &difference,
        Vector3 { x: -2.0, y: -2.0, z: -2.0 },
        Vector3 { x: 2.0, y: 2.0, z: 2.0 },
        8, // Resolution
    );

    // Generate the octree
    let octree_cell = octree::from_grid(&grid);
    
    println!("Generated octree from difference SDF");
}
```

### Working with Vector3 Operations

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