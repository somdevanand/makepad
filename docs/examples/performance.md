# Performance Optimization Examples

## Overview
This document provides examples and best practices for optimizing the performance of Makepad applications. These examples demonstrate common performance bottlenecks and how to address them.

## Efficient Data Structures

### Using ArrayVec for Small Collections
For small, fixed-size collections, use `ArrayVec` to avoid heap allocation:

```rust
use makepad_array_vec::ArrayVec;

// Instead of Vec for small collections
fn inefficient_approach() -> Vec<i32> {
    let mut vec = Vec::new();
    vec.push(1);
    vec.push(2);
    vec.push(3);
    vec
}

// More efficient approach with ArrayVec
fn efficient_approach() -> ArrayVec<i32, 10> {
    let mut vec: ArrayVec<i32, 10> = ArrayVec::new();
    vec.push(1);
    vec.push(2);
    vec.push(3);
    vec
}
```

### Using BTree for Sorted Data
For sorted data that requires frequent insertion and deletion, use `BTree`:

```rust
use makepad_btree::BTree;

// Efficient sorted data structure
fn btree_example() {
    let mut tree = BTree::new();
    
    // Insert data (O(log n))
    tree.insert(5, "five");
    tree.insert(3, "three");
    tree.insert(7, "seven");
    
    // Retrieve data (O(log n))
    if let Some(value) = tree.get(&5) {
        println!("Found: {}", value);
    }
    
    // Data is automatically sorted
    for (key, value) in tree.iter() {
        println!("{}: {}", key, value);
    }
}
```

## Drawing System Optimization

### Batch Drawing Operations
Group similar drawing operations to minimize state changes:

```rust
use makepad_draw::*;

fn inefficient_drawing(cx: &mut Cx2d) {
    // Inefficient: Creating new draw objects for each element
    for i in 0..100 {
        let mut draw_quad = DrawQuad::new();
        draw_quad.color = vec4(1.0, 0.0, 0.0, 1.0);
        draw_quad.draw_quad(cx, &Walk::default(), &Layout::default());
    }
}

fn efficient_drawing(cx: &mut Cx2d) {
    // Efficient: Reusing draw objects
    let mut draw_quad = DrawQuad::new();
    draw_quad.color = vec4(1.0, 0.0, 0.0, 1.0);
    
    for i in 0..100 {
        draw_quad.draw_quad(cx, &Walk::default(), &Layout::default());
    }
}
```

### Minimize Redraws
Only redraw elements that have changed:

```rust
struct OptimizedComponent {
    draw_quad: DrawQuad,
    needs_redraw: bool,
    color: Vec4,
}

impl OptimizedComponent {
    fn new() -> Self {
        Self {
            draw_quad: DrawQuad::new(),
            needs_redraw: true,
            color: vec4(1.0, 1.0, 1.0, 1.0),
        }
    
    fn set_color(&mut self, color: Vec4) {
        if self.color != color {
            self.color = color;
            self.needs_redraw = true;
        }
    }
    
    fn draw(&mut self, cx: &mut Cx2d) {
        if self.needs_redraw {
            self.draw_quad.color = self.color;
            self.draw_quad.draw_quad(cx, &Walk::default(), &Layout::default());
            self.needs_redraw = false;
        }
    }
}
```

### Efficient Layout Usage
Use appropriate layout settings to avoid unnecessary calculations:

```rust
use makepad_draw::*;

fn efficient_layout() -> Layout {
    Layout {
        flow: Flow::Down,
        align: Align::Center,
        // Fix sizes when possible to avoid recalculations
        walk: Walk {
            width: Size::Fixed(200.0),
            height: Size::Fixed(100.0),
            ..Default::default()
        },
        ..Default::default()
    }
}
```

## Memory Management

### Object Pooling
Reuse objects instead of creating and destroying them frequently:

```rust
use makepad_array_vec::ArrayVec;

struct ObjectPool<T, const N: usize> {
    objects: ArrayVec<T, N>,
    available: Vec<usize>,
}

impl<T: Default, const N: usize> ObjectPool<T, N> {
    fn new() -> Self {
        let mut objects = ArrayVec::new();
        let mut available = Vec::new();
        
        for i in 0..N {
            objects.push(T::default());
            available.push(i);
        }
        
        Self { objects, available }
    }
    
    fn acquire(&mut self) -> Option<&mut T> {
        if let Some(index) = self.available.pop() {
            Some(&mut self.objects[index])
        } else {
            None
        }
    }
    
    fn release(&mut self, _object: &T) {
        // In a real implementation, you'd track which object to release
        // This is a simplified example
    }
}
```

### Efficient String Handling
Minimize string allocations and copying:

```rust
use makepad_live_id::LiveId;

// Inefficient: Creating new strings frequently
fn inefficient_string_handling() -> Vec<String> {
    let mut strings = Vec::new();
    for i in 0..1000 {
        strings.push(format!("item_{}", i));
    }
    strings
}

// Efficient: Using LiveIds for identification
fn efficient_string_handling() -> Vec<LiveId> {
    let mut ids = Vec::new();
    for i in 0..1000 {
        ids.push(LiveId::from_str_num("item", i as u64));
    }
    ids
}
```

