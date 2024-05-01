---
title: "Plane-Assisted Ray Marching"
date: 2024-01-15
summary: "Rays travel block by block until they hit a block and a color is returned"
---

From the main camera a screen is projected in the direction that the player is stated to be looking. Then that screen is divided up by the number of pixels in the viewport and a ray is created for each one. Then there is a ray origin (camera origin) and a direction to travel.

So first the ray will check the sides of the cube that it is currently in to see which one it will hit. This is done through a ray-plane intersection which returns a point. Using the distance to that point we can find the surface that will be hit. After doing three ray-place intersections, which ever has the shortest distance is the surface that is hit.

There is also no need to check for all six planes of the cube since, in the X direction, the ray can only be traveling in either positive X, negative X, or is parallel to the x axis. So the first test is to figure out which of these states it is in. For simplicity the case where it is parallel is ignored and treated as postiive X. Doing this for each axis means we only need to check three surfaces. Next we can start the raymarch and keep traveling until a block is hit.

![Plane-Assisted Raymarch](raymarch.png)

It will travel block by block until eventually a block is hit. Currently this will just return a solid color for the block but this can easily be turned into UV coordinates using the intersection point.

Below I have included a snippet of the ray marching function in GLSL. It will check the three plane intersections and take the minimum. After that it will repeat traveling block by block until eventually it hits a block to stop at.

This code still has to be cleaned up and there are definitely ways that I can optimize it further but I am happy with where it currently is for the result I am looking for!

```GLSL
float totalDistance = 0.0f;
ivec3 currentBlockPos = getBlockCoords(rayOrigin);
uint previousBlock = getBlock(currentBlockPos);
uint currentBlock = previousBlock;
int blockSteps = 0;
float fogAmount = 0.0f;
float glassAmount = 0.0f;
bool entityHit = false;
while ((currentBlock == 1 || currentBlock == 2 || currentBlock == 15) && blockSteps < 1048 && !entityHit) {
    if (rayDirection.x == 0.0 && rayDirection.y == 0.0 && rayDirection.z == 0.0) {
        imageStore(screen, pixel_coords, vec4(1.0f, 1.0f, 1.0f, 1.0f));
        return;
    }
    
    vec3 signedDirection = sign(rayDirection);

    if (signedDirection.x == 0 && signedDirection.y == 0 && signedDirection.z == 0) {
        imageStore(screen, pixel_coords, vec4(0.0f, 0.0f, 1.0f, 1.0f));
        return;
    }

    ivec3 distDir = ivec3(round(signedDirection.x), 0, 0);
    float minDist = planeIntersectionDistance(rayOrigin, rayDirection, vec3(currentBlockPos.x + 0.5f * signedDirection.x, currentBlockPos.yz), vec3(-sign(rayDirection.x), 0.0f, 0.0f));

    float newDist = planeIntersectionDistance(rayOrigin, rayDirection, vec3(currentBlockPos.x, currentBlockPos.y + 0.5f * signedDirection.y, currentBlockPos.z), vec3(0.0f, -sign(rayDirection.y), 0.0f));
    if (newDist < minDist) {
        distDir = ivec3(0, round(signedDirection.y), 0);
        minDist = newDist;
    }
    newDist = planeIntersectionDistance(rayOrigin, rayDirection, vec3(currentBlockPos.xy, currentBlockPos.z + 0.5f * signedDirection.z), vec3(0.0f, 0.0f, -sign(rayDirection.z)));
    if (newDist < minDist) {
        distDir = ivec3(0, 0, round(signedDirection.z));
        minDist = newDist;
    }

    // Fog
    if (currentBlock == 2) {
        fogAmount += minDist;
    }

    // Glass
    if (currentBlock == 15) {
        glassAmount += minDist;
    }

    entityHit = entityCheck(rayOrigin, rayDirection, minDist);
    if (!entityHit) {
        currentBlockPos += distDir;
        previousBlock = currentBlock;
        currentBlock = getBlock(currentBlockPos);
        rayOrigin += rayDirection * minDist;
        totalDistance += minDist;
        blockSteps += 1;

        if (currentBlock == 15 && previousBlock != 15) { // Going into glass
            const vec3 normal = -vec3(distDir);
            rayDirection = refract(rayDirection, normal, glassRefractionIndex);
        } else if (previousBlock == 15 && currentBlock != 15) { // Coming out of glass
            const vec3 normal = -vec3(distDir);
            rayDirection = refract(rayDirection, normal, glassRefractionIndex);
        }
    }
}
```