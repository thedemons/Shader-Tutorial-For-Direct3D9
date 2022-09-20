## The frustration
- This is where I got frustrated with [CreateVertexShader documentation](https://learn.microsoft.com/en-us/windows/win32/api/d3d9/nf-d3d9-idirect3ddevice9-createvertexshader), its first parameter is `const DWORD *pFunction` and the explanation is `Pointer to an array of tokens that represents the vertex shader, including any embedded debug and symbol table information.`. Which is simply **CONFUSING**.
- After digging through a hell of useless documentation, I've realized that the `const DWORD *pFunction` is actually a pointer to the buffer containing the shader's raw data. What the hell Microsoft?

## How to actually load the shader
- First, we need to read the shader file into a buffer in memory:
    ```cpp
    std::ifstream shader("VertexShader.cso", std::ios::binary);
    if (!shader.is_open()) throw;
    
    std::vector<BYTE> fileBlob = std::vector<BYTE>(
        std::istreambuf_iterator<char>(shader),
        std::istreambuf_iterator<char>()
    );
    ```
- Then we pass the buffer to `CreateVertexShader`:
    ```cpp
    IDirect3DVertexShader9* g_pVShader;
    d3ddev->CreateVertexShader((DWORD*)fileBlob.data(), &g_pVShader);
    ```
- The same goes for the Pixel Shader:
    ```cpp
    IDirect3DPixelShader9* g_pPShader;
    d3ddev->CreatePixelShader((DWORD*)fileBlob.data(), &g_pPShader);
    ```