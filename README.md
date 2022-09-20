# HLSL Shaders Tutorial for Direct3D 9

## Introduction
After weeks of desperate searching on the internet for proper documentation on how to work with shaders in Direct3D 9, I finally grasped the basic concept of it and decided to make a short beginner-friendly tutorial.

## The problem
DX9 is old, to say the least, but it is also the easiest starting point if you want to learn DX11 or DX12. <br>
There are a few good tutorials out there on how to draw your first triangle, load your first mesh, but I've found none talking about shaders, which is a pretty important part if you wanted to make eye-candy effects or optimize performance. <br>
Even [Microsoft's documentation on how to load a shader](https://docs.microsoft.com/en-us/windows/win32/api/d3d9/nf-d3d9-idirect3ddevice9-createvertexshader) is very-very confusing.

## The goal of this tutorial
This tutorial aims for a fast and easy way to learn shaders. The goal is to provide the shortest, most intuitive explanation of shaders. It won't teach you in-depth advanced techniques but rather gives you a basic concept of how everything works.

## Table of Contents
- [Shaders vs. Effects explained](/shaders-vs-effects-explained.md)
- [Compiling your first shader](/compiling-your-first-shader.md)
- [Loading your first shader](/loading-your-first-shader.md)
- [Constant Buffer explained](/constant-buffer-explained.md)
- [Your first triangle with shaders](/your-first-triangle-with-shader.md)
- [The input and output of shaders](/the-input-and-output-of-shaders.md)
- [Textures in Pixel Shaders](/textures-in-pixel-shaders.md)
- [Animation in Pixel Shaders](/ultilizing-the-uv-in-pixel-shaders.md)
