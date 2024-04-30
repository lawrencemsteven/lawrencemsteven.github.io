---
title: "Greedy Cuboid Optimization"
date: 2024-04-21
summary: "Grouping similar blocks into one large cuboid for more efficient ray traversal"
---

Building on [Plane-Assisted Ray Marching]({{< ref "Projects/Vang/Plane-AssistedRayMarching" >}}), we can group similar voxels together and then travel through the entire cuboid at once.

![Greedy Cuboid Raymarch](raymarch.png)

For example, if a chunk, which is 64x64, consists entirely of air blocks. We could group them together as one block instead and state that a ray within that chunk can do the next ray-plane intersection at the farthest area. To do this it requires storing more information inside of each block.

Currently, the block contains 34 bits of information about the block, stating what type it is. Then it has another 30 bits for the cuboid information. 5 bits correspond to each direction which means that the maximum size for a cuboid is 32x32. So 5 bits are how far the cuboid stretches in the positive X direction, another 5 bits correspond to how far the cuboid stretches in the negative X direction, etc.

This allows for much more efficient ray traversal since the cuboid information and the block information is received entirely in one texture call and then the ray-plane intersection function just offsets the plane in the given direction by the stated amount. Much larger scenes can be rendered at a much higher framerate.