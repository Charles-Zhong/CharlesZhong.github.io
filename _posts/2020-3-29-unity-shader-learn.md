---
layout:       post
title:        "[Unity Shader] 一 整体了解"
subtitle:     "unity shader"
created:      '2020-05-07T11:17:03.346Z'
modified:     '2020-05-23T01:39:52.423Z'
author:       "CharlesZhong"
header-style: text
catalog:      true
...published:    false
tags:
    - Unity Shader
    - Shader
---


## 环境
Unity 2017.4.1
DX 

## 1 Unity Shader分类
1.1 表面着色器 Surface Shader  
表面着色器的底层也是按照顶点/片元着色器来写的，所以之后写例子时都基本先按照顶点/片元着色器来写.

1.2 顶点/片元着色器 Vertex/Fragment Shader

1.3 固定管线着色器 Fixed Function Shader

## 2 渲染管线  
渲染管线的输入是一个虚拟摄像机、一些光源、一些shader以及纹理等，最终生成或者是渲染一张二维纹理，就是屏幕上显示的一切。

``` mermaid
graph LR
1(顶点数据) -- 模型空间下的模型数据和材质和shader等 --> 2(顶点着色器)
subgraph 应用阶段CPU
1(顶点数据)
end
subgraph 几何阶段GPU
2(顶点着色器) --> 3(曲面细分着色器) --> 4(几何着色器) --> 5(裁剪) --> 6(屏幕映射)
end
subgraph 光栅化阶段GPU
6(屏幕映射) -- 裁剪空间下的模型顶点数据 --> 7(三角形设置) --> 8(三角形遍历) --> 9(片元着色器) --> 10(逐片元操作) --> 11(屏幕图像)
end
style 2 fill:#bbf,stroke:#f66,stroke-width:2px,color:#fff,stroke-dasharray:5,5
style 9 fill:#bbf,stroke:#f66,stroke-width:2px,color:#fff,stroke-dasharray:5,5
```

<!---
``` mermaid
graph LR
-->
[//]: #  (顶点数据 --> 顶点着色器 --> 曲面细分着色器 --> 几何着色器 --> 裁剪 --> 屏幕映射 --> 三角形设置 --> 三角形遍历 --> 片元着色器 --> 逐片元操作 --> 屏幕图像)
<!---
```
-->

