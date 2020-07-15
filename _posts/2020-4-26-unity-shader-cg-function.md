---
layout:       post
title:        "[Unity Shader] 四 常用函数"
subtitle:     "unity shader"
created:      '2020-05-08T10:04:43.748Z'
modified:     '2020-05-23T02:42:48.959Z'
author:       "CharlesZhong"
header-style: text
catalog:      true
...published:    false
tags:
    - Unity Shader
    - Shader
---

## 1 Cg函数库

#### 1.1 基本函数功能表

|       函数      |    功能    |
|-----------------|-----------|
| abs(x)          | 返回输入参数的绝对值 |
| acos(x)         | 反余切函数，输入参数范围为[-1,1] |
| all(x)          | 如果输入参数不为0，则返回true；否则返回false。&&运算 |
| any(x)          | 输入参数只要有其中一个不为0，则返回true。 |
| asin(x)         | 反正弦函数，输入参数取值区间为[-1,1]，返回角度值范围为[] |
| atan(x)         | 反正切函数，返回角度值范围为[] |
| atan2(y,x)      | 计算y/x的反正切值。实际上和atan(x)函数功能完全一样，至少输入参数不同。atan(x) = atan2(x, float(1)) |
| ceil(x)         | 对输入参数向上取整。例如ceil(float(1.3))，返回值为2.0 |
| clamp(x,a,b)    | 如果x值小于a，则返回a；如果x值大于b，返回b；否则返回x |
| cos(x)          | 返回弧度x的余弦值。返回值范围为[-1,1] |
| cosh(x)         | 双曲余弦(hyperbolic cosine)函数，计算x的双曲余弦值 |
| cross(A,B)      | 返回两个三元向量的叉积(cross product)。注意，输入参数必须是三元向量 |
| degrees(x)      | 输入参数为弧度值(radians)，函数将其转换为角度值(degrees) |
| determinant(m)  | 计算矩阵的行列式因子 |
| dot(A,B)        | 返回A和B的点积(dot product)。参数A和B可以是标量，也可以是向量(输入参数方面，点积和叉积函数有很大不同) |
| exp(x)          | 计算ex的值，e = 2.71828182845904523536 |
| exp2(x)         | 计算2x的值 |
| floor(x)        | 对输入参数向下取整。例如floor(float(1.3))返回的值为1.0；但是floor(float(-1.3))返回的值为-2.0。该函数与ceil(x)函数相对应 |
| fmod(x,y)       | 返回x/y的余数。如果y为0，结果不可预料 |
| frac(x)         | 返回标量或矢量的小数 |
| frexp(x, outexp)| 将浮点数x分解为尾数和指数，即x = m * 2 ^ exp，返回m，并将指数存入exp中；如果x为0，则尾数和指数都返回0 |
| isfinite(x)     | 判断标量或者向量中的每个数据是否是有限数，如果是则返回true；否则返回false；无限的或者非数据(not-a-number NaN) |
| isinf(x)        | 判断标量或者向量中的每个数据是否是无限，如果是返回true;否则返回false |
| isnan(x)        | 判断标量或者向量中的每个数据是否是非数据(not-a-number NaN)，如果是返回true；否则返回false；|
| ldexp(x, n)     | 计算x * 2n的值 |
| lerp(a, b, f)   | 计算(1-f)* + * a b f或者a b f a + * - ()的值。即在下限a和上限b之间进行插值，f表示权值。注意，如果a和b是向量，则权值f必须是标量或者等长的向量 |
| lit(N * dot * L, N * dot * H, m) | N表示法向量；L表示入射光向量；H表示半角向量；m表示高光系数。函数计算环境光、散射光、镜面光的共享，返回的4元向量；X位代表散射光的贡献，总是1.0；Y位代表散射光的贡献，如果N * L < 0或者N * H < 0，则为0；否则为（N * L）^ m；W位始终为1.0 |
| log(x)          | 计算ln(x)的值，x必须大于0 |
| log2(x)         | 计算log2(x)的值，x必须大于0 |
| log10(x)        | 计算log10(x)的值，x必须大于0 |
| max(a,b)        | 比较两个标量或等长向量元素，返回最大值 |
| min(a,b)        | 比较两个标量或等长向量元素，返回最小值 |
| modf(x, out ip) | 将浮点数num分解成整数部分和小数部分，返回小数部分，将整数部分存入ip。不常用 |
| mul(M,N)        | 计算两个矩阵相乘，如果M为AxB阶矩阵，N为BxC阶矩阵，则返回AxC阶矩阵。下面两个函数为其重载函数。 |
| pow(x,y)        | &x^{y}& |
| radians(x)      | 函数将角度值转换为弧度制 |
| round(x)        | Round-to-nearest，或closest integer to x即四舍五入 |
| rsqrt(x)        | X的反平方根，x必须大于0 |
| saturate(x)     | 如果x小于0，返回0；如果x大于1，返回1；否则，返回x;x可以是float，float3，float4，如果是一个矢量的话，则对这个矢量上的每个分量做同样的取值操作 |
| sign(x)         | 如果x大于0，返回1；如果x小于0，返回-1；否则返回0 |
| sin(x)          | 输入参数为弧度，计算正弦值，返回值范围为[-1,1] |
| sincos(float x, out s, out c) | 该函数是同时计算x的sin值和cos值，其中s = sin(x)，c = cos(x)。该函数用于“同时需要计算sin值和cos值的情况”，比分别运算要快很多！ |
| sinh(x)         | 计算双曲正弦(hyperbolic sine)值 |
| smoothstep(min, max, x) | 值x位于min、max区间中。如果x = min，返回0；如果x = max，返回1；如果x在两者之间，按照下列公式返回数据： |
| step(a, x)      | 如果x < a，返回0；否则，返回1 |
| sqrt(x)         | 求x的平方根，x必须大于0 |
| tan(x)          | 输入参数为弧度，计算正切值 |
| tanh(x)         | 计算双曲正切值 |
| transpose(M)    | M为矩阵，计算其转置矩阵 |

