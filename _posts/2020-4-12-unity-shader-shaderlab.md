---
layout:       post
title:        "[Unity Shader] 二 ShaderLab了解"
subtitle:     "unity shader"
created:      '2020-05-08T01:30:37.980Z'
modified:     '2020-05-23T02:21:54.300Z'
author:       "CharlesZhong"
header-style: text
catalog:      true
...published:    false
tags:
    - Unity Shader
    - Shader
---

2020-4-12-unity-shader-shaderlab
# [Unity Shader] 二 ShaderLab了解
## 1.环境
Unity版本 2017.4.1
Visual Studio 2017
DX版本
编辑时间 2020.5
## 2.了解
Unity Shader是Unity对原生shader的一层封装，这样研发或者美术就在改动一个效果的时候，就不必去找复杂的底层shader改动，又或者是我只是改动一点地方，但是当前的效果也想保留，可能得再加另一个shader脚本，这时候如果用底层的shader，就得重复写很多代码，所以到unity的时候，为了方便，很多东西都封装好了，直接新建一个Unity Shader的脚本，然后就可以改动少量的代码就可以运行了（前提是写对了）。对于开发人员来说，使用Unity Shader，改动一行代码就能实现效果，有时候是好事，有时候也是坏事，改动一行代码就能快速完成需求当然好，但是不熟悉底层的话，就会“知其然而不知其所以然”，难以理解其中的关键点，所以分了两部分来写，一个是Unity Shader的，另一个就是底层的Shader，一个注重实现和一部分的底层理解，另一个就注重对底层的调用。
然后对于使用Unity Shader的话，则需要使用ShaderLab来写，以及根据需要调用的渲染底层接口，来配合使用HLSL语言（DX）还是GLSL（OpenGL），或者是CG语言（Nvidia）.

## 3.语义
### 3.1 顶点/片元着色器 Vertex shader/fragment shader
先看例子，使用的顶点/片元着色器编写
```
Shader "Custom/CustomShader"
{
  Properties{
    _Color("color", Color) = (0.5f, 0.5f, 0.5f, 0.5f)
  }
  SubShader
  {
    Tags{"Queue" = "Transparent"}
    Pass
    {
      CGPROGRAM
      Name "A"

      #pragma vertex vert
      #pragma fragment frag
      #include "UnityCG.cginc"

      struct a2v
      {
        position : POSITION;
      };
      struct v2f
      {
        color : COLOR0;
      };

      float4 _Color;

      v2f vert(a2v i)
      {
        v2f o;
        return o;
      }
      fixed4 frag(v2f o):SV_TARGET
      {
        return o.color;
      }
      ENDCG
    }
  }
  FallBack "Diffuse"
}
```

#### 3.1.1 属性部分  
Properties  

|     属性类型    |                             描述                                                                       |                     使用                      |      CG变量类型       |
|----------------|--------------------------------------------------------------------------------------------------------|-----------------------------------------------|----------------------|
| Range(min,max) |  创建Float属性，以滑动条的形式便于在最大值和最小值之间进行调节                                              | _Range("Range Property", Range(0,10)) = 0     |float, half, fixed    |
| Color          |  创建色块，可以在Inspector面板上通过拾色器获取颜色值=(float,float,float,float)                             | _Color("Color Property", Color) = (0,0,0,0)   |float4, half4, fixed4 |
| 2D             |  创建纹理属性，允许直接拖曳一个纹理                                                                       | _2D("2D Property", 2D) = "white" {}           |sampler2D |
| 3D             |  3D纹理                                                                                                | _3D("3D Property", 3D) = "black" {}           |sampler3D |
| Rect(旧)       |  ~~创建一个非2次方的纹理属性，作为2DGUI元素~~                                                             | ~~暂无~~ | |
| Cube           |  在Inspector面板上创建一个立方贴图属性，允许用户直接拖曳立方贴图作为着色器属性，如果用天空盒就用cube立方体贴图  | _Cube("Cube Property", Cube) = "red" {}       |samplerCube |
| Float          |  在Inspector面板上创建一个非滑动条的float属性                                                             | _Float("Float Property", Float) = 12.3        |float, half, fixed |
| Vector         |  创建一个拥有4个float值的属性，可以用于标记方向或颜色                                                      | _Vector("Vector Property", Vector) = (1,2,3,4)|float4, half4, fixed4 |
| Int            |  创建一个Int类型的属性                                                                                   | _Int("Int Property", Int) = 1                 | |

