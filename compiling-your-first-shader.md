## Adding a Vertex Shader to Visual Studio Project
- In Visual Studio, right-click on the project in the `Solution Explorer`, and select `Add -> New Item`.
- In the window opened, select `HLSL` in the left menu then select `Vertex Shader File (.hlsl)`. If you don't have this option, simply choose any other options and name it with `.hlsl` as the format. 
- ![](/img/creating-a-shader-in-vs.png)
- Right-click on the new .hlsl file you've just created, and select `Properties` at the bottom of the menu.
- In the properties window, select `General` on the left, make sure the `Item Type` is set to `HLSL Compiler`.
- Head to the `HLSL Compiler -> General` menu on the left, set the `Shader Type` to `Vertex Shader (/vs)`, and `Shader Model` to `Shader Model 3(/3_0)`.
- ![](/img/configuring-shader-in-vs.png)

## Compiling a Vertex Shader
- We will explain the code later, just make sure it is compilable for now.
- Paste the code below and simply hit Build *(Ctrl+B)*.
- It should build a file named *.cso in your out directory.
    ```hlsl
    cbuffer cbPerFrame : register(c0)
    {
        matrix WorldViewProj : register(c0);
    };
    
    float4 main(float3 pos : POSITION) : SV_Position
    {
        return mul(float4(pos, 1.f), WorldViewProj);
    }
    ```

## Compiling a Pixel Shader
- Simply do the steps above for Vertex Shader, but in the properties window, set the `Shader Type` to `Pixel Shader (/ps)`.
- Paste the code below and simply hit Build *(Ctrl+B)*.
    ```hlsl
    cbuffer cbPerFrame : register(c0)
    {
        float4 color : register(c0);
    };
    
    float4 main() : SV_TARGET
    {
        return color;
    }
    ```