[//]: # (下面是注释部分)
[comment]: <> (This is a comment, it will not be included)
[comment]: <> (in  the output file unless you use it in)
[comment]: <> (a reference style link.)
[//]: <> (This is also a comment.)
[//]: # (This may be the most platform independent comment)
<!---
comment
-->
[//]: # (上面是注释部分)

<!---
其中
<details open>
  <summary>应用阶段</summary>
  <kbd>顶点数据</kbd>
</details>
<details close>
  <summary>几何阶段</summary>
  <kbd>顶点着色器</kbd>
  <kbd>曲面细分着色器</kbd>
  <kbd>几何着色器</kbd>
  <kbd>裁剪</kbd>
  <kbd>屏幕映射</kbd>
</details>
<details close>
  <summary>光栅化阶段</summary>
  <kbd>三角形设置</kbd>
  <kbd>三角形遍历</kbd>
  <kbd>片元着色器</kbd>
  <kbd>逐片元操作</kbd>
  <kbd>屏幕映射</kbd>
</details>
-->

#### 2.1 顶点着色器
顶点着色器是流水线的第一个阶段，它的输入来自于CPU。顶点着色器的处理单元是顶点，也就是说输入进来的每个顶点都会调用一次顶点着色器。
顶点着色器需要完成的工作主要有：<kbd>坐标变换</kbd>和<kbd>逐顶点光照</kbd>。当然，除了这两个主要任务外，顶点着色器还可以输出后续阶段所需的数据。
坐标变换，顾名思义，就是对顶点的坐标进行某种变换。例如我们可以通过改变顶点位置来模拟水面，布料等。
一个最基本的顶点着色器必须完成的一个工作是：把顶点坐标从模型空间转换到齐次剪裁空间。

#### 2.2 裁剪
由于我们的场景可能会很大，而摄像机的视野范围很有可能不会覆盖所有的场景物体，一个很自然的想法就是，那些不在摄像机视野范围内的物体不需要被处理，而裁剪就是为了完成这个目的而被提出来的。

一个图元和摄像机的关系有3种：
- 完全在视野里
- 部分在视野里
- 完全在视野外

部分在视野内的图元需要裁剪，例如一条线段的一个顶点在视野内，而另一个顶点在视野外，那么视野外部的顶点应该使用一个新的顶点来代替，这个新的顶点位于这条线段和视野边界的交点处。

#### 2.3 屏幕映射
这一步输入的坐标仍然是三维坐标系。屏幕映射的任务是把每个图元的x和y坐标转换到屏幕坐标系下，屏幕坐标系是一个二维坐标系，它和我们用于显示画面的分辨率有很大关系。
屏幕映射得到的屏幕坐标决定了这个顶点对应屏幕上哪个像素以及距离这个像素有多远，
OpenGL的屏幕坐标原点是左下角，而DirectX是左上角，如果你发现你得到的图像是倒转的，那么很有可能就是这个原因造成的。

#### 2.4 三角形设置
由这一步就进入了光栅化阶段，从上一阶段输出的信息是屏幕坐标下的顶点位置以及它们相关的额外信息，如深度值、法线方向、视角方向等，光栅化有两个最重要的目标：计算每个图元覆盖了哪些像素，以及为这些像素计算他们的颜色。光栅化的第一个流水线阶段是三角形设置，这个阶段会计算光栅化一个三角网格所需的信息。

具体来说，上一个阶段输出的都是三角网格的顶点，即我们得到的是三角网格每条边的两个端点。但如果要得到整个三角网格对像素的覆盖情况，我们就必须计算每条边上的像素坐标。为了能够计算边界像素的坐标信息，我们就需要得到三角形边界的表示方式。这样一个计算三角形网格表示数据的过程就叫做三角形设置，它的输出是为了下一个阶段做准备。

#### 2.5 三角形遍历
三角形遍历阶段将会检查每个像素是否被一个三角形网格所覆盖。如果被覆盖的话，就会生成一个片元，而这样一个找到哪些像素被三角形网格覆盖的过程就是三角形遍历，这个阶段也被称为扫描变换。
三角形遍历阶段会根据上一个阶段的计算结果来判断一个三角网格覆盖了哪些像素，并使用三角网格3个顶点的顶点信息对整个覆盖区域的像素进行插值。

#### 2.6 片元着色器
片元着色器是另一个非常重要的可编程着色器阶段，片元着色器的输入是上一个阶段对顶点信息差值得到的结果，更具体来说，是根据那些从顶点着色器输出的数据差值得到的。而它的输出是一个或多个颜色值。
这一阶段可以完成很多重要的渲染技术，其中最重要的技术之一就是纹理采样。为了在片元着色器中进行纹理采样，我们通常会在顶点着色器阶段输出每个顶点对应的纹理坐标，然后和经过光栅化阶段对三角网格的3个顶点对应的纹理坐标进行插值后，就可以得到其覆盖的片元的纹理坐标了。

#### 2.7 逐片元操作
逐片元操作是OpenGL中的说法，在DirectX中，这一阶段被称为输出合并阶段。
这一阶段有几个主要任务：
- 决定片元的可见性。设计很多测试工作，如深度测试，模板测试等。
- 如果一个片元通过了所有的测试，就需要吧这个片元的颜色值和已经存储在颜色缓冲区中的颜色进行合并。

## 3 光照模型  

> 光照模型，Lighting/Illumination Model

#### 3.1 平面着色 Flat Shading

#### 3.2 高洛德着色 Gouraud Shading

<!---
### 3.3 兰伯特着色 Lambert Shading（基础漫反射）
属于最基本的光照模型，不考虑光照反射，
-->

<!---
### 3.4 冯氏着色 Phong Shading（镜面反射）
-->
#### 3.5 Blinn-Phong Shading（修正镜面光）

#### 3.6 漫反射光照模型
> 漫反射光照模型 (Diffuse Lighting Model)、又名兰伯特光照模型(Lambert Lighting Model)

计算公式：$\ce{ C_{{diffuse}} } = (C_{{light}} * M_{{diffuse}}) * max(0, dot(n * l))$ <!--- 或者 $\ce{ C_{{diffuse}} } = (C_{{light}} * M_{{diffuse}}) * max(0, cos(a))$ -->  

<kbd>$\ce{ C_{{diffuse}} }$</kbd>是最后计算出来的漫反射光照，<kbd>$\ce{ C_{{light}} }$</kbd>是入射光的颜色和强度，<kbd>$\ce{ M_{{diffuse}} }$</kbd>是材质的漫反射系数，<kbd>n</kbd>为表面法线，<kbd>l</kbd>为光源方向，最后由表面法线和光源方向计算出来的<kbd>max(0, n * l)</kbd>则是先算出表面法线和光源方向的点乘，然后避免算出的结果为负数，就用max()函数取最大值。  

3.6.1 逐顶点光照

```
// Upgrade NOTE: replaced '_World2Object' with 'unity_WorldToObject'

Shader "UnityShaderBookLearn/Chapter6/Custom Diffuse Vertex Shader"
{
	Properties
	{
		_Diffuse("diffuse", Color) = (1,1,1,1)
	}
	SubShader
	{
		Pass
		{
			Tags{"LightMode" = "ForwardBase"}

			CGPROGRAM
			#pragma vertex vert
			#pragma fragment frag
			#include "UnityCG.cginc"
			#include "Lighting.cginc"
			//设置了光照模式以及引用了unity内置的光照文件Lighting.cginc，才能使用unity内置的光照变量，比如_LightColor0

			struct appdata
			{
				float4 vertex : POSITION;
				float3 normal : NORMAL;
			};

			struct v2f
			{
				float4 vertex : SV_POSITION;
				fixed3 color : COLOR;
			};

			fixed4 _Diffuse;
			
			v2f vert (appdata v)
			{
				v2f o;
				o.vertex = UnityObjectToClipPos(v.vertex);

				//拿到环境光的颜色 UNITY_LIGHTMODEL_AMBIENT从Lighting.cginc内找到
				float3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz;

				//将当前顶点的法线从模型空间转换到世界空间下，并归一化 unity_WorldToObject从Lighting.cginc内找到
				fixed3 _worldNormal = normalize(mul(v.normal, (float3x3)unity_WorldToObject));

				//获得世界空间下的入射光，并归一化 _WorldSpaceLightPos0从Lighting.cginc内找到
				fixed3 _worldLight = normalize(_WorldSpaceLightPos0.xyz);
				float3 diffuse = _LightColor0.xyz * _Diffuse.xyz * saturate(dot(_worldNormal, _worldLight));
				o.color = ambient + diffuse;

				return o;
			}
			
			fixed4 frag (v2f i) : SV_Target
			{
				return fixed4(i.color, 1);
			}
			ENDCG
		}
	}
}
```
缺点：没有光照射的地方就是全暗的，而且逐顶点着色在明暗交界处会有锯齿，所以可以改用逐像素计算会比较好些，可以使光照效果更平滑一些。

3.6.2 逐像素计算
```
Shader "UnityShaderBookLearn/Chapter6/Custom Diffuse Pixel Shader"
{
	Properties
	{
		_Diffuse ("Diffuse", Color) = (1, 1, 1, 1)
	}
	SubShader
	{
		Pass
		{
			Tags {"LightMode" = "ForwardBase"}

			CGPROGRAM
			#pragma vertex vert
			#pragma fragment frag
			
			#include "UnityCG.cginc"
			#include "Lighting.cginc"

			struct appdata
			{
				float4 vertex : POSITION;
				float3 normal : NORMAL;
			};

			struct v2f
			{
				float4 vertex : SV_POSITION;
				float3 worldNormal : TEXCOORD0;
			};

			fixed4 _Diffuse;
			
			v2f vert (appdata v)
			{
				v2f o;
				//UnityObjectToClipPos将模型的顶点从模型空间转换到裁剪空间
				o.vertex = UnityObjectToClipPos(v.vertex);
				o.worldNormal = mul(v.normal, (float3x3)unity_WorldToObject);
				return o;
			}
			
			fixed4 frag (v2f i) : SV_Target
			{
				fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz;
				float3 _worldNormal = normalize(i.worldNormal);
				float3 _worldLight = normalize(_WorldSpaceLightPos0.xyz);
				fixed3 diffuse = _LightColor0.xyz * _Diffuse.xyz * saturate(dot(_worldNormal, _worldLight));
				fixed3 color = ambient + diffuse;
				return fixed4(color, 1);
			}
			ENDCG
		}
	}
}

```
缺点：没有光照射的地方就是全暗的，对于逐顶点计算和逐像素计算都是一样的缺点，然后因为片元的量要远远大于顶点的量，所以片元计算的性能要大。


#### 3.7 高光反射光照模型
> 高光反射光照模型(Specular Lighting Model)、又名镜面反射、又名冯氏光照模型(Phone Lighting Model)

计算公式：$\ce{ C_{{specular}} } = (C_{{light}} * M_{{specular}}) * max(0,v,r)^{\ce{ m_{{gloss}} }}$

需要4个参数，入射光线的颜色和强度<kbd>$\ce{ C_{{specular}} }$</kbd>，材质的高光反射系数<kbd>$\ce{ M_{{specular}} }$</kbd>，视角方向

逐顶点着色
```
//基础高光反射光照模型的顶点着色实现
Shader "UnityShaderBookLearn/Chapter6/Custom Specular Vertex Shader"
{
	Properties
	{
		_Gloss ("Gloss", Range(8.0, 256)) = 20
		_Diffuse ("Diffuse", Color) = (1,1,1,1)
		_Specular ("Specular", Color) = (1,1,1,1)
	}
	SubShader
	{
		Tags { "RenderType"="Opaque" }
		LOD 100

		Pass
		{
			Tags { "LightMode"="ForwardBase"}
			CGPROGRAM
			#pragma vertex vert
			#pragma fragment frag
			
			#include "UnityCG.cginc"
			#include "Lighting.cginc"

			struct appdata
			{
				float4 vertex : POSITION;
				float3 normal : NORMAL;
			};

			struct v2f
			{
				float4 vertex : SV_POSITION;
				fixed3 color : COLOR;
			};

			float _Gloss;
			fixed4 _Diffuse;
			fixed4 _Specular;
			
			v2f vert (appdata v)
			{
				v2f o;
				o.vertex = UnityObjectToClipPos(v.vertex);

				//获得环境光
				fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz;

				//变换模型的顶点法线
				fixed3 _worldNormal = normalize(mul(v.normal, (float3x3)unity_WorldToObject));

				//获得世界空间下的光源方向
				fixed3 _worldLight = normalize(_WorldSpaceLightPos0.xyz);

				//计算漫反射
				fixed3 diffuse = _LightColor0.xyz * _Diffuse.xyz * saturate(dot(_worldNormal, _worldLight));

				//获得世界空间下的反射光
				fixed3 _reflectDir = normalize(reflect(-_worldLight, _worldNormal));

				//获得世界空间下的视图方向
				fixed3 _viewDir = normalize(_WorldSpaceCameraPos.xyz - mul(unity_WorldToObject, v.vertex).xyz);

				//计算高光反射
				fixed3 specular = _LightColor0.xyz * _Specular.rgb * pow(saturate(dot(_reflectDir, _viewDir)), _Gloss);

				//最后计算总的颜色值
				o.color = ambient + diffuse + specular;
				return o;
			}
			
			fixed4 frag (v2f i) : SV_Target
			{
				return fixed4(i.color, 1);
			}
			ENDCG
		}
	}
}
```

逐片元着色
```
//基础高光反射模型的片元着色实现
Shader "UnityShaderBookLearn/Chapter6/Custom Specular Pixel Shader"
{
	Properties
	{
		_Gloss ("Gloss", Range(8.0, 256)) = 20
		_Diffuse ("Diffuse", Color) = (1,1,1,1)
		_Specular ("Specular", Color) = (1,1,1,1)
	}
	SubShader
	{
		Tags { "RenderType"="Opaque" }
		LOD 100

		Pass
		{
			Tags { "LightMode"="ForwardBase"}
			CGPROGRAM
			#pragma vertex vert
			#pragma fragment frag
			
			#include "UnityCG.cginc"
			#include "Lighting.cginc"

			struct appdata
			{
				float4 vertex : POSITION;
				float3 normal : NORMAL;
			};

			struct v2f
			{
				float4 vertex : SV_POSITION;
				fixed3 color : COLOR;
				float3 worldNormal : TEXCOORD;
			};

			float _Gloss;
			fixed4 _Diffuse;
			fixed4 _Specular;
			
			v2f vert (appdata v)
			{
				v2f o;
				o.vertex = UnityObjectToClipPos(v.vertex);
				o.worldNormal = mul(v.normal, (float3x3)unity_WorldToObject);
				return o;
			}
			
			fixed4 frag (v2f i) : SV_Target
			{
				//获得环境光
				fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz;

				//变换模型的顶点法线
				fixed3 _worldNormal = normalize(i.worldNormal);

				//获得世界空间下的光源方向
				fixed3 _worldLight = normalize(_WorldSpaceLightPos0.xyz);

				//计算漫反射
				fixed3 diffuse = _LightColor0.xyz * _Diffuse.xyz * saturate(dot(_worldNormal, _worldLight));

				//获得世界空间下的反射光
				fixed3 _reflectDir = normalize(reflect(-_worldLight, _worldNormal));

				//获得世界空间下的视图方向
				fixed3 _viewDir = normalize(_WorldSpaceCameraPos.xyz - mul(unity_WorldToObject, i.vertex).xyz);

				//计算高光反射
				fixed3 specular = _LightColor0.xyz * _Specular.rgb * pow(saturate(dot(_reflectDir, _viewDir)), _Gloss);

				//最后计算总的颜色值
				fixed3 color = ambient + diffuse + specular;
				return fixed4(color, 1);
			}
			ENDCG
		}
	}
}
```

#### 3.8 半兰伯特着色
> 半兰伯特着色 Half-Lambert Shading，由V社提出的光照模型，最初设计是在《半条命》之上的光照模型，由漫反射光照模型（兰伯特光照模型）基础上的修改。

计算公式：$\ce{ C_{{diffuse}} } = (C_{{light}} * M_{{diffuse}}) * (a(dot(n * l) + b))$

对<kbd>n * l</kbd>的点积做一次a倍的缩放，再加上b的偏移。绝大多数情况下，a和b都是0.5，即公式为$\ce{ C_{{diffuse}} } = (C_{{light}} * M_{{diffuse}}) * (0.5(dot(n * l) + 0.5))$

<!---注释
为什么是0.5呢，可以看数学公式的几何表示，前面知道点积的计算是两个矢量的夹角余弦值，假设a是两个矢量的夹角，即cos(a)，然后cos的几何表示的值范围为[-1,1]，然后乘0.5就是[-0.5,0.5]，再加上0.5就是[0,1]，最后得到cos的值范围就是[0,1]，这样计算出来的背面没有光照射到地方就确保了不会全黑。
-->

原理：是针对漫反射的缺点进行的改进的，在计算漫反射的夹角余弦值时，乘0.5然后加上0.5，就保证了暗处不会是全暗的。

逐顶点着色
```
//有些代码跟漫反射的逐顶点着色是一样的，就不重复写了
Shader "..."
{
  ...
  SubShader
  {
    ...
    Pass
    {
      ...
      v2f vert (appdata v)
			{
				v2f o;
				o.vertex = UnityObjectToClipPos(v.vertex);

				//拿到环境光的颜色 UNITY_LIGHTMODEL_AMBIENT从Lighting.cginc内找到
				float3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz;

				//将当前顶点的法线从模型空间转换到世界空间下，并归一化 unity_WorldToObject从Lighting.cginc内找到
				fixed3 _worldNormal = normalize(mul(v.normal, (float3x3)unity_WorldToObject));

				//获得世界空间下的光源方向，并归一化 _WorldSpaceLightPos0从Lighting.cginc内找到 这是当前场景只有一个光源并且光源是平行光才能用
				fixed3 _worldLight = normalize(_WorldSpaceLightPos0.xyz);
        fixed _halfLambert = dot(_worldNormal, _worldLight) * 0.5 + 0.5;
				float3 diffuse = _LightColor0.xyz * _Diffuse.xyz * _halfLambert;
        
				o.color = ambient + diffuse;
				return o;
			}
      ...
    }
  }
}
```

逐像素着色
```
//...是因为跟上面的漫反射的逐像素着色都一样的，所以就不重复写了
Shader "..."
{
  ...
  SubShader
  {
    ...
    Pass
    {
      ...
      fixed4 frag (v2f i) : SV_Target
			{
				fixed3 ambient = UNITY_LIGHTMODEL_AMBIENT.xyz;
				float3 _worldNormal = normalize(i.worldNormal);
				float3 _worldLight = normalize(_WorldSpaceLightPos0.xyz);
        fixed _halfLambert = dot(_worldNormal, _worldLight) * 0.5 + 0.5;
				fixed3 diffuse = _LightColor0.xyz * _Diffuse.xyz * _halfLambert;
				fixed3 color = ambient + diffuse;
				return fixed4(color, 1);
			}
      ...
    }
  }
}
```

#### 3.9 UnityCG的帮助函数
> UnityCG.cginc中一些常用的帮助函数

|                    function                     |         description      |
|-------------------------------------------------|--------------------------|
| float3 WorldSpaceViewDir(float4 v)              | 输入一个模型空间中的顶点位置，返回世界空间中从该点到摄像机的观察方向。内部实现使用了UnityWorldSpaceViewDir函数 |
| float3 UnityWorldSpaceViewDir(float4 v)         | 输入一个世界空间中的顶点位置，返回世界空间中从该点到摄像机的观察方向 |
| float3 ObjSpaceViewDir(float4 v)                | 输入一个模型空间中的顶点位置，返回模型空间中从该点到摄像机的观察方向 |
| float3 WorldSpaceLightDir(float4 v)             | <kbd>仅可用于前向渲染中</kbd>。输入一个模型空间中的顶点位置，返回世界空间中从该点到光源的光照方向。内部实现使用了UnityWorldSpaceLightDir函数。没有被归一化 |
| float3 UnityWorldSpaceLightDir(float4 v)        | <kbd>仅可用于前向渲染中</kbd>。输入一个世界空间中的顶点位置，返回世界空间中从该点到光源的光照方向。没有被归一化 |
| float3 ObjSpaceLightDir(float4 v)               | <kbd>仅可用于前向渲染中</kbd>。输入一个模型空间中的顶点位置，返回模型空间中从该点到光源的光照方向。没有被归一化 |
| float3 UnityObjectToWorldNormal(float3 normal)  | 把法线从模型空间转换到世界空间中 |
| float3 UnityObjectToWorldDir(in float3 dir)     | 把方向矢量从模型空间变换到世界空间中 |
| float3 UnityWorldToObjectDir(float3 dir)        | 把方向矢量从世界空间变黄到模型空间中 |

比如上面的<kbd>fixed3 _worldNormal = normalize(mul(v.normal, (float3x3)unity_WorldToObject));</kbd>可以换成<kbd>fixed3 _worldNormal = normalize(UnityObjectToWorldNormal(v.normal));</kbd>

#### 3.10 更高级的着色
比如卡通着色、素描风格着色、PBR等。

## 4 渲染路径
4.1 VertexLit

4.2 Forward

4.3 Deferred Lighting

## 5 纹理
5.1 纹理采样

5.2 凹凸纹理

5.3 法线纹理

5.4 渐变纹理和遮罩纹理

## 6 透明效果

6.1 透明度测试
其实无法得到真正的半透明效果，得到的效果要么是透明的，要么是不透明的。

6.2 透明度混合
可以得到真正的半透明效果。

6.3 开启深度写入的透明度效果

## 7 光照

7.1 位置
位置对于平行光来说没有意义

7.2 方向
可以用_WorldSpaceLightPos0来得到这个平行光的方向

7.3 颜色
可以用_LightColor0来得到它的颜色和强度(这里是颜色和强度相乘后的结果了)

7.4 强度

7.5 衰减
平行光被认为是没有衰减的

## 5 屏幕后处理特效
高斯模糊、运动模糊等。

## 6 Unity Shader调试
#### 6.1 Unity自带的Frame Debugger

#### 6.2 Visual Studio联合Unity的调试

#### 6.3 RenderDoc

#### 6.4 假彩色图像
这是《Unity Shader入门精要》上介绍的方法

## 7 Unity Shader其他工具
#### 7.1 代码补全工具

## 8 资料  
- 书籍
  - 《Unity Shader入门精要》
  - 《Unity着色器和屏幕开发秘笈》
  - 《Unity3D ShaderLab》
- 官网  
  - https://renderdoc.org/
  - https://www.nvidia.com/en-us/drivers/cg-toolkit-15/
- 博客  
  - http://dingxiaowei.cn/2020/01/01/
  - https://gameinstitute.qq.com/community/detail/122725
  - https://zhuanlan.zhihu.com/p/74622572
- 屏幕后处理  
  - https://blog.csdn.net/zxc244603934/article/details/88384812
  - https://blog.csdn.net/cgy56191948/article/details/100665580
  - https://blog.csdn.net/cgy56191948/article/details/100773992

<!---
数学函数的图

<svg xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" width="480" height="223pt" viewBox="0 0 360 223">
    <defs>
        <symbol overflow="visible" id="a">
            <path d="M5.29-2.191H.55v-.82h4.74zm0 0"/>
        </symbol>
        <symbol overflow="visible" id="b">
            <path d="M3.234 0v-1.715H.125v-.805l3.27-4.636h.714v4.636h.97v.805h-.97V0zm0-2.52v-3.226L.992-2.52zm0 0"/>
        </symbol>
        <symbol overflow="visible" id="c">
            <path d="M5.035-.844V0H.305a1.54 1.54 0 01.101-.61c.121-.324.313-.64.578-.953.266-.312.649-.671 1.149-1.085.777-.637 1.304-1.141 1.578-1.516.273-.371.41-.723.41-1.055 0-.347-.125-.644-.375-.883-.246-.238-.574-.359-.973-.359-.421 0-.761.129-1.015.383-.254.254-.383.605-.387 1.055L.47-5.117c.062-.672.293-1.188.699-1.54.402-.355.945-.53 1.625-.53.687 0 1.23.19 1.629.57.402.383.601.855.601 1.418 0 .285-.058.566-.175.844-.118.277-.313.566-.582.875-.274.304-.723.726-1.356 1.257-.527.442-.867.743-1.015.903a2.868 2.868 0 00-.372.476zm0 0"/>
        </symbol>
        <symbol overflow="visible" id="d">
            <path d="M3.727 0h-.88v-5.602c-.21.204-.488.407-.831.606a5.902 5.902 0 01-.926.457v-.852c.492-.23.922-.511 1.289-.84.367-.328.629-.648.781-.957h.567zm0 0"/>
        </symbol>
        <symbol overflow="visible" id="e">
            <path d="M.906 0v-1H1.91v1zm0 0"/>
        </symbol>
        <symbol overflow="visible" id="f">
            <path d="M.414-3.531c0-.844.09-1.528.262-2.043.176-.516.433-.914.777-1.192.344-.28.774-.421 1.297-.421.383 0 .719.078 1.008.23.289.156.531.379.719.672.187.289.335.644.445 1.062.11.418.16.985.16 1.692 0 .84-.086 1.52-.258 2.035-.172.516-.43.914-.773 1.195-.344.281-.778.422-1.301.422-.691 0-1.234-.246-1.625-.742-.473-.594-.71-1.567-.71-2.91zm.906 0c0 1.176.137 1.957.41 2.347.278.387.614.582 1.02.582.402 0 .742-.195 1.016-.586.277-.39.414-1.171.414-2.343 0-1.18-.137-1.961-.414-2.348-.274-.387-.618-.582-1.028-.582-.402 0-.726.172-.965.516-.304.433-.453 1.238-.453 2.414zm0 0"/>
        </symbol>
        <symbol overflow="visible" id="g">
            <path d="M.414-1.875l.922-.078c.07.45.23.785.476 1.012.25.226.551.34.903.34.422 0 .781-.16 1.074-.477.293-.32.438-.742.438-1.27 0-.504-.141-.898-.422-1.187-.282-.29-.649-.434-1.106-.434a1.526 1.526 0 00-1.3.692L.57-3.383l.696-3.68h3.558v.844H1.97l-.387 1.922c.43-.3.879-.45 1.352-.45a2.14 2.14 0 011.582.65c.43.433.644.992.644 1.671 0 .649-.187 1.207-.566 1.68-.457.578-1.086.867-1.88.867-.651 0-1.183-.18-1.593-.547-.41-.363-.648-.847-.707-1.449zm0 0"/>
        </symbol>
    </defs>
    <path d="M7.66 35.668l5.371 8.715 5.367 9.754L23.77 64.78l5.37 11.371 5.372 11.914 5.367 12.274L50.62 125.2l5.371 12.198 5.367 11.786 5.371 11.195 5.372 10.426 5.367 9.492 5.37 8.414 5.372 7.203 5.371 5.883 5.367 4.465 5.371 2.98 5.371 1.453 5.368-.105 5.37-1.656 5.372-3.18 5.37-4.66 5.368-6.063 5.371-7.37 5.371-8.567 5.367-9.625 5.372-10.535 5.37-11.286 5.372-11.851 10.738-24.668 5.371-12.434 5.367-12.234 5.371-11.856 5.371-11.28 5.372-10.536 5.367-9.629 5.37-8.566 5.372-7.371 5.367-6.063 5.371-4.656 5.371-3.184 5.371-1.652 5.368-.105 5.37 1.449 5.372 2.984 5.367 4.465 5.371 5.879 5.371 7.203 5.371 8.418 5.367 9.492 5.372 10.426 5.37 11.191 10.739 23.985 5.371 12.418 5.371 12.441 5.367 12.277 5.371 11.914 5.371 11.368 5.372 10.644 5.367 9.758 5.37 8.715" fill="none" stroke-width="1.6" stroke-linecap="square" stroke="#5e81b5" stroke-miterlimit="3.25"/>
    <path d="M7.66 111.129v-4" fill="none" stroke-width=".2" stroke="#666" stroke-miterlimit="3.25"/>
    <use xlink:href="#a" x="1.66" y="123.128" fill="#666"/>
    <use xlink:href="#b" x="7.66" y="123.128" fill="#666"/>
    <path d="M29.14 111.129v-2.402M50.621 111.129v-2.402M72.102 111.129v-2.402M93.578 111.129v-4" fill="none" stroke-width=".2" stroke="#666" stroke-miterlimit="3.25"/>
    <use xlink:href="#a" x="87.58" y="123.128" fill="#666"/>
    <use xlink:href="#c" x="93.58" y="123.128" fill="#666"/>
    <path d="M115.059 111.129v-2.402M136.54 111.129v-2.402M158.02 111.129v-2.402M179.5 111.129v-4M200.98 111.129v-2.402M222.46 111.129v-2.402M243.941 111.129v-2.402M265.422 111.129v-4" fill="none" stroke-width=".2" stroke="#666" stroke-miterlimit="3.25"/>
    <use xlink:href="#c" x="262.42" y="123.128" fill="#666"/>
    <path d="M286.898 111.129v-2.402M308.379 111.129v-2.402M329.86 111.129v-2.402M351.34 111.129v-4" fill="none" stroke-width=".2" stroke="#666" stroke-miterlimit="3.25"/>
    <use xlink:href="#b" x="348.34" y="123.128" fill="#666"/>
    <path d="M.5 111.129h358M179.5 220.813h2.398M179.5 210.84h4" fill="none" stroke-width=".2" stroke="#666" stroke-miterlimit="3.25"/>
    <use xlink:href="#a" x="156.5" y="213.34" fill="#666"/>
    <g fill="#666">
        <use xlink:href="#d" x="162.5" y="213.34"/>
        <use xlink:href="#e" x="168.062" y="213.34"/>
        <use xlink:href="#f" x="170.84" y="213.34"/>
    </g>
    <path d="M179.5 200.867h2.398M179.5 190.898h2.398M179.5 180.926h2.398M179.5 170.957h2.398M179.5 160.984h4" fill="none" stroke-width=".2" stroke="#666" stroke-miterlimit="3.25"/>
    <use xlink:href="#a" x="156.5" y="163.484" fill="#666"/>
    <g fill="#666">
        <use xlink:href="#f" x="162.5" y="163.484"/>
        <use xlink:href="#e" x="168.062" y="163.484"/>
        <use xlink:href="#g" x="170.84" y="163.484"/>
    </g>
    <path d="M179.5 151.012h2.398M179.5 141.043h2.398M179.5 131.07h2.398M179.5 121.098h2.398M179.5 111.129h4M179.5 101.156h2.398M179.5 91.188h2.398M179.5 81.215h2.398M179.5 71.242h2.398M179.5 61.273h4" fill="none" stroke-width=".2" stroke="#666" stroke-miterlimit="3.25"/>
    <g fill="#666">
        <use xlink:href="#f" x="162.5" y="63.772"/>
        <use xlink:href="#e" x="168.062" y="63.772"/>
        <use xlink:href="#g" x="170.84" y="63.772"/>
    </g>
    <path d="M179.5 51.3h2.398M179.5 41.328h2.398M179.5 31.36h2.398M179.5 21.387h2.398M179.5 11.418h4" fill="none" stroke-width=".2" stroke="#666" stroke-miterlimit="3.25"/>
    <g fill="#666">
        <use xlink:href="#d" x="162.5" y="13.916"/>
        <use xlink:href="#e" x="168.062" y="13.916"/>
        <use xlink:href="#f" x="170.84" y="13.916"/>
    </g>
    <path d="M179.5 1.445h2.398M179.5 221.758V.5" fill="none" stroke-width=".2" stroke="#666" stroke-miterlimit="3.25"/>
</svg>
-->