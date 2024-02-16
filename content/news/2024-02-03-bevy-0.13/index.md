+++
title = "Bevy 0.13"
date = 2024-02-03
[extra]
author = "Bevy Contributors"
image = "TODO.gif"
show_image = true
image_subtitle = "TODO"
image_subtitle_link = "TODO"

+++

Thanks to **TODO** contributors, **TODO** pull requests, community reviewers, and our [**generous sponsors**](/community/donate), we're happy to announce the **Bevy 0.13** release on [crates.io](https://crates.io/crates/bevy)!

For those who don't know, Bevy is a refreshingly simple data-driven game engine built in Rust. You can check out our [Quick Start Guide](/learn/book/getting-started/) to try it today. It's free and open source forever! You can grab the full [source code](https://github.com/bevyengine/bevy) on GitHub. Check out [Bevy Assets](https://bevyengine.org/assets) for a collection of community-developed plugins, games, and learning resources.
And to see what the engine has to offer hands-on, check out the entries in the [latest Bevy Jam](https://itch.io/jam/bevy-jam-4/entries), including the winner [That's a lot of beeeeees](https://andrewb330.itch.io/thats-a-lot-of-beeeeees)

To update an existing Bevy App or Plugin to **Bevy 0.13**, check out our [0.12 to 0.13 Migration Guide](/learn/migration-guides/0.12-0.13/).

Since our last release a few months ago we've added a _ton_ of new features, bug fixes, and quality of life tweaks, but here are some of the highlights:

<!-- more -->

* **Primitive shapes:** basic shapes are a core building block of both game engines and video games: we've added a polished, ready-to-use collection of them!
* **System stepping:** completely pause and advance through your game frame-by-frame or system-by-system to interactively debug game logic, all while rendering continues to update.
* **Dynamic queries:** refining queries from within systems is extremely expressive, and is the last big puzzle piece for runtime-defined types and third-party modding and scripting integration.
* **Automatically inferred command flush points:** tired of reasoning about where to put `apply_deferred` and confused about why your commands weren't being applied? Us too! Now, Bevy's scheduler uses ordinary `.before` and `.after` constraints and inspects the system parameters to automatically infer (and deduplicate) synchronization points.
* **Slicing, tiling and nine-patch 2D images:** ninepatch layout is a popular tool for smoothly scaling stylized tilesets and UIs. Now in Bevy!
* **Lightmaps:** the first step towards baked global illumination: a fast, popular and pretty lighting technique.
* **Animation interpolation modes:** Bevy now supports non-linear interpolation modes in exported glTF animations.

## Primitive Shapes

<div class="release-feature-authors">authors: @Jondolf, @NiseVoid, @aevyrie</div>

Geometric shapes are used all across game development, from primitive mesh shapes and debug gizmos to physics colliders and raycasting. Despite being so commonly used across several domains, Bevy hasn't really had any general-purpose shape representations.

This is changing in Bevy 0.13 with the introduction of first-party **primitive shapes**! They are lightweight geometric primitives designed for maximal interoperability and reusability, allowing Bevy and third-party plugins to use the same set of basic shapes and increase cohesion within the ecosystem. See the original [RFC][Primitive RFC] for more details.

The built-in [collection of primitives] is already quite sizeable:

| 2D                                  | 3D                                  |
| ----------------------------------- | ----------------------------------- |
| [`Rectangle`]                       | [`Cuboid`]                          |
| [`Circle`]                          | [`Sphere`]                          |
| [`Ellipse`]                         | -                                   |
| [`Triangle2d`]                      | -                                   |
| [`Plane2d`]                         | [`Plane3d`]                         |
| [`Line2d`]                          | [`Line3d`]                          |
| [`Segment2d`]                       | [`Segment3d`]                       |
| [`Polyline2d`], [`BoxedPolyline2d`] | [`Polyline3d`], [`BoxedPolyline3d`] |
| [`Polygon`], [`BoxedPolygon`]       | -                                   |
| [`RegularPolygon`]                  | -                                   |
| [`Capsule2d`]                       | [`Capsule3d`]                       |
| -                                   | [`Cylinder`]                        |
| -                                   | [`Cone`]                            |
| -                                   | [`ConicalFrustum`]                  |
| -                                   | [`Torus`]                           |

[More primitives] will be added in future releases.

Some use cases for primitive shapes include meshing, gizmos, bounding volumes, colliders, and ray casting functionality. Several of these have landed in 0.13 already!

[Primitive RFC]: https://github.com/bevyengine/rfcs/blob/main/rfcs/12-primitive-shapes.md
[collection of primitives]: https://dev-docs.bevyengine.org/bevy/math/primitives/index.html
[`Rectangle`]: https://dev-docs.bevyengine.org/bevy/prelude/struct.Rectangle.html
[`Cuboid`]: https://dev-docs.bevyengine.org/bevy/prelude/struct.Cuboid.html
[`Circle`]: https://dev-docs.bevyengine.org/bevy/prelude/struct.Circle.html
[`Sphere`]: https://dev-docs.bevyengine.org/bevy/prelude/struct.Sphere.html
[`Ellipse`]: https://dev-docs.bevyengine.org/bevy/prelude/struct.Ellipse.html
[`Triangle2d`]: https://dev-docs.bevyengine.org/bevy/prelude/struct.Triangle2d.html
[`Plane2d`]: https://dev-docs.bevyengine.org/bevy/prelude/struct.Plane2d.html
[`Plane3d`]: https://dev-docs.bevyengine.org/bevy/prelude/struct.Plane3d.html
[`Line2d`]: https://dev-docs.bevyengine.org/bevy/prelude/struct.Line2d.html
[`Line3d`]: https://dev-docs.bevyengine.org/bevy/prelude/struct.Line3d.html
[`Segment2d`]: https://dev-docs.bevyengine.org/bevy/prelude/struct.Segment2d.html
[`Segment3d`]: https://dev-docs.bevyengine.org/bevy/prelude/struct.Segment3d.html
[`Polyline2d`]: https://dev-docs.bevyengine.org/bevy/prelude/struct.Polyline2d.html
[`Polyline3d`]: https://dev-docs.bevyengine.org/bevy/prelude/struct.Polyline3d.html
[`BoxedPolyline2d`]: https://dev-docs.bevyengine.org/bevy/prelude/struct.BoxedPolyline2d.html
[`BoxedPolyline3d`]: https://dev-docs.bevyengine.org/bevy/prelude/struct.BoxedPolyline3d.html
[`Polygon`]: https://dev-docs.bevyengine.org/bevy/prelude/struct.Polygon.html
[`BoxedPolygon`]: https://dev-docs.bevyengine.org/bevy/prelude/struct.BoxedPolygon.html
[`RegularPolygon`]: https://dev-docs.bevyengine.org/bevy/prelude/struct.RegularPolygon.html
[`Capsule2d`]: https://dev-docs.bevyengine.org/bevy/prelude/struct.Capsule2d.html
[`Capsule3d`]: https://dev-docs.bevyengine.org/bevy/prelude/struct.Capsule3d.html
[`Cylinder`]: https://dev-docs.bevyengine.org/bevy/prelude/struct.Cylinder.html
[`Cone`]: https://dev-docs.bevyengine.org/bevy/prelude/struct.Cone.html
[`ConicalFrustum`]: https://dev-docs.bevyengine.org/bevy/prelude/struct.ConicalFrustum.html
[`Torus`]: https://dev-docs.bevyengine.org/bevy/prelude/struct.Torus.html
[More primitives]: https://github.com/bevyengine/bevy/issues/10572

### Rendering

Primitive shapes can be rendered using both meshes and gizmos. In this section, we'll take a closer look at the new APIs.

Below, you can see a cuboid and a torus rendered using meshes and gizmos. You can check out at all primitives that can be rendered in the new [Rendering Primitives] example.

![On the left: A cuboid rendered with gizmos. It consists of 12 white lines. On the right: A cuboid rendered with meshes. It consists of 6 white faces.](cuboids.png)

![On the left: A torus rendered with gizmos. It consists of many small rings, all connected by 4 big rings. On the right: A torus rendered with meshes. A shape that looks like a donut.](tori.png)

[Rendering Primitives]: https://bevyengine.org/examples/Math/render-primitives

#### Meshing

<div class="release-feature-authors">authors: @Jondolf</div>

Previous versions of Bevy have had types like [`Quad`], [`Box`], and [`UVSphere`] for creating meshes from basic shapes. These have been deprecated in favor of a builder-like API using the new geometric primitives.

Primitives that support meshing implement the [`Meshable`] trait. For some shapes, the [`mesh`][`mesh` method] method returns a [`Mesh`] directly:

```rust
let before = Mesh::from(Quad::new(Vec2::new(2.0, 1.0)));
let after = Rectangle::new(2.0, 1.0).mesh(); // Mesh::from also works
```

For most primitives however, it returns a builder for optional configuration:

```rust
// Create a circle mesh with a specified vertex count
let before = Mesh::from(Circle {
    radius: 1.0,
    vertices: 64,
});
let after = Circle::new(1.0).mesh().resolution(64).build();
```

Below are a few more examples of meshing with the new primitives.

```rust
// Icosphere
let before = meshes.add(
    Mesh::try_from(Icosphere {
        radius: 2.0,
        subdivisions: 8,
    })
    .unwrap()
);
let after = meshes.add(Sphere::new(2.0).mesh().ico(8).unwrap());

// Cuboid
// (notice how Assets::add now also handles mesh convertion automatically)
let before = meshes.add(Mesh::from(shape::Box::new(2.0, 1.0, 1.0)));
let after = meshes.add(Cuboid::new(2.0, 1.0, 1.0));

// Plane
let before = meshes.add(Mesh::from(Plane::from_size(5.0)));
let after = meshes.add(Plane3d::default().mesh().size(5.0, 5.0));
```

With the addition of the primitives, meshing is also supported for more shapes, like [`Ellipse`], [`Triangle2d`], and [`Capsule2d`]. However, note that meshing is not yet implemented for all primitives, such as [`Polygon`] and [`Cone`].

Below you can see some meshes in the [`2d_shapes`] and [`3d_shapes`] examples.

![An example with 2D mesh shapes](2d_shapes.png)

![An example with 3D mesh shapes](3d_shapes.png)

Some default values for mesh shape dimensions have also been changed to be more consistent.

[`Quad`]: https://dev-docs.bevyengine.org/bevy/prelude/shape/struct.Quad.html
[`Box`]: https://dev-docs.bevyengine.org/bevy/prelude/shape/struct.Box.html
[`UVSphere`]: https://dev-docs.bevyengine.org/bevy/prelude/shape/struct.UVSphere.html
[`Meshable`]: https://dev-docs.bevyengine.org/bevy/prelude/trait.Meshable.html
[`mesh` method]: https://dev-docs.bevyengine.org/bevy/prelude/trait.Meshable.html#tymethod.mesh
[`Mesh`]: https://dev-docs.bevyengine.org/bevy/prelude/struct.Mesh.html
[`2d_shapes`]: https://bevyengine.org/examples/2D%20Rendering/2d-shapes/
[`3d_shapes`]: https://bevyengine.org/examples/3D%20Rendering/3d-shapes/

#### Gizmos

<div class="release-feature-authors">authors: @RobWalt</div>

Primitives can also be rendered with [`Gizmos`]. There are two new generic methods:

* [`gizmos.primitive_2d(primitive, position, angle, color)`][`primitive_2d`]
* [`gizmos.primitive_3d(primitive, position, rotation, color)`][`primitive_3d`]

Some primitives can have additional configuration options similar to existing [`Gizmos`] drawing methods.
For example, calling [`primitive_3d`] with a [`Sphere`] returns a [`SphereBuilder`], which offers a `segments` method
to control the level of detail of the sphere.

```rust
let sphere = Sphere { radius };
gizmos
    .primitive_3d(sphere, center, rotation, color)
    .segments(segments);
```

[`Gizmos`]: https://dev-docs.bevyengine.org/bevy/gizmos/prelude/struct.Gizmos.html
[`primitive_2d`]: https://dev-docs.bevyengine.org/bevy/gizmos/prelude/trait.GizmoPrimitive2d.html
[`primitive_3d`]: https://dev-docs.bevyengine.org/bevy/gizmos/prelude/trait.GizmoPrimitive2d.html
[`SphereBuilder`]: https://dev-docs.bevyengine.org/bevy/gizmos/primitives/dim3/struct.SphereBuilder.html

### Bounding Volumes

<div class="release-feature-authors">authors: @NiseVoid, @Jondolf</div>

In game development, spatial checks have several valuable use cases, such as getting all entities that are in the camera's view frustum or near the player, or finding pairs of physics objects that might be intersecting. To speed up such checks, bounding volumes are used to approximate more complex shapes.

Bevy 0.13 adds some new publicly available bounding volumes: [`Aabb2d`], [`Aabb3d`], [`BoundingCircle`], and [`BoundingSphere`]. These can be created manually, or generated from primitives shapes.

Each bounding volume implements the [`BoundingVolume`] trait, providing some general functionality and helpers. The [`IntersectsVolume`] trait can be used to test for intersections with these volumes. This trait is implemented for bounding volumes themselves, so you can test for intersections between them. This is supported between all existing bounding volume types, but only those in the same dimension.

Here is an example of how bounding volumes are constructed, and how an intersection test is performed:

```rust
// We create an axis-aligned bounding box that is centered at position
let position = Vec2::new(100., 50.);
let half_size = Vec2::splat(20.);
let aabb = Aabb2d::new(position, half_size);

// We create a bounding circle that is centered at position
let position = Vec2::new(80., 70.);
let radius = 30.;
let bounding_circle = BoundingCircle::new(position, radius);

// We check if the volumes are intersecting
let intersects = bounding_circle.intersects(&aabb);
```

There are also two traits for the generation of bounding volumes: [`Bounded2d`] and [`Bounded3d`]. These are implemented for the new primitive shapes, so you can easily compute bounding volumes for them:

```rust
// We create a primitive, a hexagon in this case
let hexagon = RegularPolygon::new(50., 6);

let translation = Vec2::new(50., 200.);
let rotation = PI / 2.; // Rotation in radians

// Now we can get an Aabb2d or BoundingCircle from this primitive.
// These methods are part of the Bounded2d trait.
let aabb = hexagon.aabb_2d(translation, rotation);
let circle = hexagon.bounding_circle(translation, rotation);
```

[`Aabb2d`]: https://dev-docs.bevyengine.org/bevy/math/bounding/struct.Aabb2d.html
[`Aabb3d`]: https://dev-docs.bevyengine.org/bevy/math/bounding/struct.Aabb3d.html
[`BoundingCircle`]: https://dev-docs.bevyengine.org/bevy/math/bounding/struct.BoundingCircle.html
[`BoundingSphere`]: https://dev-docs.bevyengine.org/bevy/math/bounding/struct.BoundingSphere.html
[`BoundingVolume`]: https://dev-docs.bevyengine.org/bevy/math/bounding/trait.BoundingVolume.html
[`IntersectsVolume`]: https://dev-docs.bevyengine.org/bevy/math/bounding/trait.IntersectsVolume.html
[`Bounded2d`]: https://dev-docs.bevyengine.org/bevy/math/bounding/trait.Bounded2d.html
[`Bounded3d`]: https://dev-docs.bevyengine.org/bevy/math/bounding/trait.Bounded3d.html

#### Ray Casting and Volume Casting

The bounding volumes also support basic ray casting and volume casting. Ray casting tests if a bounding volume intersects with a given ray, cast from an origin in a direction, until a maximum distance. Volume casts work similarly, but function as if moving a volume along the ray.

This functionality is provided through the new [`RayCast2d`], [`RayCast3d`], [`AabbCast2d`], [`AabbCast3d`], [`BoundingCircleCast`], and [`BoundingSphereCast`] types. They can be used to check for intersections against bounding volumes, and to compute the distance from the origin of the cast to the point of intersection.

Below, you can see ray casting, volume casting, and intersection tests in action:

<video controls><source src="bounding_intersections.mp4" type="video/mp4"/></video>

To make it easier to reason about ray casts in different dimensions, the old [`Ray`] type has also been split into [`Ray2d`] and [`Ray3d`]. The new [`Direction2d`] and [`Direction3d`] types are used to ensure that the ray direction remains normalized, providing a type-level guarantee that the vector is always unit-length. These are already in use in some other APIs as well, such as for some primitives and gizmo methods.

[`RayCast2d`]: https://dev-docs.bevyengine.org/bevy/math/bounding/struct.RayCast2d.html
[`RayCast3d`]: https://dev-docs.bevyengine.org/bevy/math/bounding/struct.RayCast3d.html
[`AabbCast2d`]: https://dev-docs.bevyengine.org/bevy/math/bounding/struct.AabbCast2d.html
[`AabbCast3d`]: https://dev-docs.bevyengine.org/bevy/math/bounding/struct.AabbCast3d.html
[`BoundingCircleCast`]: https://dev-docs.bevyengine.org/bevy/math/bounding/struct.BoundingCircleCast.html
[`BoundingSphereCast`]: https://dev-docs.bevyengine.org/bevy/math/bounding/struct.BoundingSphereCast.html
[`Ray`]: https://docs.rs/bevy/0.12.1/bevy/math/struct.Ray.html
[`Ray2d`]: https://dev-docs.bevyengine.org/bevy/math/struct.Ray2d.html
[`Ray3d`]: https://dev-docs.bevyengine.org/bevy/math/struct.Ray3d.html
[`Direction2d`]: https://dev-docs.bevyengine.org/bevy/math/primitives/struct.Direction2d.html
[`Direction3d`]: https://dev-docs.bevyengine.org/bevy/math/primitives/struct.Direction3d.html

## System Stepping

<div class="release-feature-authors">authors: @dmlary</div>

The new system stepping feature (`bevy_debug_stepping`) adds debugger-style
execution control for systems. The [`Stepping`] resource can control which
systems within a schedule execute each frame, and provides step, break, and
continue facilities to enable live debugging.

The `Stepping` resource is configured with a list of schedules that it will
control execution of when stepping is enabled. The collection of systems
within those schedules can be thought of as the stepping frame. It can take
multiple step or continue calls to traverse through the entire stepping frame.
During this time, all schedules that have not been added to `Stepping` are
executing each frame. This allows rendering & input handling to continue while
stepping through systems.

### Configuration

First, configure the [`Stepping`] resource and add it to the world:

```rust
// Create a new Stepping resource, and add schedules to debug
let mut stepping = Stepping::new();
stepping.add_schedule(Update);
stepping.add_schedule(FixedUpdate);
// Stepping is disabled by default,
// even when the resource is inserted.
// Feature flags, dev consoles and obscure hotkeys all work great.
#[cfg(feature = "my_stepping_flag")]
stepping.enable();

// add the Stepping resource to the world
app.insert_resource(stepping);
```

Systems within the added schedules will not be run unless we're performing
a system step, continuing the stepping frame, or the system has been exempt
from stepping.

### Execution Control: System Step & Continue Frame

While stepping is enabled, the `Stepping` resource tracks its location within
the stepping frame, maintaining a stepping cursor pointing at the next system
to be executed.
There are two ways systems get executed while stepping is enabled: system step,
and continue frame.

System step (`Stepping::step_frame()`) runs the system at the stepping
cursor, and advances the cursor during the next render frame.
This is useful to see individual changes made by
systems, and see the state of the world prior to executing a system

Continue frame (`Stepping::continue_frame()`) will execute systems starting
from the stepping cursor to the end of the stepping frame during the next frame.
It may stop before the end of the stepping frame if it encounters a system with
a breakpoint. This is useful for advancing quickly through an entire frame,
getting to the start of the next frame, or in combination with breakpoints.

In this video we demonstrate system step & continue frame on the breakout
example with a custom `egui` interface.  The stepping cursor can be seen moving through
the systems list as we click the `step` button.  When the `continue` button is
clicked, you can see the game progress one stepping frame for each click.

<video controls><source src="stepping-step-continue.mp4" type="video/mp4"/></video>

### Breakpoints

When a schedule grows to a certain point, it can take a long time to step
through every system in the schedule just to see the effects of a few systems.
In this case, stepping provides system breakpoints.

You can set a breakpoint on those systems you care about, then use
`Stepping::continue_frame()` to run systems starting at the stepping cursor
until a breakpoint is encountered, or the end of the stepping frame.
If the stepping cursor points at a system with a breakpoint when you call
`Stepping::continue_frame()` that system will run.  This allows you to set
breakpoints through a system, and repeatedly continue the frame to stop prior
to each system of interest.

In this video of the breakout example, we add a breakpoint to
`check_for_collisions()` so we can verify the collision detection & handling
behavior each frame without stepping through all the other systems.

The video shows the stepping cursor moves from the start of the stepping frame
to `check_for_collisions()` the first time we click `continue` in the ui.  On
the next click, `check_for_collisions()` and all remaining systems are run,
moving the cursor back up to the start of the stepping frame.

<video controls><source src="stepping-breakpoint.mp4" type="video/mp4"/></video>

### Disabling Systems during Stepping

During debugging, it can be helpful to disable systems to narrow down the
source of the problem. `Stepping::never_run()` and `Stepping::never_run_node()`
can be used to disable systems while stepping is enabled. Note that disabling
systems in this manner only has an effect while stepping is enabled. When
stepping is disabled, all disabled systems will resume executing.

In this video of the breakout example, we disable the `check_for_collisions()`
system and use continue frame to move the ball into the center of the blocks,
then re-enable the system to have fun destroying all the blocks from the
inside.

<video controls><source src="stepping-disable-system.mp4" type="video/mp4"/></video>

### Excluding Systems from Stepping

It may be necessary to ensure some systems still run while stepping is enabled.
While best-practice is to have them in a schedule that has not been added to
the `Stepping` resource, it is possible to configure systems to always run
while stepping is enabled. This is primarily useful for event & input handling
systems.

Systems can be configured to always run by calling
`Stepping::always_run()`, or `Stepping::always_run_node()`.
When a system is configured to always run, it will run each rendering frame
even when stepping is enabled.

In this video of the breakout example the `move_paddle()` system is configured
to always run.  We then use the arrow keys to move the paddle while the rest
of the game systems are stopped by stepping.  This allows us to position the
paddle precisely before continuing frame-by-frame as the ball impacts the
paddle.

<video controls><source src="stepping-run-always.mp4" type="video/mp4"/></video>

### Limitations

There are some limitations in this initial implementation of stepping:

* Any system that reads events likely will not step properly
  * Frames still advance normally while stepping is enabled
  * Events can be cleared before a stepped system can read them
  * Best approach here is to configure event-based systems to always run,
    or put them in a schedule not added to `Stepping`
  * Continue with breakpoints may also work in this scenario
* Conditional systems may not run as expected when stepping
  * Similar to event-based systems, if the run condition is true for only a short
    time, system may not run when stepped

### Detailed Examples

* [Text-based stepping example](https://github.com/bevyengine/bevy/blob/main/examples/ecs/system_stepping.rs)
* Non-interactive [bevy UI example stepping plugin](https://github.com/bevyengine/bevy/blob/main/examples/games/stepping.rs) used in the breakout example
* Interactive [egui stepping plugin](https://gist.github.com/dmlary/3fd57ebf1f88bb9afa8a6604737dac97) used in demo videos

[`Stepping`]: https://docs.rs/bevy/0.13.0/bevy/ecs/schedule/stepping/Stepping.html

## Query Transmutation

<div class="release-feature-authors">authors: @hymm, james-j-obrien</div>

Have you every wanted to pass a query to a function, but instead of having a
`Query<&Transform>` you have a `Query<(&Transform, &Velocity), With<Enemy>>`?
Well now you can by using the `Query::transmute_lens` method. Query transmutes
allow you to change a query into different query types as long as the
components accessed are a subset of the original query. If you do try to access
data that is not in the original query, this method will panic.

```rust
fn reusable_function(lens: &mut QueryLens<&Transform>) {
    let query = lens.query();
    // do something with the query...
}

// We can use the function in a system that takes the exact query.
fn system_1(mut query: Query<&Transform>) {
    reusable_function(&mut query.as_query_lens());
}

// We can also use it with a query that does not match exactly
// by transmuting it.
fn system_2(mut query: Query<(&mut Transform, &Velocity), With<Enemy>>) {
    let mut lens = query.transmute_lens::<&Transform>();
    reusable_function(&mut lens);
}
```

Note that the `QueryLens` will still iterate over the same entities as the
original `Query` it is derived from. A `QueryLens<&Transform>` taken from
a `Query<(&Transform, &Velocity)>`, will only include the `Transform` of
entities with both `Transform` and `Velocity` components.

Besides removing parameters you can also change them in limited ways to the
different smart pointer types. One of the more useful is to change a
`& mut` to a `&`. See the [documentation](https://docs.rs/bevy/latest/bevy/ecs/system/struct.Query.html#method.transmute_lens)
for more details.

One thing to take into consideration is the transmutation is not free.
It works by creating a new state and copying a bunch of the cached data
inside the original query. It's not a expensive operation, but you should
probably avoid doing it inside a hot loop.

## `WorldQuery` Trait Split

<div class="release-feature-authors">authors: @wainwrightmark @taizu-jin</div>

A [`Query`] has two type parameters: one for the the data to be fetched, and a second optional one for the filters.

In previous versions of Bevy both parameters simply required [`WorldQuery`]: there was nothing stopping you from using types intended as filters in the data position (or vice versa).

Apart from making the type signature of the [`Query`] items more complicated (see example below) this usually worked fine as most filters had the same behaviour in either position.

Unfortunately this was not the case for [`Changed`] and [`Added`] which had different (and undocumented) behaviour in the data position and this could lead to bugs in user code.

To allow us to prevent this type of bug at compile time, the [`WorldQuery`] trait has been replaced by two traits: [`QueryData`] and [`QueryFilter`]. The data parameter of a [`Query`] must now be [`QueryData`] and the filter parameter must be [`QueryFilter`].

Most user code should be unaffected or easy to migrate.

```rust
// Probably a subtle bug: `With` filter in the data position - will not compile in 0.13
fn my_system(query: Query<(Entity, With<ComponentA>)>)
{
    // The type signature of the query items is `(Entity, ())`, which is usable but unwieldy
  for (entity, _) in query.iter(){
  }
}

// Iidiomatic, compiles in both 0.12 and 0.13
fn my_system(query: Query<Entity, With<ComponentA>>)
{
  for entity in query.iter(){
  }
}
```

[`Query`]: https://dev-docs.bevyengine.org/bevy/ecs/system/struct.Query.html
[`WorldQuery`]: https://docs.rs/bevy/0.12.0/bevy/ecs/query/trait.WorldQuery.html
[`Changed`]: https://dev-docs.bevyengine.org/bevy/ecs/query/struct.Changed.html
[`Added`]: https://dev-docs.bevyengine.org/bevy/ecs/query/struct.Added.html
[`QueryData`]: https://dev-docs.bevyengine.org/bevy/ecs/query/trait.QueryData.html
[`QueryFilter`]: https://dev-docs.bevyengine.org/bevy/ecs/query/trait.QueryFilter.html

## Automatically insert `apply_deferred` systems

<div class="release-feature-authors">authors: @hymm</div>

When writing gameplay code, you might commonly have one system that wants to
immediately see the effects of commands queued in another system.
Before 0.13, you would have to manually insert an `apply_deferred` system between the two,
a special system which causes those commands to be applied when encountered.
Bevy now detects when a system with commands
is ordered relative to other systems and inserts the `apply_deferred` for you.

```rust
// Before 0.13
app.add_systems(
    Update,
    (
        system_with_commands,
        apply_deferred,
        another_system,
    ).chain()
);
```

```rust
// After 0.13
app.add_systems(
    Update,
    (
        system_with_commands,
        another_system,
    ).chain()
);
```

This was a common beginner footgun: if two systems are ordered, shouldn't the second always see the results of the first?

Automatically inserted `apply_deferred` systems are optimized by automatically merging them if
possible. In most cases, it is recommended to remove all manually inserted
`apply_deferred` systems, as allowing Bevy to insert and merge these systems as needed will
usually be both faster and involve less boilerplate.

```rust
// This will only add one apply_deferred system.
app.add_systems(
    Update,
    (
        (system_1_with_commands, system_2).chain(),
        (system_3_with_commands, system_4).chain(),
    )
);
```

If this new behavior does not work for you, please consult the migration guide.
There are several new APIs that allow you to opt-out.

## More Flexible One-Shot Systems

<div class="release-feature-authors">authors: @Nathan-Fenner</div>

In 0.12, we introduced [one-shot systems](https://bevyengine.org/news/bevy-0-12/#one-shot-systems), a handy way to call systems on demand without having to add them to a schedule.
The initial implementation had some limitations with regards to what systems could and could not be used as one-shot systems.
These limitations have since been resolved, starting with one-shot systems with input and output.

```rust

fn increment_sys(In(increment_by): In<i32>, mut counter: ResMut<Counter>) -> i32 {
    counter.0 += increment_by;
    counter.0
}

let mut world = World::new();
let id = world.register_system(increment_sys);

world.insert_resource(Counter(1));
let count_one = world.run_system_with_input(id, 5).unwrap(); // increment counter by 5 and return 6
let count_two = world.run_system_with_input(id, 2).unwrap(); // increment counter by 2 and return 8
```

Using either `world.run_system_with_input(system_id, input)` or `commands.run_system_with_input(system_id, input)`, you can now supply input parameters to systems that accept them. Additionally, both `world.run_system` and `world.run_system_with_input` now return system output as `Ok(output)`. Note that output cannot be returned when calling the system through commands, because of their deferred nature.

Some smaller improvements to one-shot systems include registering boxed systems with `register_boxed_system` (which was shipped in 0.12.1, but didn't get a blog post) and the ability to register exclusive systems as one-shot systems.

```rust
world.register_system(|world: &mut World| { /* do anything */ });
```

All these improvements round out one-shot systems significantly: they should now work just like any other Bevy system.

## Dynamic Queries

<div class="release-feature-authors">authors: @james-j-obrien, @jakobhellermann, @Suficio</div>

In Bevy's ECS, queries use a type-powered DSL. The full type of the query (
what component to access, which filter to use) must be specified at compile time.

Sometimes, we can't easily know what data the query wants to access at compile time.
A UI with a dynamic list filtering by component, bindings for a scripting language,
_entity relationships_ (more on this later), all of those, are impossible to accomplish without
creating queries at runtime.

Dynamic queries lift this restriction.

The standard way of defining a `Query` is by using them as system parameters:

```rust
fn take_damage(mut player_health: Query<(Entity, &mut Health), With<Player>>) {
    // ...
}
```

**This won't change.** And for most (if not all)  gameplay use cases, you will
continue to happily use the delightfully simple [`Query`] API.

However, consider this situation: as a game or mod developer I want to list entities
with a specific component through a text prompt. Similarly to how the Quake console works.
What would that look like?

```rust
#[derive(Resource)]
struct UserQuery(String);

// user_query is entered as a text prompt by the user when the game is running.
// In a system, it's quickly apparent that we can't use `Query`.
fn list_entites_system(user_query: Res<UserQuery>, query: Query<FIXME, With<FIXME>>) {}

// Even when using the more advanced `World` API, we are stuck.
fn list_entities(user_query: String, world: &mut World) {
    // FIXME: what type goes here?
    let query = world.query::<FIXME>();
}
```

It's impossible to chose a type based on the value of `user_query`!
[`QueryBuilder`] solves this problem.

```rust
fn list_entities(
    user_query: String,
    type_registry: &TypeRegistry,
    world: &mut World,
) -> Option<()> {
    let name = user_query.split(' ').next()?;
    let type_id = type_registry.get_with_short_type_path(name)?.type_id();
    let component_id = world.components().get_id(type_id)?;

    let query = QueryBuilder::<FilteredEntityRef>::new(&mut world)
        .ref_id(component_id)
        .build();

    for entity_ref in query.iter(world) {
        let ptr = entity_ref.get_by_id(component_id);
        // Convert `ptr` into a `&dyn Reflect` and use it.
    }
    Some(())
}
```

It is still an error-prone, complex, and unsafe API, but it makes something that was previously
impossible possible.
We expect third party crates to provide convenient wrappers around the `QueryBuilder` API,
some of which will undoubtedly make their way upstream.

[`QueryBuilder`] is here for people who need queries with runtime access specification,
maybe you need to:

* Add a runtime filter to [`bevy-inspector-egui`]'s entity inspector.
* Define queries in a scripting language such as Lua or JavaScript.
* Define new components from a scripting language and query them.
* Add a [Quake-style console] to modify or query components from a prompt at runtime.
* Create an [editor with remote capabilities].
* And these are only the plans we've heard about so far!

### Relations

We mentioned _entity relationships_ earlier. What are relations? They are a way to associate
entities to other entities. For example, the `Parent` and `Children` components
in `bevy_hierarchy` are primitive relations.
By storing `Entity` inside of a component type, we can describe a link between entities.

`bevy_hierarchy` is fairly robust, but if you yourself want to create your own
relation (say, a group of units in an RTS game), the road ahead is a tar pit of footguns
and synchronization bugs.

_Entity relationships_ encode relations in the ECS. They are a staple of the [Flecs] C
ECS. This makes it _a pleasure_ to describe relations,
as opposed to the endless difficulties presented in most ECS's (including Bevy).

Sander Mertens, of Flecs fame, [describes in details] the prerequisites for an
entity relationship implementation.
One of those prerequisites is the ability to use specific entity ids as query parameters.
Dynamic queries allow just that.

### A long wait

Given how useful dynamic queries are, you might be wondering why Bevy didn't have them already.
But dynamic queries have a long history: they date back from **November 2022**,
when Suficio proposed [a simple change]. It was deemed too unsafe, [a counter-proposal]
was made by Jakob Hellermann. It was stalled due to complexity and the lack of qualified and
interested reviewers. They were finally merged in **January 2024**
thanks to [James O'Brien's stupendous effort][The dynamic query pull request].

For an in-depth technical and historical breakdown of dynamic queries, check
out [this GitHub discussion thread](https://github.com/bevyengine/bevy/discussions/9816).

They have been _a long time_ coming and they are finally here!

[`bevy-inspector-egui`]: https://crates.io/crates/bevy-inspector-egui
[Quake-style console]: https://github.com/doonv/bevy_dev_console
[editor with remote capabilities]: https://makeshift-bevy-web-editor.vercel.app/
[a simple change]: https://github.com/bevyengine/bevy/pull/6240
[The dynamic query pull request]: https://github.com/bevyengine/bevy/pull/9774
[a counter-proposal]: https://github.com/bevyengine/bevy/pull/6390
[`QueryBuilder`]: https://dev-docs.bevyengine.org/bevy/ecs/prelude/struct.QueryBuilder.html
[describes in details]: https://ajmmertens.medium.com/a-roadmap-to-entity-relationships-5b1d11ebb4eb
[Flecs]: https://www.flecs.dev/flecs/

## Events Live Longer

Events are a useful tool for passing data into systems and between systems.

Internally, Bevy events are double-buffered, so a given event will be silently dropped once the buffers have swapped twice.
The `Events<T>` resource is setup this way so events are dropped after a predictable amount of time, preventing their queues from growing forever and causing a memory leak.

Before 0.12.1, event queues were swapped every update (i.e. every frame).
That was an issue for games with logic in `FixedUpdate`, since it meant events would normally disappear before systems in the next `FixedUpdate` could read them.

Bevy 0.12.1 changed the swap cadence to "every update that runs `FixedUpdate` one or more times" (only if the `TimePlugin` is installed).
This change did resolve the original problem, but it then caused problems in the other direction.
Users were surprised to learn some of their systems with `run_if` conditions would iterate much older events than expected.
(In hindsight, we should have considered it a breaking change and postponed it until this release.)
The change also introduced a bug (fixed in this release) where only one type of event was actually being dropped.

One proposed future solution to this lingering but unintended coupling between `Update` and `FixedUpdate` is to use event timestamps to change the default range of events visible by `EventReader<T>`.
That way systems in `Update` would skip any events older than a frame while systems in `FixedUpdate` could still see them.

For now, the `<=0.12.0` behavior can be recovered by simply removing the `EventUpdateSignal` resource.

```rust
fn main() {
    let mut app = App::new()
        .add_plugins(DefaultPlugins);
    
    /* ... */

    // If this resource is absent, events are dropped the same way as <=0.12.0.
    app.world.remove_resource::<EventUpdateSignal>();
    
    /* ... */

    app.run();
}
```

## Texture atlas rework

<div class="release-feature-authors">authors: @ManevilleF</div>

Texture atlases are a tool used to efficiently combine multiple images into a single larger texture called an atlas.

Bevy 0.13 significantly reworks them, reducing boilerplate and making the feature more data-oriented.
Say goodbye to `TextureAtlasSprite` and `UiTextureAtlasImage` components (and the corresponding `Bundle` types):
texture atlasing is now enabled by adding a single _additional_ component to your sprite and image entities: `TextureAtlas`.

### Why this change

Texture atlases (sometimes called sprite sheets) simply to draw a custom _section_ of the texture.
The new `TextureAtlas` represents that behaviour, storing:

* a `Handle<TextureAtlasLayout>`, an asset mapping an index to a `Rect` section of a texture
* a `usize` index defining which section `Rect` of the layout we want to display

## Texture Slicing and Tiling

<div class="release-feature-authors">authors: @ManevilleF</div>

3D rendering gets a lot of love, but 2D features matter too!
We're pleased to add CPU-based _slicing and tiling_ to both `bevy_sprite` and `bevy_ui` in Bevy 0.13!

This behavior is controlled by a new optional component: [`ImageScaleMode`].

[`ImageScaleMode`]: https://dev-docs.bevyengine.org/bevy/prelude/enum.ImageScaleMode.html

### 9 slicing

Adding `ImageScaleMode::Sliced(_)` to an entity with a sprite or UI bundle enables [9 slicing](https://en.wikipedia.org/wiki/9-slice_scaling),
keeping the image in proportions in resize, avoiding stretching of the texture.

![Stretched Vs Sliced texture](slice_vs_stretched.png)

This is very useful for UI, allowing your pretty textures to look right even as the size of your element changes.

![Sliced Buttons](ui_slice.png)
> Border texture by [Kenney's](https://kenney.nl/assets/fantasy-ui-borders)

Configuration:

```rust
commands.spawn((
    SpriteSheetBundle::default(),
    ImageScaleMode::Sliced(TextureSlicer {
        // The image borders are 20 pixels in every direction
        border: BorderRect::square(20.0),
        // we don't stretch the coners more than their actual size (20px)
        max_corner_scale: 1.0,
        ..default()
    }),
));
```

### Tiling

Adding `ImageMode::Tiled { .. }` to your 2D sprite entities enables _texture tiling_: repeating the image until their entire area is fulled.
This is commonly used for backgrounds and surfaces.

<video controls><source src="logo_tiling.mp4" type="video/mp4"/></video>

Configuration:

```rust
commands.spawn((
    SpriteSheetBundle::default(),
    ImageScaleMode::Tiled {
        // The image will repeat horizontally
        tile_x: true,
        // The image will repeat vertically
        tile_y: true,
        // The texture will repeat if the drawing rect is larger than the image size
        stretch_value: 1.0,
    },
));
```

## Exposure Settings

<div class="release-feature-authors">authors: @TODO</div>

TODO.

## Basic baked global illumination

<div class="release-feature-authors">authors: @pcwalton</div>

Computing lighting in real time is expensive;
but for elements of a scene that never move (like rooms or terrain),
we can get prettier lighting and shadows for cheaper by computing it ahead of time using **global illumination**,
then storing the results in a "baked" form that never changes.
Global illumination is a more sophisticated (and expensive) approach to lighting that often uses ray tracing.
Unlike Bevy's default lighting tools, it takes light bouncing off of other objects into account,
producing more realistic effects through the inclusion of indirect light.

**Lightmaps**, textures that store pre-computed results of this global illumination, have been a mainstay of real-time graphics for decades.
The [lightmap PR] adds preliminary support for them to Bevy.
Like the [lightmaps example] shows, just load in your baked lightmap image, and then insert a [`Lightmap`] component on the corresponding mesh.

![A simple scene rendered using baked light maps in Bevy 0.13](light_map.png)

Following up on this work, the [irradiance volumes PR] added support for a second form of baked global illumination.
**Irradiance volumes** (or voxel global illumination) is a technique used for approximating indirect light by first dividing
a scene into cubes (voxels), then sampling the amount of light present at the center of each of those voxels.
This light is then added to objects within that space as they move through it, changing the ambient light level on those objects appropriately.
As the PR helpfully explains, we've chosen to use the ambient cubes algorithm for this, based on Half Life 2.
This allows us to match Blender's [Eevee renderer], giving users a simple and free path to creating nice-looking irradiance volumes for their own scenes.

Note that this work does not include any Bevy-native way to precompute global illumination!
Instead for now this should be computed externally in a tool like [The Lightmapper], and then loaded into Bevy.

[lightmap PR]: https://github.com/bevyengine/bevy/pull/10231
[`Lightmap`]: https://dev-docs.bevyengine.org/bevy/pbr/struct.Lightmap.html
[lightmaps example]: https://github.com/bevyengine/bevy/blob/main/examples/3d/lightmaps.rs
[irradiance volumes PR]: https://github.com/bevyengine/bevy/pull/10268
[The Lightmapper]: https://github.com/Naxela/The_Lightmapper
[Eevee renderer]: https://docs.blender.org/manual/en/latest/render/eevee/index.html

## Minimal Reflection Probes

<div class="release-feature-authors">authors: @pcwalton</div>

**Environment maps** are 2D textures used to simulate lighting, reflection, and skyboxes in a 3D scene.
**Reflection probes** generalize environment maps to allow for multiple environment maps in the same scene, each of which has its own axis-aligned bounding box.
This is a standard feature of physically-based renderers and was inspired by the [corresponding feature in Blender's Eevee renderer].

In the [reflection probes PR], we've added basic support for these, laying the groundwork for pretty, high-performance reflections in Bevy games.
Like with the baked global illumination work discussed above, these must currently be precomputed externally, then imported into Bevy.
As discussed in the PR, there are quite a few caveats: WebGL2 support is effectively non-existent, sharp and sudden transitions will be observed because there's no blending,
and all cubemaps in the world of a given type (diffuse or specular) must have the same size, format, and mipmap count.

[reflection probes PR]: https://github.com/bevyengine/bevy/pull/11366
[corresponding feature in Blender's Eevee renderer]: https://docs.blender.org/manual/en/latest/render/eevee/light_probes/reflection_cubemaps.html

## Lights work with `RenderLayers`

<div class="release-feature-authors">authors: @robftm</div>

[`RenderLayers`] are Bevy's answer to quickly hiding and showing entities en masse, powered by camera-driven views.
Great for things like customizing the first-person view of what a character is holding (or making sure vampires don't show up in your mirrors!).

[`RenderLayers`] [now play nice] with lights, fixing a serious limitation to make sure this awesome feature can shine appropriately!

[`RenderLayers`]: https://docs.rs/bevy/latest/bevy/render/view/struct.RenderLayers.html
[now play nice]: https://github.com/bevyengine/bevy/pull/10742

## Approximate Indirect Specular Occlusion

<div class="release-feature-authors">authors: @aevyrie</div>

Bevy's current PBR renderer over-brightens the image, especially at grazing angles where the fresnel
effect tends to make surfaces behave like mirrors. This over-brightening happens because the
surfaces must reflect _something_, but without path traced or screen-space reflections, the renderer
has to guess _what_ is being reflected. The best guess it can make is to sample the environment cube
map, even if light would've hit something else before reaching the environment light. This artifact,
where light occlusion is ignored, is called specular light leaking.

Consider a car tire; though the rubber might be shiny, you wouldn't expect it to have bright
specular highlights inside a wheel well, because the car itself is blocking (occluding) the light
that would otherwise cause these reflections. Checking for occlusion can be computationally
expensive. Instead, this change uses Bevy's existing screen space ambient occlusion to approximate
specular occlusion.

<b style="display:block; margin-bottom: -18px">Drag this image to compare</b>

<div class="image-compare" style="aspect-ratio: 16 / 9" data-title-a="Specular Occlusion On" data-title-b="Specular Occlusion Off">
  <img class="image-a" alt="Specular Occlusion On" src="specular_occlusion_on.jpg">
  <img class="image-b" alt="Specular Occlusion Off" src="specular_occlusion_off.jpg">
</div>

<div style="font-size: 1.0rem" class="release-feature-authors">Model Credits: <a href="https://sketchfab.com/3d-models/bmw-r1200gs-motorcycle-6550451b0ae547039585a44286b2f530">BMW R1200GS Motorcycle</a> by Moto3D is licensed under <a href="http://creativecommons.org/licenses/by/4.0/">CC-BY-4.0</a>.
</div>

In the future, this could be further improved with screen space reflections (SSR).
However, conventional wisdom is that you should use specular occlusion alongside SSR, because SSR still suffers from light leaking artifacts.

## Bind Group Layout Entries

<div class="release-feature-authors">authors: @IceSentry</div>

We added a new API, inspired by the bind group entries API from 0.12, to declare bind group layouts. This new API is based on using built-in functions to define bind group layouts resources and automatically set the index based on its position.

Here's a short example of how declaring a new layout looks:

```rust
let layout = render_device.create_bind_group_layout(
    "post_process_bind_group_layout"),
    &BindGroupLayoutEntries::sequential(
        ShaderStages::FRAGMENT,
        (
            texture_2d_f32(),
            sampler(SamplerBindingType::Filtering),
            uniform_buffer::<PostProcessingSettings>(false),
        ),
    ),
);
```

## WGPU 0.19 Upgrade and Rendering Performance Improvements

<div class="release-feature-authors">authors: @Elabajaba, @JMS55</div>

In Bevy 0.13 we upgraded from `wgpu` 0.17 to `wgpu` 0.19, which includes the long awaited `wgpu` [arcanization](https://gfx-rs.github.io/2023/11/24/arcanization.html) that allows us to [compile shaders asynchronously](https://github.com/bevyengine/bevy/pull/10812) to avoid shader compilation stutters and [multithread draw call creation](https://github.com/bevyengine/bevy/pull/9172 ) for better performance.

Due to changes in wgpu 0.19, we've added a new `webgpu` feature to Bevy that is now required when doing WebAssembly builds targeting WebGPU. Disabling the `webgl2` feature is no longer required when targeting WebGPU, but the new `webgpu` feature currently overrides the `webgl2` feature when enabled. Library authors, please do not enable the `webgpu` feature by default. In the future we plan on allowing you to target both WebGL2 and WebGPU in the same WebAssembly binary, but it requires reworking parts of the renderer where we're relying on compile time constants when targeting `webgl2`, and adding a way to choose the renderer's backend at runtime on web.

As usual, there's been some changes that may cause issues for custom shaders. We've swapped the material and mesh bind groups, so that mesh data is now in bind group 1, and material data is in bind group 2. This greatly improved our draw call batching when combined with changing the sorting functions for the opaque passes to sort by pipeline and mesh. Previously we were sorting them by distance from the camera. These batching improvements mean we're doing fewer draw calls, which improves CPU performance, especially in larger scenes. We've also removed the `get_instance_index` function in shaders, as it was only required to workaround an upstream bug that has been fixed in wgpu 0.19. For other shader or rendering changes, please see the [migration guide](/learn/migration-guides/0.12-0.13/) and [wgpu's changelog](https://github.com/gfx-rs/wgpu/blob/v0.19/CHANGELOG.md).

Many small changes both to Bevy and `wgpu` have made a big difference in our performance on realistic 3D scenes!
We ran some quick tests on both Bevy 0.12 and Bevy 0.13 on the same machine on four complex scenes: [Bistro], [Sponza], [San Miguel] and [Hidden Valley].

![A high polygon, realistically lit screenshot of a beautiful cafe with a tree in the background.](San_Miguel_13.png)

As you can see, these scenes are substantially more detailed than most video game environments, but that screenshot was being rendered in Bevy at better than 60 FPS at 1440p resolution!
Between Bevy 0.12 and Bevy 0.13 we saw performance gains across all of the scenes, although the magnitude varied widely. Great work!

![A graph showing the FPS of the different scenes. Bistro went from 90 FPS to 120 FPS, while the other scenes improved slightly. All scenes were 60-120 FPS.](rendering-perf-graph.svg)

[Bistro]: https://github.com/DGriffin91/bevy_bistro_scene
[Sponza]: https://github.com/DGriffin91/bevy_sponza_scene
[San Miguel]: https://github.com/DGriffin91/bevy_san_miguel_scene
[Hidden Valley]: https://blog.polyhaven.com/hidden-alley/

## Unload rendering assets from RAM

<div class="release-feature-authors">authors: @JMS55, @mockersf, @brianreavis</div>

Meshes and the textures used to define their materials take up a ton of memory:
in many games, memory usage is the biggest limitation on the resolution and polygon count of the game!
Moreover, actually transferring that data from system RAM (used by the CPU) to the VRAM (used by the GPU) can be a real performance bottleneck.

In the [initial PR] by @JMS55, Bevy added the ability to unload this data from system RAM, once it has been succesfully tranferred to VRAM.
However, unloading the data from the CPU [can result in bugs], and make it harder to actually inspect or modify that data from your other game systems.
As a result, this behavior is currently off by default.

To configure this behavior for your asset, set the [`RenderAssetUsages`] field when constructing your raw asset type such as `Image` or `Mesh`,
which is a bitflag type that allows you to specify whether to retain the data in the main (CPU) world, the render (GPU) world, or both.

@brianreavis [later refined] this API, and used it to ensure that texture atlases and font atlases only extract data that's actually in use
to VRAM, rather than wasting work sending _all_ possible images or characters to VRAM every frame.
Neat!

[initial PR]: https://github.com/bevyengine/bevy/pull/10520
[can result in bugs]: https://github.com/bevyengine/bevy/pull/11212
[`RenderAssetUsages`]: https://dev-docs.bevyengine.org/bevy/render/render_asset/struct.RenderAssetUsages.html
[later refined]: https://github.com/bevyengine/bevy/pull/11399

## Better batching through smarter sorting

<div class="release-feature-authors">authors: @Elabajaba</div>

One of the core techniques used to speed up rendering is to draw many similar objects together at the same time.
In this case, Bevy was already using a technique called "batching", which allows us to combine multiple similar operations together,
reducing the number of expensive draw calls (instructions to the GPU) that are being made.

However, our strategy for defining these batches was far from optimal.
Previously, we were sorting by distance to the camera, and _then_ checking if multiple of the same meshes were adjacent to each other in that sorted list.
On realistic scenes, this is unlikely to find many candidates for merging!

Following [PR #11671] however, we first sort by pipeline (effectively the type of material being used), and then by mesh identity.
This strategy results in better batching, improving overall FPS by double digit percentages on the [realistic scene tested](https://syntystore.com/products/polygon-fantasy-kingdom)!

![A graph showing batching improvements. Shadows are very expensive, and FPS improved by at least 20% in all cases tested.](better_batching.svg)

[PR #11671]: https://github.com/bevyengine/bevy/pull/11671

## Type-Safe Labels for the `RenderGraph`

<div class="release-feature-authors">authors: @DasLixou</div>

Bevy uses Rust's type system extensively when defining labels, letting developers lean on tooling to catch typos and ease refactors.
But this didn't apply to Bevy's render graph. In the render graph, hard-coded—and potentially overlapping—strings were used to define nodes and sub-graphs.

```rust
// Before 0.13
impl MyRenderNode {
    pub const NAME: &'static str = "my_render_node"
}
```

In Bevy 0.13, we're using a more robust way to name render nodes and render graphs with the help of the type-safe label pattern already used by `bevy_ecs`.

```rust
// After 0.13
#[derive(Debug, Hash, PartialEq, Eq, Clone, RenderLabel)]
pub struct MyRenderLabel;
```

With those, the long paths for const-values become shorter and cleaner:

```rust
// Before 0.13
render_app
    .add_render_graph_node::<ViewNodeRunner<MyRenderNode>>(
        core_3d::graph::NAME,
        MyRenderNode::NAME,
    )
    .add_render_graph_edges(
        core_3d::graph::NAME,
        &[
            core_3d::graph::node::TONEMAPPING,
            MyRenderNode::NAME,
            core_3d::graph::node::END_MAIN_PASS_POST_PROCESSING,
        ],
    );

// After 0.13
use bevy::core_pipeline::core_3d::graph::{Labels3d, SubGraph3d};

render_app
    .add_render_graph_node::<ViewNodeRunner<MyRenderNode>>(
        SubGraph3d,
        MyRenderLabel,
    )
    .add_render_graph_edges(
        SubGraph3d,
        (
            Labels3d::Tonemapping,
            MyRenderLabel,
            Labels3d::EndMainPassPostProcessing,
        ),
    );
```

When you need dynamic labels for render nodes, those can still be achieved via e.g. tuple structs:

```rust
#[derive(Debug, Hash, PartialEq, Eq, Clone, RenderLabel)]
pub struct MyDynamicLabel(&'static str);
```

This is particularly nice because we don't have to store strings in here: we can use integers, custom enums or any other hashable type.

## Camera-Driven UI

<div class="release-feature-authors">authors: @bardt, @oceantume</div>

Historically, Bevy's UI elements have been scaled and positioned in the context of the primary window, regardless of the camera settings. This approach made some UI experiences like split-screen multiplayer difficult to implement, and others such as having UI in multiple windows impossible.

We are now introducing a more flexible way of rendering the user interface: camera-driven UI. Each camera can now have its own UI root, rendering according to its viewport, scale factor, and a target which can be a secondary window or even a texture.

This change unlocks a variety of new UI experiences, including split-screen multiplayer, UI in multiple windows, displaying non-interactive UI in a 3D world, and more.

![Split-screen with independent UI roots](split-screen.png)

If there is one camera in the world, you don't need to do anything; your UI will be displayed in that camera's viewport.

```rust
commands.spawn(Camera3dBundle {
    // Camera can have custom viewport, target, etc.
});
commands.spawn(NodeBundle {
    // UI will be rendered to the singular camera's viewport
});
```

For when more control is desirable, or there are multiple cameras, we introduce [`TargetCamera`] component. This component can be added to a root UI node to specify which camera it should be rendered to.

```rust
// For split-screen multiplayer, we set up 2 cameras and 2 UI roots
let left_camera = commands.spawn(Camera3dBundle {
    // Viewport is set to left half of the screen
}).id();

commands
    .spawn((
        TargetCamera(left_camera),
        NodeBundle {
            //...
        }
    ));

let right_camera = commands.spawn(Camera3dBundle {
    // Viewport is set to right half of the screen
}).id();

commands
    .spawn((
        TargetCamera(right_camera),
        NodeBundle {
            //...
        })
    );
```

With this change, we also remove [`UiCameraConfig`] component. If you were using it to hide UI nodes, you can achieve the same outcome by setting [`Visibility`] component on the root node.

```rust
commands.spawn(Camera3dBundle::default());
commands.spawn(NodeBundle {
    visibility: Visibility::Hidden, // UI will be hidden
    // ...
});
```

[`TargetCamera`]: https://docs.rs/bevy/0.13.0/bevy/ui/struct.TargetCamera.html
[`Visibility`]: https://docs.rs/bevy/0.13.0/bevy/render/view/enum.Visibility.html
[`UiCameraConfig`]: https://docs.rs/bevy/0.12.1/bevy/ui/camera_config/struct.UiCameraConfig.html

## Winit Upgrade

<div class="release-feature-authors">authors: @Thierry Berger, @mockersf</div>

Through the heroic efforts of our contributors and reviewers, Bevy is [now upgraded] to use `winit 0.29`.
[`winit`] is our windowing library: it abstracts over all of the different operating systems and input devices that end users might have,
and provides a basically uniform API to enable a write-once run-anywhere experience.
While this brings with it the usual litany of valuable [bug fixes and stability improvements],
the critical change revolves around how [`KeyCode`] is handled.

Previously, [`KeyCode`] represented the logical meaning of a key on a keyboard:
pressing the same button on the same keyboard when swapping between QWERTY and AZERTY keyboard layouts would give a different result!
Now,  [`KeyCode`] represents the physical location of the key.
Lovers of WASD games know that this is a much better default for games. For most Bevy developers, you can leave your existing code untouched
and simply benefit from better default keybindings for users on non-QWERTY keyboards or layouts.
If you need information about the logical keys pressed, use the [`ReceivedCharacter`] event.

[now upgraded]: https://github.com/bevyengine/bevy/pull/10702
[`winit`]: https://docs.rs/winit/latest/winit/
[bug fixes and stability improvements]: https://github.com/rust-windowing/winit/blob/master/CHANGELOG.md#0292
[`KeyCode`]: https://docs.rs/bevy/latest/bevy/input/keyboard/enum.KeyCode.html
[`ReceivedCharacter`]: https://docs.rs/bevy/latest/bevy/prelude/struct.ReceivedCharacter.html

## Animation Interpolation Methods

<div class="release-feature-authors">authors: @mockersf</div>

Generally, animations are defined by their **keyframes**: snapshots of the position (and other state) or objects at moments along a time line.
But what happens between those keyframes? Game engines need to **interpolate** between them, smoothly transitioning from one state to the next.

The simplest interpolation method is linear: the animated object just moves an equal distance towards the next keyframe every unit of time.
But this isn't always the desired effect! Both stop-motion-style and more carefully smoothed animations have their place.

Bevy now supports both step and cubic spline interpolation in animations.
Most of the time, this will just be parsed correctly from the glTF files, but when setting [`VariableCurve`] manually,
there's a new [`Interpolation`] field to set.

[`VariableCurve`]: https://dev-docs.bevyengine.org/bevy/animation/struct.VariableCurve.html
[`Interpolation`]: https://dev-docs.bevyengine.org/bevy/animation/enum.Interpolation.html

![Demonstrating the different types of interpolation](interpolation_methods.gif)

## `Animatable` Trait

<div class="release-feature-authors">authors: @james7132</div>

When you think of "animation": you're probably imagining moving objects through space.
Translating them back and forth, rotating them, maybe even squashing and stretching them.
But in modern game development, animation is a powerful shared set of tools and concepts for "changing things over time".
Transforms are just the beginning: colors, particle effects, opacity and even boolean values like visibility can all be animated!

In Bevy 0.13, we've taken the first step towards [this vision](https://github.com/bevyengine/rfcs/blob/main/rfcs/51-animation-composition.md),
with the [`Animatable`] trait.

```rust
/// An animatable value type.
pub trait Animatable: Reflect + Sized + Send + Sync + 'static {
    /// Interpolates between `a` and `b` with  a interpolation factor of `time`.
    ///
    /// The `time` parameter here may not be clamped to the range `[0.0, 1.0]`.
    fn interpolate(a: &Self, b: &Self, time: f32) -> Self;

    /// Blends one or more values together.
    ///
    /// Implementors should return a default value when no inputs are provided here.
    fn blend(inputs: impl Iterator<Item = BlendInput<Self>>) -> Self;

    /// Post-processes the value using resources in the [`World`].
    /// Most animatable types do not need to implement this.
    fn post_process(&mut self, _world: &World) {}
}
```

This is the first step towards animation blending and an asset-driven animation graph which is an essential for shipping large scale 3D games in Bevy.
But for now, this is just a building block. We've implemented this for a few key types (`Transform`, `f32` and `glam`'s `Vec` types) and published the trait.
Slot it into your games and crates, and team up with other contributors to help `bevy_animation` become just as pleasant and featureful as the rest of the engine.

[`Animatable`]: https://dev-docs.bevyengine.org/bevy/prelude/trait.Animatable.html

## Multiple gizmo configurations

<div class="release-feature-authors">authors: @jeliag</div>

Gizmos let you quickly draw shapes using
an immediate mode API. Here is how you use them:

```rust
// bevy 0.12.1
fn set_gizmo_width(mut config: ResMut<GizmoConfig>) {
    // set the line width of every gizmos with this global configuration resource.
    config.line_width = 5.0;
}

fn draw_circles(mut gizmos: Gizmos) {
    // Draw two circles with a 5 pixels outline
    gizmos.circle_2d(vec2(100., 0.), 120., Color::NAVY);
    gizmos.circle_2d(vec2(-100., 0.), 120., Color::ORANGE);
}
```

Add a [`Gizmos`] system param and simply call a few methods. Cool!

Gizmos are also great for crate authors, they can use the same API.
For example, the [`oxidized_navigation`] navmesh library uses gizmos for its debug overlay.
Neat!

However, there is only one global configuration. Therefore,
a dependency could very well affect the game's gizmos.
It could even make them completely unusable.

Not so great. How to solve this? Gizmo groups.

Now, [`Gizmos`] comes with an optional parameter.
By default, it uses a global configuration:

```rust
fn draw_circles(mut default_gizmos: Gizmos) {
    default_gizmos.circle_2d(vec2(100., 0.), 120., Color::NAVY);
}
```

But with a [`GizmoConfigGroup`] parameter, `Gizmos` can choose a distinct configuration:

```rust
fn draw_circles(
    mut default_gizmos: Gizmos,
    // this uses a distinct configvvvvvvvvvvvvvvv
    mut navigation_gizmos: Gizmos<NavigationGroup>,
) {
    // Two circles with different outline width
    default_gizmos.circle_2d(vec2(100., 0.), 120., Color::NAVY);
    navigation_gizmos.circle_2d(vec2(-100., 0.), 120., Color::ORANGE);
}
```

Create your own gizmo config group by deriving `GizmoConfigGroup`,
and registering it to the `App`:

```rust
#[derive(Default, Reflect, GizmoConfigGroup)]
pub struct NavigationGroup;

impl Plugin for NavigationPlugin {
    fn build(&mut self, app: &mut App) {
        app
            .init_gizmo_group::<NavigationGroup>()
            // ... rest of plugin initialization.
    }
}
```

And this is how you set the configuration of gizmo groups to different values:

```rust
// bevy 0.13.0
set_gizmo_width(mut config_store: ResMut<GizmoConfigStore>) {
    let config = config_store.config_mut::<DefaultGizmoConfigGroup>().0;
    config.line_width = 20.0;

    let navigation_config = config_store.config_mut::<NavigationGroup>().0;
    navigation_config.line_width = 10.0;
}
```

Now, the navigation gizmos have a fully separate configuration and don't conflict
with the game's gizmos.

Not only that, but the game dev can integrate and toggle the navigation gizmos with their
own debug tools however they wish. Be it a hotkey, a debug overlay UI button,
an RPC call. The world is your oyster.

[`oxidized_navigation`]: https://crates.io/crates/oxidized_navigation
[`GizmoConfigGroup`]: https://dev-docs.bevyengine.org/bevy/gizmos/config/trait.GizmoConfigGroup.html

## glTF Extensions

<div class="release-feature-authors">authors: @CorneliusCornbread</div>

**[glTF]** is a popular standardized open file format, used to store and share 3D models and scenes between different programs.
The trouble with standards though is that you eventually want to _customize_ it, just a little, to better meet your needs.
Khronos Group, in their wisdom, foresaw this and defined a standardized way to customize the format called **[extensions]**.

Extensions can be readily exported from other tools (like Blender), and contain [all sorts] of other useful information: from bleeding edge physically-based material information like anisotropy to performance hints like how to instance meshes.

Because Bevy parses loaded glTF's into our own entity-based hierarchy of objects, getting access to this information when you want to do new rendering things can be hard!
With [the changes by CorneliusCornbread] you can configure the loader to store a raw copy of the glTF file itself with your loaded asset, allowing you to parse and reprocess this information however you please.

[glTF]: https://www.khronos.org/gltf/
[extensions]: https://kcoley.github.io/glTF/extensions/
[all sorts]: https://github.com/KhronosGroup/glTF/blob/main/extensions/README.md
[the changes by CorneliusCornbread]: https://github.com/bevyengine/bevy/pull/11138

## Extensionless Asset Support

<div class="release-feature-authors">authors: @bushrat011899</div>

In prior versions of Bevy, the default way to choose an [`AssetLoader`] for a particular asset was entirely based around file extensions. The recent addition of [`meta` files] allowed for specifying more granular loading behavior, but file extensions were still required. In Bevy 0.13, the asset type can now be used to infer the [`AssetLoader`].

```rust
// Uses AudioSettingsAssetLoader
let audio = asset_server.load("data/audio.json");

// Uses GraphicsSettingsAssetLoader
let graphics = asset_server.load("data/graphics.json");
```

This is possible because every [`AssetLoader`] is required to declare what **type** of asset it loads, not just the extensions it supports. Since the [`load`] method on [`AssetServer`] was already generic over the type of asset to return, this information is already available to the [`AssetServer`] so that the appropriate [`Handle`] type is returned.

```rust
// The above example with types shown
let audio: Handle<AudioSettings> = asset_server.load::<AudioSettings>("data/audio.json");
let graphics: Handle<GraphicsSettings> = asset_server.load::<GraphicsSettings>("data/graphics.json");
```

Now we can also use it to choose the [`AssetLoader`] itself.

```rust
#[derive(Resource)]
struct Settings {
    audio: Handle<AudioSettings>,
//                ^^^^^^^^^^^^^
//                | This type...
}

fn setup(mut settings: ResMut<Settings>, asset_server: Res<AssetServer>) {
    settings.audio = asset_server.load("data/audio.json");
//                                ^^^^
//                                | ...is passed here...
}

impl AssetLoader for AudioSettingsAssetLoader {
    type Asset = AudioSettings;
//               ^^^^^^^^^^^^^
//               | ...and checked against AssetLoader::Asset

    /* snip */
}
```

When loading an asset, the loader is chosen by checking (in order):

1. The asset `meta` file
2. The type of `Handle<A>` to return
3. The file extension

```rust
// This will be inferred from context to be a glTF asset, ignoring the file extension
let gltf_handle = asset_server.load("models/cube/cube.gltf");

// This still relies on file extension due to the label
let cube_handle = asset_server.load("models/cube/cube.gltf#Mesh0/Primitive0");
//                                                        ^^^^^^^^^^^^^^^^^
//                                                        | Asset path label
```

### File extensions are now optional

Since the asset type can be used to infer the loader, neither the file to be loaded nor the [`AssetLoader`] need to have file extensions.

```rust
pub trait AssetLoader: Send + Sync + 'static {
    /* snip */

    /// Returns a list of extensions supported by this [`AssetLoader`], without the preceding dot.
    fn extensions(&self) -> &[&str] {
        // A default implementation is now provided
        &[]
    }
}
```

Previously, an asset loader with no extensions was very cumbersome to use. Now, they can be used just as easily as any other loader. Likewise, if a file is missing its extension, Bevy can now choose the appropriate loader.

```rust
let license = asset_server.load::<Text>("LICENSE");
```

Appropriate file extensions are still recommended for good project management, but this is now a recommendation rather than a hard requirement.

### Multiple `AssetLoader`'s can be selected for the same asset

Now, a single path can be used by multiple asset handles as long as they are distinct asset types.

```rust
// Load the sound effect for playback
let bang = asset_server.load::<AudioSource>("sound/bang.ogg");

// Load the raw bytes of the same sound effect (e.g, to send over the network)
let bang_blob = asset_server.load::<Blob>("sound/bang.ogg");

// Returns the bang handle since it was already loaded
let bang_again = asset_server.load::<AudioSource>("sound/bang.ogg");
```

Note that the above example uses [turbofish] syntax for clarity. In practice, it's not required, since the type of asset loaded can usually be inferred by surrounding context at the call site.

```rust
#[derive(Resource)]
struct SoundEffects {
    bang: Handle<AudioSource>,
    bang_blog: Handle<Blob>,
}

fn setup(mut effects: ResMut<SoundEffects>, asset_server: Res<AssetServer>) {
    effects.bang = asset_server.load("sound/bang.ogg");
    effects.bang_blob = asset_server.load("sound/bang.ogg");
}
```

The [`custom_asset` example] has been updated to demonstrate these new features.

[`meta` files]: https://bevyengine.org/news/bevy-0-12/#asset-meta-files
[`AssetServer`]: https://dev-docs.bevyengine.org/bevy/asset/struct.AssetServer.html
[`AssetLoader`]: https://dev-docs.bevyengine.org/bevy/asset/trait.AssetLoader.html
[`load`]: https://dev-docs.bevyengine.org/bevy/asset/struct.AssetServer.html#method.load
[`Handle`]: https://dev-docs.bevyengine.org/bevy/asset/enum.Handle.html
[turbofish]: https://turbo.fish/
[`custom_asset` example]: https://bevyengine.org/examples/Assets/custom-asset/

## Asset Transformers

<div class="release-feature-authors">authors: @thepackett, @RyanSparker</div>

Asset processing, at its core, involves implementing the `Process` trait, which takes some byte data representing an asset, transforming it, and then returning the processed byte data. However, implementing the `Process` trait by hand is somewhat involved, and so a generic `LoadAndSave<L: AssetLoader, S: AssetSaver>` `Process` implementation was written to make asset processing more ergonomic.

Using the `LoadAndSave` `Process` implementation, the previous Asset processing pipeline had four stages:

1. An `AssetReader` reads some asset source (filesystem, http, etc) and gets the byte data of an asset.
2. An `AssetLoader` reads the byte data and converts it to a bevy `Asset`.
3. An `AssetSaver` takes a bevy `Asset`, processes it, and then converts it back into byte data.
4. An `AssetWriter` then writes the asset byte data back to the asset source.

`AssetSaver`s were responsible for both transforming an asset and converting it into byte data. However, this posed a bit of an issue for code reusability. Every time you wanted to transform some asset, such as an image, you would need to rewrite the portion that converts the asset to byte data. To solve this, `AssetSaver`s are now solely responsible for converting an asset into byte data, and `AssetTransformer`s which are responsible for transforming an asset were introduced. A new `LoadTransformAndSave<L: AssetLoader, T: AssetTransformer, S: AssetSaver>` `Process` implementation was added to utilize the new `AssetTransformer`s.

The new asset processing pipeline, using the `LoadTransformAndSave` `Process` implementation, has five stages:

1. An `AssetReader` read some asset source (filesystem, http, etc) and gets the byte data of an asset.
2. An `AssetLoader` reads the byte data and converts it to a bevy `Asset`.
3. An `AssetTransformer` takes an asset and transforms it in some way.
4. An `AssetSaver` takes a bevy `Asset` and converts it back into byte data.
5. An `AssetWriter` then writes the asset byte data back to the asset source.

In addition to having better code reusability, this change encorages writing `AssetSaver`s for various common asset types, which could be used to add runtime asset saving functionality to the `AssetServer`.

The previous `LoadAndSave` `Process` implementation still exists, as there are some cases where an asset transformation step is unnecessary, such as when saving assets into a compressed format.

See the [Asset Processing Example](<https://github.com/bevyengine/bevy/blob/main/examples/asset/processing/asset_processing.rs>) for a more detailed look into how to use `LoadTransformAndSave` to process a custom asset.

## Entity Optimizations

<div class="release-feature-authors">authors: @Bluefinger, @notverymoe, @scottmcm, @bushrat011899, @james7132</div>

`Entity` (Bevy's 64-bit unique identifier for enitities) received a number of changes this cycle, combining laying some more groundwork for relations alongside _related_, and nice to have, performance optimizations. The work here involved a lot of deep-diving into compiler codegen/assembly output, with running lots of benchmarks and testing in order to ensure all changes didn't cause breakages or major problems. Although the work here was dealing with mostly _safe_ code, there were lots of underlying assumptions being changed that could have impacted code elsewhere. This was the most "micro-optimization" oriented set of changes in Bevy 0.13.

* [#9797]: created a unified identifier type, paving the path for us to use the same fast, complex code in both our `Entity` type and the much-awaited relations
* [#9907]: allowed us to store `Option<Entity>` in the same number of bits as `Entity`, by changing the layout of our Entity type to reserve exactly one `u64` value for the `None` variant
* [#10519]: swapped us to a manually crafted `PartialEq` and `Hash` implementation for `Entity` to improve speed and save instructions in our hot loops
* [#10558]: combined the approach of [#9907] and [#10519] to optimize `Entity`'s layout further, and optimized our `PartialOrd` and `Ord` implementations!
* [#10648]: further optimized our entity hashing, changing how we multiply inside of the hash to save one precious assembly instruction in the optimized compiler output

Full credit is also due to the authors who pursued similar work in [#2372] and [#3788]: while their work was not ultimately merged, it was an incredibly valuable
inspiration and source of prior art to base these more recent changes on.

![Benchmark results of optimisation work](entity_hash_optimsation_benches.png)

The above results show from where we started (`optimised_eq` being the first PR that introduced the benchmarks) to where we are now with all the optimisations in place (`optimised_entity`).
There are improvements across the board, with clear performance benefits that should impact multiple areas of the codebase, not just when hashing entities.

There are a ton of crunchy, well-explained details in the linked PRs, including some fascinating assembly output analysis.
If that interests you, open some new tabs in the background!

[#9797]: https://github.com/bevyengine/bevy/pull/9797
[#9907]: https://github.com/bevyengine/bevy/pull/9907
[#10519]: https://github.com/bevyengine/bevy/pull/10519
[#10558]: https://github.com/bevyengine/bevy/pull/10558
[#10648]: https://github.com/bevyengine/bevy/pull/10648
[#2372]: https://github.com/bevyengine/bevy/pull/2372
[#3788]: https://github.com/bevyengine/bevy/pull/3788

## Porting `Query::for_each` to `QueryIter::fold` override

Currently to get the full performance out of iterating over queries, `Query::for_each` must be used in order to take advantage of auto-vectorization and internal iteration optimizations that the compiler can apply. However, this isn't idiomatic rust and is not an iterator method so you can't use it on an iterator chain. However, it is possible to get the same benefits for some iterator methods, for which [#6773](https://github.com/bevyengine/bevy/pull/6773/) by @james7132 sought to achieve. By providing an override to `QueryIter::fold`, it was possible to port the iteration strategies of `Query::for_each` so that `Query::iter` and co could achieve the same gains. Not _every_ iterator method currently benefits from this, as they require overriding `QueryIter::try_fold`, but that is currently still a nightly-only optimisation. This same approach is within the Rust standard library.

This deduplicated code in a few areas, such as no longer requiring both `Query::for_each` and `Query::for_each_mut`, as one just needs to call `Query::iter` or `Query::iter_mut` instead. So code like:

```rust
fn some_system(mut q_transform: Query<&mut Transform, With<Npc>>) {
    q_transform.for_each_mut(|transform| {
        // Do something...
    });
}
```

Becomes:

```rust
fn some_system(mut q_transform: Query<&mut Transform, With<Npc>>) {
    q_transform.iter_mut().for_each(|transform| {
        // Do something...
    });
}
```

The assembly output was compared as well between what was on main branch versus the PR, with no tangible differences being seen between the old `Query::for_each` and the new `QueryIter::for_each()` output, validating the approach and ensuring the internal iteration optimizations were being applied.

As a plus, the same internal iteration optimizations in `Query::par_for_each` now reuse code from `for_each`, deduplicating code there as well and enabling users to make use of `par_iter().for_each()`. As a whole, this means there's no longer any need for `Query::for_each`, `Query::for_each_mut`, `Query::_par_for_each`, `Query::par_for_each_mut` so these methods have been deprecated for 0.13 and will be removed in 0.14.

## Reducing `TableRow` `as` casting

Not all improvements in our ECS internals were focused around performance. Some small changes were done to improve type safety and tidy-up some of the codebase to have less `as` casting being done on various call sites for `TableRow`. The problem with `as` casting is that in some cases, the cast will fail by truncating the value silently, which could then cause havoc by accessing the wrong row and so forth. [#10811](https://github.com/bevyengine/bevy/pull/10811) by @bushrat011899 was put forward to clean up the API around `TableRow`, providing convenience methods backed by `assert`s to ensure the casting operations could never fail, or if they did, they'd panic correctly.

Naturally, _adding_ asserts in potentially hot codepaths were cause for some concern, necessitating considerable benchmarking efforts to confirm there were regressions and to what level. With careful placing of the new `assert`s, the detected regression for these cases was in the region of 0.1%, well within noise.
But the benefit was a less error-prone API and more robust code.
With a complex unsafe codebase like `bevy_ecs`, every little bit helps.

## <a name="what-s-next"></a>What's Next?

We have plenty of work in progress! Some of this will likely land in **Bevy 0.14**.

Check out the [**Bevy 0.14 Milestone**](https://github.com/bevyengine/bevy/milestone/20) for an up-to-date list of current work that contributors are focusing on for **Bevy 0.14**.

### More editor experimentation

Led by the brilliant JMS55, we've opened up a free-form [playground] to define and answer [key questions] about the design of the `bevy_editor`: not through discussion, but through concrete prototyping.
Should we use an in-process editor (less robust to game crashes) or an external one (more complex)?
Should we ship an editor binary (great for non-programmers) or embed it in the game itself (very hackable)?
Let's find out by doing!

There are some incredible mockups, functional prototypes and third-party editor-ajdacent projects out there. Some highlights:

<div style="display: grid; grid-template-columns: 1fr 1fr 1fr 1fr 1fr 1fr; gap: 1rem; align-items: end; justify-items: center; margin: 2rem; font-size: 0.8rem; text-align: center">
  <div style="grid-column: span 2">
    <a href="editor_mockup.png"><img src="editor_mockup.png" alt="A mockup of the UI for a graphical Bevy editor" style="border-radius: 0; box-shadow: 0 0.5rem 0.5rem rgba(0, 0, 0, 0.5)"></a>
    bevy_editor_mockup
  </div>
  <div style="grid-column: span 2">
    <a href="locomotion_graph.png"><img src="locomotion_graph.png" alt="A node-based animation graph editor built with `bevy_egui`" style="border-radius: 0; box-shadow: 0 0.5rem 0.5rem rgba(0, 0, 0, 0.5)"></a>
    bevy_animation_graph
  </div>
  <div style="grid-column: span 2">
    <a href="space_editor.png"><img src="space_editor.png" alt="A screenshot from `space_editor`, showcasing a functional scene editor with gizmos" style="border-radius: 0; box-shadow: 0 0.5rem 0.5rem rgba(0, 0, 0, 0.5)"></a>
    space_editor
  </div>
  <div style="grid-column: 2 / span 2">
    <a href="bevy_components.png"><img src="bevy_components.png" alt="A screenshot from Blender, with Blender UI modifying Bevy component values" style="border-radius: 0; box-shadow: 0 0.5rem 0.5rem rgba(0, 0, 0, 0.5)"></a>
    bevy_components
  </div>
  <div style="grid-column: span 2">
    <a href="makeshift_web.png"><img src="makeshift_web.png" alt="A recording of a web-based editor resulting in changes to Bevy entities in real time" style="border-radius: 0; box-shadow: 0 0.5rem 0.5rem rgba(0, 0, 0, 0.5)"></a>
    bevy_remote
  </div>
</div>

* [] a Bevy-branded editor UI mockup by `@Jacob_` and `@!!&Amy` on Discord, imagining what the UX for an ECS-based editor [could look like]
* [] [`bevy_animation_graph`]: a fully-functional asset-driven animation graph crate with its own node-based editor for Bevy
* [] [`space_editor`]: a polished Bevy-native third-party scene editor that you can use today!
* [] [`Blender_bevy_components_workflow`]: an impressively functional ecosystem of tools that lets you use Blender as a seamless level and scene editor for your games today.
* [] `@coreh`'s experiment on a [reflection-powered remote protocol], allowing devs to inspect and control their Bevy games from other processes, languages and even devices! [Try it out live]!

It's really exciting to see this progress, and we're keen to channel that energy and experience into official first-party efforts.

[playground]: https://github.com/bevyengine/bevy_editor_prototypes
[could look like]: https://asour8.github.io/bevy_editor_mockup/editor/
[key questions]: https://github.com/bevyengine/bevy_editor_prototypes/discussions/1
[`bevy_animation_graph`]: https://crates.io/crates/bevy_animation_graph
[`space_editor`]: https://github.com/rewin123/space_editor
[`Blender_bevy_components_workflow`]: https://github.com/kaosat-dev/Blender_bevy_components_workflow
[reflection-powered remote protocol]: https://github.com/coreh/bevy/pull/1
[Try it out live]: https://makeshift-bevy-web-editor.vercel.app/

### `bevy_dev_tools`

The secret to smooth game development is great tooling.
It's time to give Bevy developers the tools they need to inspect, debug and profile their games as part of the first-party experience.
From FPS meters to system stepping to a first-party equivalent of the fantastic [`bevy-inspector-egui`]: giving these a home in Bevy itself helps us polish them, points new users in the right direction, and allows us to use them in the `bevy_editor` itself.

### A revised scene format

[Scenes] are Bevy's general-purpose answer to serializing ECS data to disk: tracking entities, components, and resources for both save games and loading premade levels.
However, the existing .ron-based scene format is hard to hand-author, overly verbose, and brittle; changes to your code (or that of your dependencies!) rapidly invalidate saved scenes.
Cart has been cooking up a [revised scene format] with tight IDE and code integration that tackles these problems and makes authoring content (including UI!) in Bevy a joy. Whether you're writing code, writing scene files, or generating it from a GUI.

[Scenes]: https://github.com/bevyengine/bevy/tree/latest/examples/scene
[revised scene format]: https://github.com/bevyengine/bevy/discussions/9538

### `bevy_ui` improvements

`bevy_ui` has its fair share of problems and limitations, [both mundane and architectural];
however, there are tangible things we can and are doing to improve this:
an improved scene format offers an end to the boilerplate when defining layouts, [rounded] [corners] just need a little love from reviewers, and the powerful and beloved object picking from [`bevy_mod_picking`] is slated to be upstreamed for both UI and gameplay alike.
A spectacular array of [third-party UI solutions] exists today, and learning from those and committing to a core architecture for UI logic and reactivity is a top priority.

[both mundane and architectural]: https://www.leafwing-studios.com/blog/ecs-gui-framework/
[rounded]: https://github.com/bevyengine/bevy/pull/8973
[corners]: https://github.com/bevyengine/bevy/pull/11813
[third-party UI solutions]: https://bevyengine.org/assets/#ui

### Meshlet rendering

Split meshes into clusters of triangles called meshlets, which bring many efficiency gains. During the 0.13 development cycle we made a [lot of progress on this feature](https://github.com/bevyengine/bevy/pull/10164). We implemented a GPU-driven meshlet renderer that can scale to much more triangle-dense scenes, with much lower CPU load. Memory usage, however, is very high, and we haven't implemented LODs or compression yet. Instead of releasing it half-baked, we're going to continue to iterate, and are very excited to (hopefully) bring you this feature in a future release.

![The Stanford dragon mesh rendered as meshlet clusters](meshlet_preview.png)

### The steady march towards relations

[Entity-entity relations], the ability to track and manage connections between entities directly in the ECS, has been one of the most requested ECS features for years now.
Following the [trail blazed by `flecs`], the mad scientists over in `#ecs-dev` are steadily [reshaping our internals], [experimenting with external implementations], and shipping the general purpose building blocks (like dynamic queries or [lifecycle hooks]) needed to build a fast, robust and ergonomic solution.

[Entity-entity relations]: https://github.com/bevyengine/bevy/issues/3742
[trail blazed by `flecs`]: https://ajmmertens.medium.com/building-games-in-ecs-with-entity-relationships-657275ba2c6c
[reshaping our internals]: https://github.com/orgs/bevyengine/projects/15
[experimenting with external implementations]: https://crates.io/crates/aery
[lifecycle hooks]: https://github.com/bevyengine/bevy/pull/10756

## Support Bevy

Sponsorships help make our work on Bevy sustainable. If you believe in Bevy's mission, consider [sponsoring us](/community/donate) ... every bit helps!

<a class="button button--pink header__cta" href="/community/donate">Donate <img class="button__icon" src="/assets/heart.svg" alt="heart icon"></a>

## Contributors

Bevy is made by a [large group of people](/community/people/). A huge thanks to the 185 contributors that made this release (and associated docs) possible! In random order:

TODO: add contributors

## Full Changelog

The changes mentioned above are only the most appealing, highest impact changes that we've made this cycle.
Innumerable bug fixes, documentation changes and API usability tweaks made it in too.
For a complete list of changes, check out the PRs listed below.

TODO: add full changelog, sorting by area.