---
title: "Ray-Marched Lighting"
date: 2024-07-18
summary: "Blinn-Phong Lighting Using Ray-Marching"
---

Now Vang supports multiple light sources!

I had a few issues initially with SSBO's that I was able to iron out during this release. This mostly had to do with how the data was being set on the GPU and how it was being updated.

Similar to before a ray now travels to a surface and gets the color at that point on the surface. Different from before though, now it will go through the lighting step. For each light in the scene (max 1000) it will ray march to that light source and check whether the surface is lit by the light or in shadow. Then it will calculate the ambient, diffuse, and specular contribution from that light and change the color based on those factors.

For demonstration purposes, I have added a headlight to the player. They can turn it on and off with the `F` key and use the `G` key to toggle the light being detached from the player. This basically allows the user to move a light around and see what it looks like from different angles.

This is not final and I still have a few more changes and optimizations to make. For example, the player can not currently place or destroy lights which should be possible so I will have to attach them to an object. I will also have to add some optimizations to how the light is being rendered. Currently it will calculate all values of contribution from the light no matter what. However, this should only be done if the light is even in range so I can add that conditional innitially. I also have had an idea to add a voxel octree structure to the lights that will dictate the farthest reaching light in an area. That way it can quickly rule out which lights it needs and which it doesn't.

This was a massive update though and I am very happy with how it turned out!
```