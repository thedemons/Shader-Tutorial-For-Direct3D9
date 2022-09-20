# What a shader really is?
A shader determines the actual pixel being drawn on the screen, there are two basic types of shaders: Vertex Shader and Pixel Shader.

## Vertex Shader: 
- The vertex shader determines the screen coordinate of the vertex. It is usually used to translate the world position into a normalized screen coordinate. 
- A normalized screen coordinate ranges from (-1, -1) in the bottom left corner to (1, 1) in the upper right corner of the screen, with a Z coordinate indicates the near-far position (depth-index) of the vertex, it ranges from 0 to 1.
- The vertex shader is executed for n times of how many vertices you have, e.g for a single triangle the code is executed three times.

## Pixel shader: 
- The pixel shader determines the color of the pixel being drawn. 
- It will be executed n times for how many pixels are on the screen, it usually uses more processing power than a Vertex Shader.

## The shader pipeline:
- Let's say we have a triangle, which has three vertices (X,Y,Z) in the world position.
- The three vertices are passed to a Vertex Shader, which will do some calculations, and output three normalized screen coordinates. *(The Vertex Shader will be executed three times)*
- These three coordinates are then passed to a rasterizer, which we will talk about in the future. The rasterizer does some calculations and decided that there are 16 pixels needed to be drawn in total.
- These 16 pixels are then passed into the Pixel Shader, which does some calculations and gives a blue color, which is then drawn onto the screen. *(The Pixel Shader will be executed 16 times)*

![](/img/how-vertex-and-pixel-shader-work.png)


# What about Effects?
Effects are the combination of a Vertex and Pixel Shaders, it behaves exactly like the two of them separated, but how to use it in the code is somewhat different. Effects are deprecated so they won't be focused on in this tutorial.