#### 1.2 额外函数表

|     函数    |   功能    |
|------------|-----------|
| length()   |
| float()    |
| clamp()    |

#### 1.3 几何函数
几何函数，用于执行和解析几何相关的计算，例如根据入射光向量和顶点法向量，求取反射光和折射光方向向量。  
Cg语言标准库中有3个几何函数会经常被使用到，分别是：  
- <kbd>normalize</kbd>函数，对向量进行归一化；
- <kbd>reflect</kbd>函数，计算反射光方向向量;
- <kbd>refract</kbd>函数，计算折射光方向向量。

|         函数         |    功能     |
|----------------------|-------------|
| distance(pt1, pt2)   | 两点之间的欧几里得距离(Euclidean distance) |
| faceforward(N, I, Ng)| 如果Ng * I < 0,返回N；否则返回-N |
| length(v)            | 返回一个向量的模，即sqrt(dot(v,v)) |
| normalize(v)         | 归一化向量 |
| reflect(I, N)        | 根据入射光方向向量I，和顶点法向量N，计算反射光方向向量。其中I和N必须被<strong>归一化</strong>，需要非常注意的是，这个I是<strong>指向顶点</strong>的；函数只对三元向量有效，i是入射方向，n是法线方向，最后返回反射方向，参数可以是float、float2、float3 |
| refract(I, N, eta)   | 计算折射向量，I为入射光线，N为法向量，eta为折射系数；其中I和N必须被归一化，如果I和N之间的夹角太大，则返回(0,0,0)，特就是没有折射光线；I是<strong>指向顶点</strong>的；函数只对三元向量有效 |

#### 1.4 纹理映射函数

#### 1.5 偏导函数

#### 1.6 调试函数

## 2 Unity额外的函数
text2D()

## 3 资料
- [https://developer.download.nvidia.cn/CgTutorial/cg_tutorial_appendix_e.html](https://developer.download.nvidia.cn/CgTutorial/cg_tutorial_appendix_e.html)
- [https://www.jianshu.com/p/c789aff2d6e9](https://www.jianshu.com/p/c789aff2d6e9)