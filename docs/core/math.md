# Math Library

## Overview
The Math library in Makepad provides a comprehensive set of mathematical structures and operations essential for graphics programming, game development, and scientific computing. It includes implementations of vectors, matrices, quaternions, and various utility functions.

## Vector Types

### Vec2 (2D Vector)
Represents a two-dimensional vector with `x` and `y` components.

#### Creation
```rust
use makepad_math::Vec2;

// Create a new Vec2
let v = Vec2 { x: 1.0, y: 2.0 };

// Using the vec2 helper function
let v = makepad_math::vec2(1.0, 2.0);

// Create a Vec2 with all components set to the same value
let v = Vec2::all(5.0); // Results in Vec2 { x: 5.0, y: 5.0 }
```

#### Operations
```rust
let a = vec2(1.0, 2.0);
let b = vec2(3.0, 4.0);

// Addition
let sum = a + b;

// Subtraction
let diff = a - b;

// Scalar multiplication
let scaled = a * 2.0;

// Length (magnitude)
let length = a.length();

// Normalization
let normalized = a.normalize();

// Dot product
let dot_product = a.dot(b);

// Distance between two vectors
let distance = a.distance(&b);

// Linear interpolation
let interpolated = Vec2::from_lerp(a, b, 0.5);
```

### Vec3 (3D Vector)
Represents a three-dimensional vector with `x`, `y`, and `z` components.

#### Creation
```rust
use makepad_math::Vec3;

// Create a new Vec3
let v = Vec3 { x: 1.0, y: 2.0, z: 3.0 };

// Using the vec3 helper function
let v = makepad_math::vec3(1.0, 2.0, 3.0);

// Create a Vec3 with all components set to the same value
let v = Vec3::all(5.0); // Results in Vec3 { x: 5.0, y: 5.0, z: 5.0 }
```

#### Operations
```rust
let a = vec3(1.0, 2.0, 3.0);
let b = vec3(4.0, 5.0, 6.0);

// Addition
let sum = a + b;

// Subtraction
let diff = a - b;

// Scalar multiplication
let scaled = a * 2.0;

// Length (magnitude)
let length = a.length();

// Normalization
let normalized = a.normalize();

// Dot product
let dot_product = a.dot(b);

// Cross product
let cross_product = Vec3::cross(a, b);

// Linear interpolation
let interpolated = Vec3::from_lerp(a, b, 0.5);
```

### Vec4 (4D Vector)
Represents a four-dimensional vector with `x`, `y`, `z`, and `w` components.

#### Creation
```rust
use makepad_math::Vec4;

// Create a new Vec4
let v = Vec4 { x: 1.0, y: 2.0, z: 3.0, w: 4.0 };

// Using the vec4 helper function
let v = makepad_math::vec4(1.0, 2.0, 3.0, 4.0);

// Create a Vec4 with all components set to the same value
let v = Vec4::all(5.0); // Results in Vec4 { x: 5.0, y: 5.0, z: 5.0, w: 5.0 }
```

#### Operations
```rust
let a = vec4(1.0, 2.0, 3.0, 4.0);
let b = vec4(5.0, 6.0, 7.0, 8.0);

// Addition
let sum = a + b;

// Subtraction
let diff = a - b;

// Scalar multiplication
let scaled = a * 2.0;

// Dot product
let dot_product = a.dot(b);

// Linear interpolation
let interpolated = Vec4::from_lerp(a, b, 0.5);

// Color manipulation (RGBA)
let color = vec4(0.5, 0.7, 0.9, 1.0); // RGBA values between 0.0 and 1.0
```

## Matrix Types

### Mat4 (4x4 Matrix)
Represents a 4x4 matrix commonly used for 3D transformations.

#### Creation
```rust
use makepad_math::Mat4;

// Identity matrix
let identity = Mat4::identity();

// Translation matrix
let translation = Mat4::translation(makepad_math::vec3(1.0, 2.0, 3.0));

// Scaling matrix
let scale = Mat4::scale(2.0);

// Rotation matrix
let rotation = Mat4::rotation(makepad_math::vec3(0.0, 1.0, 0.0)); // Rotate around Y axis

// Perspective projection matrix
let perspective = Mat4::perspective(45.0, 16.0/9.0, 0.1, 100.0);
```

#### Operations
```rust
let a = Mat4::identity();
let b = Mat4::translation(vec3(1.0, 2.0, 3.0));

// Matrix multiplication
let result = Mat4::mul(&a, &b);

// Transform a vector
let v = vec4(1.0, 0.0, 0.0, 1.0);
let transformed = a.transform_vec4(v);

// Invert a matrix
let inverted = a.invert();
```

