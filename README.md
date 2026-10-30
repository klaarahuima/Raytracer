# Ray Tracer

A C++ ray tracer capable of visualizing 3D scenes with spheres and triangle meshes, advanced lighting effects, acceleration structures, and realistic material properties.

## Features

* **Surface Materials \& Optics**

  * **Diffuse Surfaces:** Standard Lambertian direct and indirect lighting models.
  * **Specular Reflection (Mirror):** Sharp reflection rays based on surface normal reflection.
  * **Refraction:** Ray bending simulated via Snell's Law.
  * **Unified Geometry Hierarchy:** Geometric objects share a parent class, enabling refractive/transparent and reflective triangle meshes.
* **Lighting \& Shading**

  * **Direct \& Indirect Lighting:** Point light sources with ray-cast shadow calculations and global illumination via random path sampling.
  * **Phong Interpolation:** Smooth vertex normal interpolation across triangle meshes to eliminate polygonal edges.
* **Performance \& Rendering Acceleration**

  * **Antialiasing:** Multi-sampling per pixel using direction perturbations to produce smooth edges.
  * **Bounding Volume Hierarchy (BVH):** Tree-structured spatial partitioning with depth-first traversal for fast ray-mesh intersections.
  * **Simple Axis-Aligned Bounding Box (AABB):** Quick preliminary scene clipping based on mesh extents.
  * **CPU Parallelization:** Multi-core parallel processing for high performance.
  * **Mesh Loading:** Native import support for `.obj` files.

## Render Benchmarks

All benchmark renders below were executed at **$512 \\times 512$ resolution** with a 60° FOV on an Intel i7 CPU (14 cores):

|Rendered Scene|Features / Settings|Render Time|
|-|-|-|
|**Sphere Room (Direct)**|Diffuse, Mirror, Refraction|**< 1.0 s**|
|**Spheres (Indirect Light)**|128 rays/pixel, max depth 5, parallelized|**2.0 s**|
|**Spheres (Antialiased)**|64 rays/pixel, max depth 5, parallelized|**2.0 s**|
|**Cat Mesh (Direct)**|Single AABB, Phong normal interpolation|**1.5 s**|
|**Cat Mesh (Indirect)**|64 rays/pixel, max depth 5, BVH accelerated|**3.5 s**|
|**Cat Mesh (Glass \& Mirror)**|256 rays/pixel, BVH accelerated|**7.5 s**|

## Technical Details

### 1\. Spatial Acceleration (BVH)

Ray-mesh intersection testing on detailed geometry (thousands of polygons) is computationally intensive. To optimize scene traversal:

1. **AABB:** Encloses the mesh in a single bounding box to quickly eliminate non-intersecting rays.
2. **BVH:** Recursively splits the mesh into a hierarchical tree of bounding boxes. A depth-first search stops early when a ray misses a bounding volume, vastly reducing triangle intersection tests.

### 2\. Refraction \& Normal Interpolation

* **Refraction:** Implemented using Snell's Law to adjust the path of light rays passing through media:
$$\\eta\_1 \\sin(\\theta\_1) = \\eta\_2 \\sin(\\theta\_2)$$
* **Phong Shading:** Barycentric coordinates interpolate normals across a triangle face to render smooth curved surfaces on polyhedral meshes:
$$\\mathbf{N}\_p = w\_A \\mathbf{N}\_A + w\_B \\mathbf{N}\_B + w\_C \\mathbf{N}\_C$$

## Future Roadmap

* \[ ] Implement Fresnel equations ($F\_R$) for dynamic reflection/refraction ratios.
* \[ ] Add UV coordinates and texture mapping support.
* \[ ] Add depth-of-field (thin-lens camera model).
* \[ ] GPU acceleration via CUDA or OptiX.
