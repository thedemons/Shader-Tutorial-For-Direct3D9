## Your first triangle with Shader
You've come a long way, it's now time to see what you have achieved with your own eye!

This is a simple proof-of-concept code, with no error-checking or optimizing in mind, you should not actually use it other than the purpose of quickly put the triangle onto the screen.

```cpp
std::vector<BYTE> ReadFileToBuffer(const char* fileName)
{
    std::ifstream shader(fileName, std::ios::binary);
    if (!shader.is_open()) throw;

    std::vector<BYTE> fileBlob = std::vector<BYTE>(
        std::istreambuf_iterator<char>(shader),
        std::istreambuf_iterator<char>()
    );
    return fileBlob;
}

void MainLoop()
{
    static IDirect3DPixelShader9* g_pPShader = nullptr;
    static IDirect3DVertexShader9* g_pVShader = nullptr;
    
    // load the shader on first run
    if (g_pPShader == nullptr || g_pVShader == nullptr)
    {
        auto vsBlob = ReadFileToBuffer("VertexShader.cso");
        auto psBlob = ReadFileToBuffer("PixelShader.cso");

        d3ddev->CreatePixelShader((DWORD*)psBlob.data(), &g_pPShader);
        d3ddev->CreateVertexShader((DWORD*)vsBlob.data(), &g_pVShader);
    }

    // get the WorldViewProjection transposed and pass it to vertex shader
    D3DXMATRIX World, View, Projection;
    d3ddev->GetTransform(D3DTS_WORLD, &World);
    d3ddev->GetTransform(D3DTS_VIEW, &View);
    d3ddev->GetTransform(D3DTS_PROJECTION, &Projection);

    D3DXMATRIX WorldViewProjection, WorldViewProjectionTransposed;
    WorldViewProjection = World * View * Projection;

    D3DXMatrixTranspose(&WorldViewProjectionTransposed, &WorldViewProjection);
    d3ddev->SetVertexShaderConstantF(0, WorldViewProjectionTransposed, 4);

    // set the color for pixel shader
    float psConstant[] = { 0.f, 1.f, 0.f, 1.f };
    d3ddev->SetPixelShaderConstantF(0, psConstant, 1);

    // create the vertex as we would normally do
    static struct CustomVertex { float x, y, z; };
    static CustomVertex vertices[] = {
        {0.f, 0.f, 1.f},
        {1.f, 0.f, -1.f},
        {-1.f, 0.f, -1.f},
    };

    IDirect3DVertexBuffer9* g_pVB;
    d3ddev->CreateVertexBuffer(sizeof(vertices), D3DUSAGE_WRITEONLY, D3DFVF_XYZ, D3DPOOL_MANAGED, &g_pVB, nullptr);

    void* pVertices;
    g_pVB->Lock(0, sizeof(vertices), &pVertices, 0);
    memcpy(pVertices, vertices, sizeof(vertices));
    g_pVB->Unlock();

    d3ddev->SetStreamSource(0, g_pVB, 0, sizeof(CustomVertex));

    // tell directx to use our shader
    d3ddev->SetVertexShader(g_pVShader);
    d3ddev->SetPixelShader(g_pPShader);

    // draw the triangle
    d3ddev->DrawPrimitive(D3DPT_TRIANGLELIST, 0, 1);

    g_pVB->Release();
}
```

## The result
![](/img/first-triangle.png)