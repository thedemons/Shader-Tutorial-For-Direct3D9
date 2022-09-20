## What is a constant buffer?
- A constant buffer is a way to pass data from your C++ code to the shader.
- For Vertex Shaders, we usually want it to know the World, View, and Projection matrix for it to translate the world position to the screen position.
- For Pixel Shaders, it is useful for setting time, color,.. value.

## How to pass the World View Projection matrix to Vertex Shader
- This is pretty straightforward, assuming you already have the World, View, and Projection matrix.
- In the shader, the matrix is column-major, not row-major, this is because the GPU is faster at calculating column-major matrix, so we need to transpose the matrix first. *I will not go into detail about how the matrix works here.*
    ```cpp
    D3DXMATRIX World, View, Projection;
    // get the current world, view and projection matrix
    d3ddev->GetTransform(D3DTS_WORLD, &World);
    d3ddev->GetTransform(D3DTS_VIEW, &View);
    d3ddev->GetTransform(D3DTS_PROJECTION, &Projection);
    
    D3DXMATRIX WorldViewProjection, WorldViewProjectionTransposed;
    WorldViewProjection = World * View * Projection;
    
    D3DXMatrixTranspose(&WorldViewProjectionTransposed, &WorldViewProjection);
    ```
- As we already specified that the `WorldViewProj` constant buffer in the Vertex Shader is at the `register(c0)` in [the previous chapter](/compiling-your-first-shader.md#compiling-a-vertex-shader):
    ```cpp
    cbuffer cbPerFrame : register(c0)
    {
        matrix WorldViewProj : register(c0);
    };
    ```
- We'll need to set the `StartRegister` parameter of the `SetVertexShaderConstantF` method to `0`. The `Vector4fCount` is set to `4`, which indicates that this is a 4x4 float buffer. *(Vector4fCount x 4)*
    ```cpp
    d3ddev->SetVertexShaderConstantF(0, WorldViewProjectionTransposed, 4);
    ```

## How to pass a color to Pixel Shader
- The format of the color is (R, G, B, A), ranges from 0.f to 1.f, let's make a green color:
    ```cpp
    float psConstant[] = {0.f, 1.f, 0.f, 1.f};
    ```
- As we already specified that the `color` constant buffer in the Pixel Shader is at the `register(c0)` in [the previous chapter](/compiling-your-first-shader.md#compiling-a-pixel-shader):
    ```cpp
    cbuffer cbPerFrame : register(c0)
    {
        float4 color : register(c0);
    };
    ```
- We'll need to set the `StartRegister` parameter of the `SetPixelShaderConstantF` method to `0`. The `Vector4fCount` is set to `1`, indicates that this is a 1x4 float buffer. *(Vector4fCount x 4)*
    ```cpp
    d3ddev->SetPixelShaderConstantF(0, psConstant, 1);
    ```

## What is a register?
- Think about it as an index in the buffer, each register will hold 128 bits of data, which is four float/int values.
- For example, you can have two colors for the pixel shader by:
    ```cpp
    cbuffer cbPerFrame : register(c0)
    {
        float4 color_a : register(c0);
        float4 color_b : register(c1);
    };
    ```
- And in C++:
    ```cpp 
    float psConstant[] = {
        0.f, 1.f, 0.f, 1.f, // color_a in the shader
        1.f, 0.f, 0.f, 1.f, // color_b in the shader
    };
    
    d3ddev->SetPixelShaderConstantF(0, psConstant, 2); // 2 x 4 float buffer
    ```
## Passing a single value to the constant buffer
- As you might have noticed, the buffer goes by 4 values at a time, and you might need to only pass a single value, in which case, you can do it like this:
    ```cpp
    cbuffer cbPerFrame : register(c0)
    {
        float time : register(c0);
        float angle : register(c1);
    };
    ```
    ```cpp
    float time = 10.f;
    float angle = D3DX_PI;
    float psConstant[] = {
        time, 0.f, 0.f, 0.f, // reserve 3 values
        angle, 0.f, 0.f, 0.f, // reserve 3 values
    };
    
    d3ddev->SetPixelShaderConstantF(0, psConstant, 2);
    ```
- But that is a waste of space and performance, do this instead:
    ```cpp
    cbuffer cbPerFrame : register(c0)
    {
        float timeAndAngle : register(c0);
    };
    // access it later by
    // float time = timeAndAngle[0];
    // float angle = timeAndAngle[1];
    ```
    ```cpp
    float time = 10.f;
    float angle = D3DX_PI;
    float psConstant[] = {
        time, angle, 0.f, 0.f, // reserve 2 values
    };
    
    d3ddev->SetPixelShaderConstantF(0, psConstant, 1);
    ```