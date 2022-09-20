## The UV is handy!
- The UV is not only used for textures, you can also make awesome effect out of it, this is how.
- Let's say we want to make a white circle that fades, the center of the UV is (0.5, 0.5), so we calculate the color based on the distance of the current UV to the center.
    ```cpp
    float4 main(float2 uv : TEXCOORD) : SV_TARGET
    {
        float dist = distance(uv, float2(0.5f, 0.5f)) * 2.f;
        return 1.f - dist;
    }
    ```
- This is the result:
- ![](/img/uv-effect-1.png)
- Or we could have an interesting effect using some simple math:
    ```cpp
    #define M_PI 3.141592654f
    
    float4 main(float2 uv : TEXCOORD) : SV_TARGET
    {
        float dist = distance(uv, float2(0.5f, 0.5f)) * 2.f;
        float alpha = cos(dist * M_PI * 2.f * 5.f) * 0.5f + 0.5f;
        
        alpha *= 1.f - dist;
        return float4(color.rgb, alpha);
    }
    ```
- The result:
- ![](/img/uv-effect-2.png)

## Implement an animation
- By passing a `time` variable to the constant buffer, we could have animation:
    ```cpp
    cbuffer cbPerFrame : register(c0)
    {
        float4 colorAndTime : register(c0); // {R, G, B, time}
    };
    
    #define M_PI 3.141592654f
    
    float4 main(float2 uv : TEXCOORD) : SV_TARGET
    {
        float3 color = colorAndTime.rgb;
        float time = colorAndTime[3]; // or colorAndTime.a
        
        float dist = distance(uv, float2(0.5f, 0.5f)) * 2.f;
        float alpha = cos((dist - time * 0.1f) * M_PI * 2.f * 5.f) * 0.5f + 0.5f;
        alpha *= 1.f - dist;
        return float4(color, alpha);
    }
    ```
- And in C++:
    ```cpp
        static float time = 0.f; time += 0.01f;
        float psConstant[] = { 0.f, 1.f, 0.f, time };
        d3ddev->SetPixelShaderConstantF(0, psConstant, 1);
    ```
- The result:
- ![](/img/uv-effect-3.gif)