## Quaternion Types

### Quat (Quaternion)
Represents a quaternion used for 3D rotations.

#### Creation
```rust
use makepad_math::Quat;

// Identity quaternion
let identity = Quat::default();

// Quaternion from rotation
let quat = Quat::look_rotation(
    makepad_math::vec3(0.0, 0.0, 1.0), // Forward vector
    makepad_math::vec3(0.0, 1.0, 0.0)  // Up vector
);
```

#### Operations
```rust
let a = Quat::default();
let b = Quat::look_rotation(vec3(0.0, 0.0, 1.0), vec3(0.0, 1.0, 0.0));

// Quaternion multiplication
let result = Quat::multiply(&a, &b);

// Invert a quaternion
let inverted = a.invert();

// Rotate a vector
let v = vec3(1.0, 0.0, 0.0);
let rotated = a.rotate_vec3(&v);

// Spherical linear interpolation
let interpolated = Quat::from_slerp(a, b, 0.5);
```

## Geometric Types

### Pose
Represents a 3D pose with orientation (quaternion) and position (vector).

#### Creation and Usage
```rust
use makepad_math::{Pose, Quat, Vec3};

let pose = Pose::new(
    Quat::default(),  // Identity orientation
    vec3(1.0, 2.0, 3.0)  // Position
);

// Transform a vector by the pose
let v = vec3(0.0, 0.0, 1.0);
let transformed = pose.transform_vec3(&v);

// Convert to matrix
let matrix = pose.to_mat4();
```

## Utility Functions

### Angle Conversions
```rust
let radians = 1.5708; // π/2
let degrees = radians * 57.29578; // Convert to degrees

let degrees = 90.0;
let radians = degrees * 0.017453292; // Convert to radians
```

### Color Manipulation
```rust
// Create a color from RGBA values
let color = vec4(0.5, 0.7, 0.9, 1.0);

// Convert to/from u32 representation
let color_u32 = color.to_u32();
let color_from_u32 = Vec4::from_u32(color_u32);

// HSV conversion
let hsv_color = color.to_hsva();
let rgb_color = Vec4::from_hsva(hsv_color);
```

## Practical Examples

### 3D Transformation Chain
```rust
use makepad_math::*;

// Create transformation matrices
let translation = Mat4::translation(vec3(1.0, 2.0, 3.0));
let rotation = Mat4::rotation(vec3(0.0, 1.0, 0.0)); // Rotate around Y axis
let scale = Mat4::scaled_translation(2.0, vec3(0.0, 0.0, 0.0));

// Combine transformations (order matters!)
let transform = Mat4::mul(&Mat4::mul(&scale, &rotation), &translation);

// Apply transformation to a point
let point = vec4(1.0, 0.0, 0.0, 1.0);
let transformed_point = transform.transform_vec4(point);
```

### Camera Setup
```rust
use makepad_math::*;

// Create a perspective projection matrix
let projection = Mat4::perspective(45.0, 16.0/9.0, 0.1, 100.0);

// Create a view matrix (camera transform)
let camera_position = vec3(0.0, 0.0, 5.0);
let target_position = vec3(0.0, 0.0, 0.0);
let up_vector = vec3(0.0, 1.0, 0.0);

let view_direction = (target_position - camera_position).normalize();
let view_rotation = Quat::look_rotation(view_direction, up_vector);
let view_translation = Mat4::translation(-camera_position);

let view = Mat4::mul(&view_translation, &view_rotation.to_mat4());

// Combine projection and view matrices
let view_projection = Mat4::mul(&projection, &view);
```

## Performance Considerations

1. **Use appropriate vector types**: Use Vec2, Vec3, or Vec4 based on your dimensionality needs
2. **Precompute transformations**: Combine matrices when possible rather than applying multiple transformations
3. **Avoid unnecessary normalization**: Only normalize vectors when needed
4. **Use helper functions**: Leverage provided functions like `vec2()`, `vec3()`, `vec4()` for cleaner code

## Best Practices

1. **Prefer stack allocation**: These math types are designed to be lightweight and stack-allocated
2. **Use const where possible**: Many creation functions are `const` and can be used in const contexts
3. **Leverage operator overloading**: Use `+`, `-`, `*`, `/` operators for cleaner mathematical expressions
4. **Consider memory layout**: These types are `#[repr(C)]` for compatibility with graphics APIs

## Related Components
- `Pose`: Combines position and orientation
- `Plane`: Represents a 3D plane for intersection calculations
- `CameraFov`: Field of view parameters for camera calculations