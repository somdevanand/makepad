# Core Libraries API Reference

## Live ID System

### Structs

#### `LiveId`
A unique identifier for live objects.

```rust
#[derive(Clone, Default, Eq, Hash, Copy, Ord, PartialOrd, PartialEq)]
pub struct LiveId(pub u64);
```

##### Methods

###### `LiveId::empty()`
Creates an empty LiveId (value of 0).

```rust
pub fn empty() -> Self
```

###### `LiveId::from_lo_hi(lo: u32, hi: u32) -> Self`
Creates a LiveId from low and high 32-bit values.

```rust
pub fn from_lo_hi(lo: u32, hi: u32) -> Self
```

###### `LiveId::lo(&self) -> u32`
Returns the low 32-bit value of the LiveId.

```rust
pub fn lo(&self) -> u32
```

###### `LiveId::hi(&self) -> u32`
Returns the high 32-bit value of the LiveId.

```rust
pub fn hi(&self) -> u32
```

###### `LiveId::seeded() -> Self`
Creates a LiveId with the seed value.

```rust
pub fn seeded() -> Self
```

###### `LiveId::is_unique(&self) -> bool`
Checks if the LiveId is a unique identifier.

```rust
pub fn is_unique(&self) -> bool
```

###### `LiveId::is_ident(&self) -> bool`
Checks if the LiveId is a hash-based identifier.

```rust
pub fn is_ident(&self) -> bool
```

###### `LiveId::is_empty(&self) -> bool`
Checks if the LiveId is empty (value of 0).

```rust
pub fn is_empty(&self) -> bool
```

###### `LiveId::get_value(&self) -> u64`
Returns the underlying 64-bit value of the LiveId.

```rust
pub fn get_value(&self) -> u64
```

###### `LiveId::from_str(id_str: &str) -> Self`
Creates a LiveId from a string.

```rust
pub const fn from_str(id_str: &str) -> Self
```

###### `LiveId::from_str_lc(id_str: &str) -> Self`
Creates a LiveId from a string, converting to lowercase.

```rust
pub const fn from_str_lc(id_str: &str) -> Self
```

###### `LiveId::str_append(self, id_str: &str) -> Self`
Creates a new LiveId by appending a string to an existing LiveId.

```rust
pub const fn str_append(self, id_str: &str) -> Self
```

###### `LiveId::id_append(self, id: LiveId) -> Self`
Creates a new LiveId by appending another LiveId.

```rust
pub const fn id_append(self, id: LiveId) -> Self
```

###### `LiveId::unique() -> Self`
Generates a new unique LiveId.

```rust
pub fn unique() -> Self
```

### Enums

#### `InternLiveId`
Controls whether strings are interned when creating LiveIds.

```rust
#[derive(Clone, Copy)]
pub enum InternLiveId {
    Yes,
    No,
}
```

### Traits

#### `FromLiveId`
Trait for types that can be created from a LiveId.

```rust
pub trait FromLiveId {
    fn from_live_id(id: LiveId) -> Self;
}
```

### Functions

#### `live_id!`
Macro to create a LiveId from a string literal.

```rust
macro_rules! live_id {
    ($s:expr) => { ... };
}
```

#### `id!`
Macro to create an array of LiveIds from a sequence of identifiers.

```rust
macro_rules! id {
    ($($s:ident).+) => { ... };
}
```

#### `ids!`
Macro to create a nested array of LiveIds.