对于精度的数值类型,CG/HLSL中3种精度的数值类型

|   type   |    精度   |
|----------|----------------|
| float    | 最高精度的浮点值。通常使用32位来存储 |
| half     | 中等精度的浮点值。通常使用16位来存储，精度范围是-60000 ~ +60000 |
| fixed    | 最低精度的浮点值。通常使用11位来存储，精度范围是-2.0 ~ +2.0 |

#### 3.1.2 SubShader  
在SubShader里面会设置渲染状态RenderSetup和设置Tags
```
SubShader
{
  //可选
  [RenderSetup]
  //可选
  [Tags]
  Pass
  {
    ...
  }
}
```
##### 3.2.1 状态设置  

|   RenderSetup   |    说明   |   设置指令  |
|-----------------|-----------|------------|
| Cull            | 设置剔除模式；剔除背面/正面/关闭剔除 | Cull Back/Front/Off |
| ZTest           | 设置深度测试时使用的函数 | ZTest Less Greater/LEqual/GEqual/Equal/NotEqual/Always |
| ZWrite          | 开启/关闭深度写入 | ZWrite On/Off |
| Blend           | 开启并设置混合模式 | Blend SrcFactor DstFactor |

3.2.1.1 Cull

3.2.1.2 ZTest

3.2.1.3 ZWrite

3.2.1.4 Blend

##### 3.2.2 Tags  
使用键值对去声明的，Tags{"key1" = "value1" "key2" = "value2"}

| 标签类型               |   说明   |   例子   |
|-----------------------|----------|---------|
| Queue                 | 控制渲染顺序，指定该物体属于哪一个渲染队列，通过这种方式可以保证所有的透明物体可以再所有不透明物体后面被渲染，我们也可以自定义使用的渲染队列来控制物体的渲染顺序 | Tags{"Queue" = "Transparent"}
| RenderType            | 对着色器进行分类，例如这是一个不透明的着色器，或是这是一个透明的着色器等。这可以被用于着色器替换(Shader Replacement)功能 | Tags{"RenderType" = "Opaque"}
| DisableBatching       | 一些SubShader在使用Unity的批处理功能时会出现问题，例如使用了模型空间下的坐标来进行顶点动画。这时可以通过该标签来直接指明是否对该SubShader使用批处理 | Tags{"DisableBatching" = "True"} |
| ForceNoShadowCasting  | 控制使用该SubShader的物体是否会投射阴影 | Tags{"ForceNoShadowCasting" = "True"} |
| IgnoreProjector       | 如果该标签值为"True"，那么使用该SubShader的物体将不会受Projector的影响。通常用于半透明物体 | Tags{"IgnoreProjector" = "True"} |
| CanUseSpriteAtlas     | 当该SubShader是用于精灵(sprites)时，将该标签设为"False" | Tags{"CanUseSpriteAtlas" = "False"} |
| PreviewType           | 指明材质面板将如何预览该材质。默认情况下，材质将显示为一个球形，我们可以通过把该标签的值设为"Plane" "SkyBox"来改变预览类型 | Tags{"PreviewType" = "Plane"} |

3.2.2.1 Queue 渲染队列
代表的具体数值，所以可以Tags{"Queue" = "Transparent+1000"}，"+"左右不能加空格

|    Queue    |       参数        |
|-------------|-------------------|
|  Background |   1000            |
|  Geometry   |   2000            |
|  AlphaTest  |   2450            |
|  Transparent|   3000            |
|  Overlay    |   4000            |

3.2.2.2 RenderType 渲染类型  
这个标签再替代渲染做Post Effects时很重要，Unity内置的Image Effects就要根据它来决定如何替代渲染

|    RenderType       |       描述        |
|---------------------|-------------------|
|  Opaque             |                   |
|  Transparent        |                   |
|  TransparentCutout  |                   |
|  Backgroud          |                   |
|  Overlay            |                   |

#### 3.2.3 LOD

#### 3.2.4 Pass  
SubShader包装了一个渲染方案，而方案由具体的一个个Pass来执行。Pass内置的标签是针对渲染路径的，告诉渲染引擎这个Pass应该在什么样的渲染路径下被渲染。
```
SubShader
{
  ...
  Pass
  {
    [Name]
    [Tags]
    [RenderSetup]
    //代码部分
    [Code]
  }
  ...
}
```

