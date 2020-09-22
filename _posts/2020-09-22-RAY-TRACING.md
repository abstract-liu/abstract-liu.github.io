---
layout: page
title: "RAY TRACING"
date: 2020-09-22
categories: CG
---

## 1G.Ray Casting
The main idea is to cast lots of rays from eye throw pixels and intersect with objects. Because it just renderes direct illumination, we have to judge if this intersection point is able to connect light source. And then perform shading calculation to compute color of pixel.

## 2G.Whitted-Style Ray Tracing
The main idea is to recursively reflect rays. But it only consider three cases: reflection, refraction, shadow

### Intersection

Ray Equation:
r(t) = o + td 0<=t<infinity

Intersection with sphere
(p -c)^2 - R^2 = 0

Intersection with triangle mesh
Plane equation:
(p - p^') N = 0

Moller Trumbore Algorithm
This is the barycentric coordinate method.
solve: O+tD = (1-b_1-b_2)P_0 + b_1P_1 + b_2P_2
then we can examine if (1-b_1-b_2), b_1, b_2 are all above 0.

### Accelerating structure
Axis-Aligned-Bounding-Box(AABB): 3 pairs of slabs
for each pair, calculate t_min and t_max
t_enter = max{t_min}, t_exit = min{t_max}

if t_exit < 0, the box is behind the ray
if t_enter < 0 & t_exit >=0, the ray's origin is in the box

in summary, ray and AABB intersect iff
t_enter < t_exit & t_exit >=0

#### Spatial partition
Uniform grids work well on large collections of objects that are distributed evenly in size and space.

1. oct-tree
2. kd-tree
3. bsp-tree

two problems:
1. it takes too much space.
2. some objects may occur over once in the kd-tree.

#### Object Partition
Bounding volume hirarchy(BVH)
1. choose a dimension to split.
2. heuristic: choose the longest axis in node.
3. heuristic: split at the position of median object.
4. stop when node contains few elements.

# Radiometry
lambert's cosine law:
irradiance is proportion to cosine of angle.

rendering equation


direct illumination: emission from light sources and 1st bounce
indirect illumination: over 2nd bounce
global illumination: direct + indirect

## 3G. path tracing
ray_generation(cam_pos, pixel)
    uniformly choose N sample position within the pixel
    pixel_radiance = 0
    for each sample
        shoot a ray r(cam_pos, sample_pos)
        if r hit the scene at p
            pixel_radiance += 1/ N * shade
    return pixel_radiance

shade(p, wo)
    Uniformly sample the light at x’ (pdf_light = 1 / A) L_dir = L_i * f_r * cos θ * cos θ’ / |x’ - p|^2 / pdf_light 
    L_indir = 0.0 
    Test Russian Roulette with probability P_RR 
    Uniformly sample the hemisphere toward wi (pdf_hemi = 1 / 2pi) 
    Trace a ray r(p, wi) 
    If ray r hit a non-emitting object at q 
        L_indir = shade(q, -wi) * f_r * cos θ / pdf_hemi / P_RR
    Return L_dir + L_indir