```rust
macro_rules! ids {
    ($($($s:ident).+),+) => { ... };
}

## Math Library

### Structs

#### `Vec2`
A two-dimensional vector.

```rust
#[repr(C)]
#[derive(Clone, Copy, Default, PartialEq, Debug)]
pub struct Vec2 {
    pub x: f32,
    pub y: f32,
}
```

##### Methods

###### `Vec2::new() -> Vec2`
Creates a new Vec2 with default values (0.0, 0.0).

```rust
pub fn new() -> Vec2
```

###### `Vec2::all(x: f32) -> Vec2`
Creates a Vec2 with all components set to the same value.

```rust
pub fn all(x: f32) -> Vec2
```

###### `Vec2::from_lerp(a: Vec2, b: Vec2, f: f32) -> Vec2`
Linearly interpolates between two Vec2 values.

```rust
pub fn from_lerp(a: Vec2, b: Vec2, f: f32) -> Vec2
```

###### `Vec2::distance(&self, other: &Vec2) -> f32`
Calculates the distance between two Vec2 values.

```rust
pub fn distance(&self, other: &Vec2) -> f32
```

###### `Vec2::length(&self) -> f32`
Calculates the length (magnitude) of the Vec2.

```rust
pub fn length(&self) -> f32
```

###### `Vec2::normalize(&self) -> Vec2`
Returns a normalized version of the Vec2.

```rust
pub fn normalize(&self) -> Vec2
```

#### `Vec3`
A three-dimensional vector.

```rust
#[repr(C)]
#[derive(Clone, Copy, Default, PartialEq, Debug)]
pub struct Vec3 {
    pub x: f32,
    pub y: f32,
    pub z: f32,
}
```

##### Methods

###### `Vec3::from_lerp(a: Vec3, b: Vec3, f: f32) -> Vec3`
Linearly interpolates between two Vec3 values.

```rust
pub fn from_lerp(a: Vec3, b: Vec3, f: f32) -> Vec3
```

###### `Vec3::scale(&self, f: f32) -> Vec3`
Scales the Vec3 by a scalar value.

```rust
pub fn scale(&self, f: f32) -> Vec3
```

###### `Vec3::cross(a: Vec3, b: Vec3) -> Vec3`
Calculates the cross product of two Vec3 values.

```rust
pub fn cross(a: Vec3, b: Vec3) -> Vec3
```

###### `Vec3::dot(&self, other: Vec3) -> f32`
Calculates the dot product of two Vec3 values.

```rust
pub fn dot(&self, other: Vec3) -> f32
```

###### `Vec3::normalize(&self) -> Vec3`
Returns a normalized version of the Vec3.

```rust
pub fn normalize(&self) -> Vec3
```

###### `Vec3::length(&self) -> f32`
Calculates the length (magnitude) of the Vec3.

```rust
pub fn length(&self) -> f32
```

#### `Vec4`
A four-dimensional vector.

```rust
#[repr(C)]
#[derive(Clone, Copy, Default, Debug, PartialEq)]
pub struct Vec4 {
    pub x: f32,
    pub y: f32,
    pub z: f32,
    pub w: f32,
}
```

##### Methods

###### `Vec4::all(v: f32) -> Self`
Creates a Vec4 with all components set to the same value.

```rust
pub const fn all(v: f32) -> Self
```

###### `Vec4::to_vec3(&self) -> Vec3`
Converts the Vec4 to a Vec3 by dropping the w component.

```rust
pub const fn to_vec3(&self) -> Vec3
```

###### `Vec4::dot(&self, other: Vec4) -> f32`
Calculates the dot product of two Vec4 values.

```rust
pub fn dot(&self, other: Vec4) -> f32
```

###### `Vec4::from_lerp(a: Vec4, b: Vec4, f: f32) -> Vec4`
Linearly interpolates between two Vec4 values.

```rust
pub fn from_lerp(a: Vec4, b: Vec4, f: f32) -> Vec4
```

#### `Mat4`
A 4x4 matrix.

```rust
#[derive(Clone, Copy, PartialEq, Debug)]
#[repr(C)]
pub struct Mat4 {
    pub v: [f32; 16],
}
```

##### Methods

###### `Mat4::identity() -> Mat4`
Creates an identity matrix.

```rust
pub const fn identity() -> Mat4
```

###### `Mat4::transpose(&self) -> Mat4`
Returns the transpose of the matrix.

```rust
pub fn transpose(&self) -> Mat4
```

###### `Mat4::perspective(fov_y: f32, aspect: f32, near: f32, far: f32) -> Mat4`
Creates a perspective projection matrix.

```rust
pub fn perspective(fov_y: f32, aspect: f32, near: f32, far: f32) -> Mat4
```

###### `Mat4::translation(v: Vec3) -> Mat4`
Creates a translation matrix.

```rust
pub const fn translation(v: Vec3) -> Mat4
```

###### `Mat4::scale(s: f32) -> Mat4`
Creates a scaling matrix.

```rust
pub const fn scale(s: f32) -> Mat4
```

###### `Mat4::transform_vec4(&self, v: Vec4) -> Vec4`
Transforms a Vec4 by the matrix.

```rust
pub fn transform_vec4(&self, v: Vec4) -> Vec4
```

###### `Mat4::mul(a: &Mat4, b: &Mat4) -> Mat4`
Multiplies two matrices.

```rust
pub fn mul(a: &Mat4, b: &Mat4) -> Mat4
```

###### `Mat4::invert(&self) -> Mat4`
Inverts the matrix.

```rust
pub fn invert(&self) -> Mat4
```

#### `Quat`
A quaternion for 3D rotations.

```rust
#[repr(C)]
#[derive(Copy, Clone, Debug, Default, PartialEq)]
pub struct Quat {
    pub x: f32,
    pub y: f32,
    pub z: f32,
    pub w: f32,
}
```

##### Methods

###### `Quat::multiply(a: &Quat, b: &Quat) -> Self`
Multiplies two quaternions.

```rust
pub fn multiply(a: &Quat, b: &Quat) -> Self
```

###### `Quat::invert(&self) -> Self`
Inverts the quaternion.

```rust
pub fn invert(&self) -> Self
```

###### `Quat::rotate_vec3(&self, v: &Vec3) -> Vec3`
Rotates a Vec3 by the quaternion.

```rust
pub fn rotate_vec3(&self, v: &Vec3) -> Vec3
```

###### `Quat::dot(&self, other: Quat) -> f32`
Calculates the dot product of two quaternions.

```rust
pub fn dot(&self, other: Quat) -> f32
```

###### `Quat::from_slerp(n: Quat, m: Quat, t: f32) -> Quat`
Spherical linear interpolation between two quaternions.

```rust
pub fn from_slerp(n: Quat, m: Quat, t: f32) -> Quat
```

### Functions

#### `vec2(x: f32, y: f32) -> Vec2`
Creates a new Vec2.

```rust
pub const fn vec2(x: f32, y: f32) -> Vec2
```

#### `vec3(x: f32, y: f32, z: f32) -> Vec3`
Creates a new Vec3.

```rust
pub const fn vec3(x: f32, y: f32, z: f32) -> Vec3
```

#### `vec4(x: f32, y: f32, z: f32, w: f32) -> Vec4`
Creates a new Vec4.

```rust
pub const fn vec4(x: f32, y: f32, z: f32, w: f32) -> Vec4
```

## Drawing System

### Structs

#### `Cx2d`
The 2D drawing context.

```rust
pub struct Cx2d { /* fields omitted */ }
```

#### `Cx3d`
The 3D drawing context.

```rust
pub struct Cx3d { /* fields omitted */ }
```

#### `DrawQuad`
A drawable quad element.

```rust
pub struct DrawQuad { /* fields omitted */ }
```

##### Methods

###### `DrawQuad::new() -> Self`
Creates a new DrawQuad instance.

```rust
pub fn new() -> Self
```

###### `DrawQuad::draw_quad(&mut self, cx: &mut Cx2d, walk: &Walk, layout: &Layout)`
Draws the quad.

```rust
pub fn draw_quad(&mut self, cx: &mut Cx2d, walk: &Walk, layout: &Layout)
```

#### `DrawText`
A drawable text element.

```rust
pub struct DrawText { /* fields omitted */ }
```

##### Methods

###### `DrawText::new() -> Self`
Creates a new DrawText instance.

```rust
pub fn new() -> Self
```

###### `DrawText::draw_text(&mut self, cx: &mut Cx2d, text: &str)`
Draws the text.

```rust
pub fn draw_text(&mut self, cx: &mut Cx2d, text: &str)
```

#### `DrawIcon`
A drawable icon element.

```rust
pub struct DrawIcon { /* fields omitted */ }
```

##### Methods

###### `DrawIcon::new() -> Self`
Creates a new DrawIcon instance.

```rust
pub fn new() -> Self
```

###### `DrawIcon::draw_icon(&mut self, cx: &mut Cx2d, icon: Option<LiveId>)`
Draws the icon.

```rust
pub fn draw_icon(&mut self, cx: &mut Cx2d, icon: Option<LiveId>)
```

#### `Walk`
Layout instructions for walking elements.

```rust
pub struct Walk { /* fields omitted */ }
```

#### `Layout`
Layout configuration for positioning elements.

```rust
pub struct Layout { /* fields omitted */ }
```

### Enums

#### `Flow`
Layout flow direction.

```rust
#[derive(Clone, Copy, PartialEq, Debug)]
pub enum Flow {
    Down,
    Right,
    // other variants omitted
}
```

#### `Align`
Alignment options.

```rust
#[derive(Clone, Copy, PartialEq, Debug)]
pub enum Align {
    Start,
    Center,
    End,
    // other variants omitted
}
```

#### `Size`
Size specifications.

```rust
#[derive(Clone, Copy, PartialEq, Debug)]
pub enum Size {
    Fixed(f32),
    Percentage(f32),
    Fit,
    Fill,
}