3.3.4.1 Name
需要结合“UsePass”语义来使用，Unity内对于Name后的命名要大写，如
```
Shader "Custom/A"
SubShader
{
  Pass
  {
    Name "APASS"
  }
}
```
```
Shader "Custom/B"
SubShader{
  UsePass "Custom/A/APASS"
}
```

3.3.4.2 Pass中的Tags

|     Tags       |       |
|----------------|-------|
| LightMode      |
| RequireOptions |

3.3.4.2.1 LightMode
Unity支持3中RendereringPath，分别是VertexLit、Forward和Deferred Lighting，为此又定义了在Pass中使用的LightMode标签Vertex、ForwardBase、ForwardAdd、PrepassBase、PrepassFinal等，分别表示当前Pass是为在哪一个RenderingPath下设计使用的。

|    LightMode   |     含义     |
|----------------|-------------|
| Always         | 无论使用哪种渲染路径，该Pass总是会被渲染，但不会计算任何光照 |
| ForwardBase    | 用于前向渲染。该Pass会计算环境光、最重要的平行光、逐顶点/SH光源和光照贴图(Lightmaps) |
| ForwardAdd     | 用于前向渲染。该Pass会计算额外的逐像素光源，每个Pass对应一个光源 |
| PrepassBase    | 在旧版延迟渲染(legacy Deferred Lighting)中使用，该Pass将渲染法线和高光反射的指数部分 |
| PrepassFinal   | 在旧版延迟渲染(legacy Deferred Lighting)中使用，该Pass将通过组合纹理，光照和自发光来渲染得到最后的颜色 |
| Deferred       | 用于延迟渲染。该Pass会渲染G-缓冲(G-buffer) |
| ShadowCaster   | 将物体的深度信息渲染到阴影贴图(shadowmap)或者深度纹理中 |
| MotionVectors  | 用于计算每个对象的运动矢量 |
| Vertex         | 当对象没有进行光照映射(lightmap)时，在旧版顶点光照(legacy Vertex Lit Rendering)中使用；所有的顶点光都将被应用到 |
| VertexLMRGBM   | 当对象被光照时，在旧版顶点光照(legacy Vertex Lit rendering)中使用；在光照映射(lightmap)是RGBM编码(一般是PC和控制台场景中)时使用 |
| VertexLM       | 当对象被光照时，在旧版顶点光照(legacy Vertex Lit rendering)中使用；在光照映射(lightmap)是双LDR编码(一般是移动平台场景中)时使用 |

3.3.4.3 code部分
之后就是着重写代码去调整着色器的部分，这里可以用GLHL或者HLSL或者Cg着色语言来写代码(实际上这里的着色语言和原本的语言还是不同，是Unity对原生的着色语言进行的一层封装，一些函数和语法也跟原来的大部分都相同，所以可以按照原生的着色语言标准来写，但实际应该叫做Unity GLSL或者Unity HLSL或者Unity Cg)，比如用Unity Cg语言来写的话就是下面的
```
SubShader
{
  Pass
  {
    CGPROGRAM
    //CODE
    ENDCG
  }
}
```
使用Unity HLSL语言来写的话是下面的
```
SubShader
{
  Pass
  {
    HLSLPROGRAM
    //CODE
    ENDHLSL
  }
}
```

#### 3.1.3 FallBack  
在所有的渲染方案SubShader都不适用于这个平台时，就会使用默认的渲染方案。一般开发时不写，这样可以编辑查看自己写的渲染方案是不是对的，在发布时才写。
选项 FallBack "name" 或者 FallBack Off

### 3.2 Surface Shader 表面着色器
```
Shader "Custom/CustomSurfaceShader"
{
  Properties{
    //
  }
  SubShader{
    Tags{}
    CGPROGRAM
    #pragrma surface surf Limbert
    ENDCG
  }
}
```

## 4 其他语义
CustomEditor扩展Unity编辑器界面
Category对Shader命令分组

# 资料
书籍
- 《Unity Shader入门精要》

链接
- <https://zhuanlan.zhihu.com/p/93846000>
- <https://gameinstitute.qq.com/community/detail/114545>