## Concurrency Optimization

### Async Task Management
Use futures efficiently to avoid blocking the main thread:

```rust
use makepad_futures::future::{Future, Poll, Context};
use makepad_futures::task::{Waker, RawWaker, RawWakerVTable};

// Non-blocking future implementation
struct NonBlockingFuture {
    completed: bool,
    progress: u32,
    total: u32,
}

impl Future for NonBlockingFuture {
    type Output = u32;

    fn poll(mut self: std::pin::Pin<&mut Self>, _cx: &mut Context<'_>) -> Poll<Self::Output> {
        if self.completed {
            Poll::Ready(self.progress)
        } else {
            // Simulate progress without blocking
            self.progress += 1;
            if self.progress >= self.total {
                self.completed = true;
                Poll::Ready(self.progress)
            } else {
                Poll::Pending
            }
        }
    }
}
```

### Channel Usage
Use channels efficiently for communication between tasks:

```rust
use makepad_futures::channel::mpsc;

fn efficient_channel_usage() {
    // Use bounded channels to apply backpressure
    let (sender, receiver) = mpsc::channel::<i32>(100);
    
    // Producer task
    let producer = async move {
        for i in 0..1000 {
            // This will block if the channel is full, applying natural backpressure
            if sender.send(i).await.is_err() {
                break;
            }
        }
    };
    
    // Consumer task
    let consumer = async move {
        while let Some(value) = receiver.next().await {
            // Process value
            process_value(value);
        }
    };
    
    // Run both tasks concurrently
    // futures::join!(producer, consumer);
}

fn process_value(value: i32) {
    // Process the value
}
```

## Math Operations Optimization

### Vector Operations
Use vector operations efficiently:

```rust
use makepad_math::*;

fn inefficient_vector_ops() -> Vec<Vec2> {
    let mut points = Vec::new();
    for i in 0..1000 {
        let x = i as f32;
        let y = (i * 2) as f32;
        points.push(Vec2 { x, y });
    }
    
    // Inefficient: Performing operations in a loop
    let mut results = Vec::new();
    for point in &points {
        let scaled = Vec2 {
            x: point.x * 2.0,
            y: point.y * 2.0,
        };
        results.push(scaled);
    }
    results
}

fn efficient_vector_ops() -> Vec<Vec2> {
    let mut points = Vec::new();
    for i in 0..1000 {
        let x = i as f32;
        let y = (i * 2) as f32;
        points.push(Vec2 { x, y });
    }
    
    // Efficient: Using vectorized operations
    points.iter().map(|p| *p * 2.0).collect()
}
```

### Matrix Operations
Optimize matrix operations:

```rust
use makepad_math::*;

fn efficient_matrix_ops() {
    // Precompute transformations when possible
    let translation = Mat4::translation(vec3(10.0, 20.0, 0.0));
    let rotation = Mat4::rotation(vec3(0.0, 0.0, 1.0)); // Rotate around Z axis
    let scale = Mat4::scale(2.0);
    
    // Combine transformations in the correct order
    let transform = Mat4::mul(&Mat4::mul(&scale, &rotation), &translation);
    
    // Apply to multiple points
    let points = vec![
        vec4(0.0, 0.0, 0.0, 1.0),
        vec4(1.0, 0.0, 0.0, 1.0),
        vec4(0.0, 1.0, 0.0, 1.0),
    ];
    
    let transformed_points: Vec<Vec4> = points
        .iter()
        .map(|p| transform.transform_vec4(*p))
        .collect();
}
```

## Best Practices Summary

### General Performance Tips
1. **Profile your code**: Use profiling tools to identify bottlenecks
2. **Minimize allocations**: Reuse objects and use stack allocation when possible
3. **Batch operations**: Group similar operations together
4. **Cache results**: Store computed values that don't change frequently
5. **Use appropriate data structures**: Choose data structures based on access patterns
6. **Avoid unnecessary work**: Only compute what you need, when you need it
7. **Leverage parallelism**: Use async operations and concurrency where appropriate

### Drawing Performance Tips
1. **Minimize state changes**: Batch drawing operations with similar properties
2. **Use efficient layouts**: Fix sizes when possible to avoid recalculations
3. **Only redraw what's needed**: Track which components need updating
4. **Optimize shaders**: Use simple shaders and minimize texture lookups
5. **Use texture atlases**: Combine multiple textures into a single atlas

### Memory Management Tips
1. **Use stack allocation**: Prefer ArrayVec and other stack-allocated structures
2. **Reuse objects**: Implement object pooling for frequently created/destroyed objects
3. **Minimize string operations**: Use LiveIds for identification instead of strings
4. **Drop data promptly**: Implement Drop traits to clean up resources quickly

### Concurrency Tips
1. **Use bounded channels**: Apply backpressure to prevent resource exhaustion
2. **Avoid blocking operations**: Use async operations to keep the UI responsive
3. **Limit concurrent tasks**: Don't spawn too many tasks simultaneously
4. **Handle errors gracefully**: Properly handle cancellation and errors in async code

By following these examples and best practices, you can significantly improve the performance of your Makepad applications while maintaining clean, readable code.