## The input of Vertex Shaders
- In the [Compiling your first shader](/compiling-your-first-shader.md) tutorial, this code was used:
    ```cpp
    float4 main(float3 pos : POSITION) : SV_Position
    {
        return mul(float4(pos, 1.f), WorldViewProj);
    }
    ```
- Let's look at the parameter of the main function, it accepts a `float3` with a `POSITION` semantic which indicates that it needs an XYZ position as the input. Which is equal to `D3DFVF_XYZ` if you don't use vertex shaders.
- But vertices might have multiple inputs, not just a position, for example, the extra UV coordinate of a textured vertex. Here is how you would do it:
    ```cpp
    struct VS_Input
    {
        float3 pos : POSITION;
        float2 uv : TEXCOORD;
    };
     
    float4 main(VS_Input vin) : SV_Position
    {
        return mul(float4(vin.pos, 1.f), WorldViewProj);
    }
    ```

## The output of Vertex Shaders
- In the code above, we didn't use the UV coordinate from the input, but we must use it because the output of Vertex Shader will eventually get passed to the Pixel Shader and it needs the UV to look up the texture.
- So we need to return the UV as well:
    ```cpp
    struct VS_Input
    {
        float3 pos : POSITION;
        float2 uv : TEXCOORD;
    };
     
    struct VS_Output
    {
        float4 pos : SV_Position;
        float2 uv : TEXCOORD;
    };
    
    VS_Output main(VS_Input vin)
    {
        VS_Output vout;
        vout.pos = mul(float4(vin.pos, 1.f), WorldViewProj);
        vout.uv = vin.uv;
        return vout;
    }
    ```

## The input of Pixel Shaders
- In most cases, you want to match the input of the pixel shader to the output of the vertex shader used.
    ```cpp
    struct VS_Output
    {
        float4 pos : SV_Position;
        float2 uv : TEXCOORD;
    };
    
    float4 main(VS_Output pin) : SV_TARGET
    {
        // we will learn how to use the tex2D function in the next chapter
        return tex2D(gTexSampler, pin.uv);
    }
    ```
- But you can also do it like this and it will use the semantics to match the input with the output from the Vertex Shader:
    ```cpp
    float4 main(float2 uv : TEXCOORD) : SV_TARGET
    {
        return tex2D(gTexSampler, uv);
    }
    ```

