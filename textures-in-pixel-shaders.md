# The concept of UV
- The UV is just a normalized XY coordinate of the texture, it ranges from (0,0) in the top left corner to (1,1) in the bottom right corner of the texture.
- In a Pixel Shader, you need these coordinates in order to look up the texture to see what is the color for that specific coordinate.

## Using textures in pixel shaders
**Important**: The vertex shader must output the uv coordinate in order for this to work, see [the previous chapter](/the-input-and-output-of-shaders.md#the-output-of-vertex-shaders).
- First, define some global variables:
    ```cpp
    sampler2D gTexSampler : register(s0) =
    sampler_state 
    {
        Texture = <gTexture>;
        MinFilter = LINEAR;
        MagFilter = LINEAR;
        MipFilter = LINEAR;
    };
    ```
- NOTE: Apparently the `Texture = <g_MeshTexture>` in the [documentation](https://learn.microsoft.com/en-us/windows/win32/direct3dhlsl/dx-graphics-hlsl-sampler#example) is not important, it is only be used for Effects, which won't be mentioned here.
- And use the `tex2D()` method to lookup the color in the texture at the `uv` coordinate.
    ```cpp
    float4 main(float2 uv : TEXCOORD) : SV_TARGET
    {
        return tex2D(gTexSampler, uv);
    }
    ```

## Using multiple textures
- Just like in the constant buffer, we use the register index to achieve it:
    ```cpp
    sampler2D gTexSampler1 : register(s0) =
    sampler_state 
    {
        MinFilter = LINEAR;
        MagFilter = LINEAR;
        MipFilter = LINEAR;
    };
    sampler2D gTexSampler2 : register(s1) =
    sampler_state
    {
        MinFilter = LINEAR;
        MagFilter = LINEAR;
        MipFilter = LINEAR;
    };
    
    float4 main(float2 uv : TEXCOORD) : SV_TARGET
    {
        if (uv.x > 0.5f)
            return tex2D(gTexSampler2, uv);
        else
            return tex2D(gTexSampler1, uv);
    }
    ```

## The code
```cpp
    // create the vertex as we would normally do
    static struct CustomVertex { float x, y, z, u, v; };
    static CustomVertex vertices[] = {
        {-1.f, 0.f,  1.f, 0.f, 0.f},
        { 1.f, 0.f,  1.f, 1.f, 0.f},
        {-1.f, 0.f, -1.f, 0.f, 1.f},
        { 1.f, 0.f, -1.f, 1.f, 1.f},
    };

    IDirect3DVertexBuffer9* g_pVB;
    d3ddev->CreateVertexBuffer(sizeof(vertices), D3DUSAGE_WRITEONLY, D3DFVF_XYZ | D3DFVF_TEX1, D3DPOOL_MANAGED, &g_pVB, nullptr);

    void* pVertices;
    g_pVB->Lock(0, sizeof(vertices), &pVertices, 0);
    memcpy(pVertices, vertices, sizeof(vertices));
    g_pVB->Unlock();

    d3ddev->SetStreamSource(0, g_pVB, 0, sizeof(CustomVertex));
    d3ddev->SetFVF(D3DFVF_XYZ | D3DFVF_TEX1);
    d3ddev->SetTexture(0, texture1);
    d3ddev->SetTexture(1, texture2);


    // tell directx to use our shader
    d3ddev->SetVertexShader(g_pVShader);
    d3ddev->SetPixelShader(g_pPShader);

    // draw the texture
    d3ddev->DrawPrimitive(D3DPT_TRIANGLESTRIP, 0, 2);

    g_pVB->Release();
```

## The output
![](/img/using-two-textures.png)