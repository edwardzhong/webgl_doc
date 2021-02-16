## GLSL内建函数

### 三角函数

下标用anyFloat来表示float、vec2、vec3和vec4中的一种：  

三角函数

| **函数**                             | **描述**             |
| ------------------------------------ | -------------------- |
| anyFloat radians(anyFloat degrees)   | 将角度值转化为弧度值 |
| anyFloat degrees(anyFloat radians)   | 将弧度值转化为角度值 |
| anyFloat sin(anyFloat angle)         | 三角正弦             |
| anyFloat cos(anyFLoat angle)         | 三角余弦             |
| anyFloat tan(anyFLoat angle)         | 三角正切             |
| anyFloat asin(anyFloat x)            | 反正弦               |
| anyFloat acos(anyFloat x)            | 反余弦               |
| anyFloat atan(anyFloat y,anyFloat x) | y/x的反正切          |
| anyFloat sinh(anyFloat x)            | 双曲正弦             |
| anyFloat cosh(anyFloat x)            | 双曲余弦             |
| anyFloat tanh(anyFloat x)            | 双曲正切             |
| anyFloat asinh(anyFloat x)           | 反双曲正弦           |
| anyFloat acosh(anyFloat x)           | 反双曲余弦           |
| anyFloat atanh(anyFloat x)           | 反双曲正切           |

### 指数函数

指数函数

| **函数**                            | **描述**             |
| ----------------------------------- | -------------------- |
| anyFloat pow(anyFLoat x,anyFloat y) | x的y次方             |
| anyFloat exp(anyFLoat x)            | x的自然指数          |
| anyFloat log(anyFloat x)            | x的自然对数          |
| anyFloat exp2(anyFloat x)           | 2的x次方             |
| anyFloat log2(anyFLoat angle)       | 以2为底的x的自然对数 |
| anyFloat sqrt(anyFloat x)           | x的平方根            |
| anyFloat inversesqrt(anyFloat x)    | x的逆平方根          |



### 几何函数

几何函数

| **函数**                              | **描述**                                                     |
| ------------------------------------- | ------------------------------------------------------------ |
| float length(vec2/vec3/vec4 x)        | 返回x向量的长度, $ \sqrt{x_0^2 +x_1^2+…}$                    |
| float distance(vec p0,vec p1)         | 返回p0和p1之间的距离, length(p0 - p1)                        |
| float dot(vec x,vec y)                | 返回x和y的点乘结果 a•b=\|a\|\|b\|cosø，向量的余弦值(夹角)    |
| vec3 cross(vec3 x,vec3 y)             | 返回x和y的叉乘结果 axb=\|a\|\|b\|sinø, 已知物体表面的两个非平行矢量（或者不在同一直线的三个点），就可依靠叉积求得法线 |
| vec normalize(vec x)                  | 返回和x方向相同的单位长度向量                                |
| vec faceforward(vec N,vec I,vec nRef) | 如果dot(Nref,l)<0则返回N，否则返回-N                         |
| vec reflect(vec l,vec N)              | 返回反射向量，参数：入射向量l，表面法向量N                   |
| vec refract(vec l,vec N,float eta)    | 返回折射向量，参数：入射向量l，表面法向量N和折射指数比eta    |

### 矩阵函数

许多矩阵操作都是使用常规数学运算符进行的，不过还有一些有用的矩阵函数： 

矩阵函数

| **函数**                           | **描述**                                               |
| ---------------------------------- | ------------------------------------------------------ |
| mat matrixCompMult(mat x,mat y)    | 逐个分量地将两个矩阵相乘。与线性代数的矩阵乘法不同     |
| mat2 outerProduct(vec2 c,vec2 r)   | 返回一个矩阵，这个矩阵是指定的两个向量的外积（叉乘积） |
| mat3 outerProduct(vec3 c,vec3 r)   |                                                        |
| mat4 outerProduct(vec4 c,vec4 r)   |                                                        |
| mat2X3 outerProduct(vec3 c,vec2 r) |                                                        |
| mat3X2 outerProduct(vec2 c,vec3 r) |                                                        |
| mat2X4 outerProduct(vec4 c,vec2 r) |                                                        |
| mat4X2 outerProduct(vec2 c,vec4 r) |                                                        |
| mat3X4 outerProduct(vec4 c,vec3 r) |                                                        |
| mat4X3 outerProduct(vec3 c,vec4 r) |                                                        |
| mat2 transpose(mat2 m)             | 返回一个矩阵，这个矩阵是指定就是转置矩阵               |
| mat3 transpose(mat3 m)             |                                                        |
| mat4 transpose(mat4 m)             |                                                        |
| mat2X3 transpose(mat3X2 m)         |                                                        |
| mat3X2 transpose(mat2X3 m)         |                                                        |
| mat2X4 transpose(mat4X2 m)         |                                                        |
| mat4X2 transpose(mat2X4 m)         |                                                        |
| mat3X4 transpose(mat4X3 m)         |                                                        |
| mat4X3 transpose(mat3X4 m)         |                                                        |
| float determinant(mat2 m)          | 返回一个矩阵，这个矩阵是指定矩阵的行列式               |
| float determinant(mat3 m)          |                                                        |
| float determinant(mat4 m)          |                                                        |
| mat2 inverse(mat2 m)               | 返回一个矩阵，这个矩阵是指定矩阵的逆矩阵               |
| mat3 inverse(mat3 m)               |                                                        |
| mat4 inverse(mat4 m)               |                                                        |

### 向量相关函数

向量之间的比较，要使用下标列出的这些函数： 

向量相关函数

| **函数**                              | **描述**                        |
| ------------------------------------- | ------------------------------- |
| bvec lessThan(vec x,vec y)            | 逐个分量地返回x                 |
| bvec lessThan(ivec x,ivec y)          |                                 |
| bvec lessThan(uvec x,uvec y)          |                                 |
| bvec lessThanEqual(vec x,vec y)       | 逐个分量地返回x<=y的结果        |
| bvec lessThanEquea(ivec x,ivec y)     |                                 |
| bvec lessThanEqual(uvec x,uvec y)     |                                 |
| bvec greaterThan(vec x,vec y)         | 逐个分量地返回x>y的结果         |
| bvec greaterThan(ivec x,ivec y)       |                                 |
| bvec greaterThan(uvec x,uvec y)       |                                 |
| bvec greaterThanEqual(vec x,vec y)    | 逐个分量地返回x>=y的结果        |
| bvec greaterThanEqual (ivec x,ivec y) |                                 |
| bvec greaterThanEqual (uvec x,uvec y) |                                 |
| bvec equal(vec x,vec y)               | 逐个分量地返回x==y的结果        |
| bvec equal(ivec x,ivec y)             |                                 |
| bvec equal(uvec x,uvec y)             |                                 |
| bvec notEqual(vec x,vec y)            | 逐个分量地返回x！=y的结果       |
| bvec notEqual (ivec x,ivec y)         |                                 |
| bvec notEqual (uvec x,uvec y)         |                                 |
| bool any(bvec x)                      | 如果x的任意分量为真，则返回真   |
| bool all(bvec x)                      | 如果x的所有分量都为真，则返回真 |
| bvec not(bvec x)                      | 返回x的逐个分量的补集           |

### 常用函数

所有这些函数都能用于标量和向量数据类型的运算，并且也返回标量和向量数据类型： 

矩阵函数 通用函数

| 函数                                                         | 描述                                                         |
| :----------------------------------------------------------- | ------------------------------------------------------------ |
| anyFloat abs(anyFLoat x)                                     | 返回x的绝对值                                                |
| anyInt abs(anyInt x)                                         |                                                              |
| anyFLoat sign(anyFloat x)                                    | x> 0.0 返回1.0，x == 0.0 返回 0.0，x<0 返回 -1.0             |
| anyInt sign(anyInt x)                                        |                                                              |
| anyFloat floor(anyFLoat x)                                   | 返回不大于x的最小整数                                        |
| anyFloat trunc(anyFloat x)                                   | 返回不大于x的最接近的整数                                    |
| anyFloat round(anyFloat x)                                   | 返回最接近x的整数的值。如果是小数部分为0.5则可能取任意一个方向的整数（根据具体实现而定） |
| anyFloat roundEven(anyFloat x)                               | 返回最接近x的整数的值。如果是小数部分为0.5则取最接近的偶数   |
| anyFloat ceil(anyFloat x)                                    | 返回大于x的最接近它的整数值                                  |
| anyFloat fract(anyFloat x)                                   | 返回x的小数部分                                              |
| anyFloat mod(anyFloat x,float y)                             | 返回x对y取余得到的模数                                       |
| anyFloat mod(anyFloat x,anyFloat y)                          |                                                              |
| anyFloat mod(anyFloat x,out anyFloat l)                      | 返回x的小数部分，并将l设为余下的整数部分的值                 |
| anyFloat min(anyFloat x,anyFloat y)                          | 返回x和y中较小的一个                                         |
| anyFloat min(anyFloat x,anyInt y)                            |                                                              |
| anyInt min(anyInt x,anyInt y)                                |                                                              |
| anyInt min(anyInt x,int y)                                   |                                                              |
| anyUInt min(anyUInt x,anyUInt y)                             |                                                              |
| anyUint min(anyUInt x,uint y)                                |                                                              |
| anyFloat max(anyFloat x,anyFloat y)                          | 返回x和y中较大的一个                                         |
| anyFloat max (anyFloat x,anyInt y)                           |                                                              |
| anyInt max (anyInt x,anyInt y)                               |                                                              |
| anyInt max (anyInt x,int y)                                  |                                                              |
| anyUInt max (anyUInt x,anyUInt y)                            |                                                              |
| anyUint max (anyUInt x,uint y)                               |                                                              |
| anyFLoat clamp(anyFLoat x, anyFLoat minVal, anyFLoat maxVal) | 获取三个参数中的中间值，即返回介于 minVal 和 maxVal 区间的值 |
| anyFLoat clamp(anyFLoat x, float minVal, float maxVal)       |                                                              |
| anyFLoat clamp(anyInt x,anyInt minVal, anyInt maxVal)        |                                                              |
| anyFLoat clamp(anyInt x,int minVal, int maxVal)              |                                                              |
| anyFLoat clamp(anyUint x, anyUint minVal, anyUint maxVal)    |                                                              |
| anyFLoat clamp(anyUint x, uint minVal, uint maxVal)          |                                                              |
| anyFLoat mix(anyFLoat x,anyFloat y,anyFloat a)               | 返回x和y的线性混合，a从0到1变化                              |
| anyFLoat mix(anyFLoat x,anyFloat y,float a)                  |                                                              |
| anyFloat mix(anyFloat x,anyFLoat y,anyBool a)                | a为假时返回x的各个分量，而在a为真时返回y的各个分量           |
| anyFLoat step(anyFloat edge,anyFloat x)                      | 对小于阈值的值，返回 `0.0`，大于阈值，则返回 `1.0` ( x < edge 则返回0.0，否则返回1.0,) |
| anyFloat step(float edge,anyFloat x)                         |                                                              |
| anyFloat smoothstep(anyFloat edge0,anyFLoat edge1,anyFloat x) | 给定一个范围的上下限和一个数值，这个函数会在已有的范围内给出插值 (x <= edge0 则返回0.0，x >= edge1则返回1.0，在两者之间则在0.0和1.0之间取一个平滑插值) |
| anyFloat smoothstep(anyFloat edge0,fLoat edge1,anyFloat x)   |                                                              |
| anyBool isnan(anyFLoat x)                                    | Returns true if exist Nan                                    |
| anyBool isinf(anyFloat x)                                    | 如果x为正无穷大或负无穷大，则返回true                        |
| anyInt floatBitsToInt(anyFLoat x)                            | 将一个浮点值转换成整数值                                     |
| anyUint floatBitsToUint(anyFloat x)                          |                                                              |
| anyFLoat intBitsToFloat(anyInt x)                            | 将一个整数值转换成浮点值                                     |
| anyFLoat uintBitsToFloat(anyUint x)                          |                                                              |
| dFdx(p)                                                      | p在x方向上的偏导数                                           |
| dFdy(p)                                                      | p在y方向上的偏导数                                           |
| fwidth(p)                                                    | p在x和y方向上的偏导数的绝对值之和                            |

#### dFdx, dFdy 和 fwidth

vec3 point = gl_TexCoord[0].xyz;

dFdx的参数如果是uniform变量, dFdx dFdy返回值始终为0. 值不会变的变量就会返回0值 
dFdx(point.x) 就是当屏幕坐标x改变1时, 当前point各个分量 x y z会变化多少，dFdx(point.xyz) 同理

**这两个函数的意义** 
假设我们画一个矩形R,总共有4个顶点,那么顶点着色器会进行4次, gl_TexCoord[0]在顶点着色器会有4个值 
假设矩形R映射到屏幕上总共有16个像素(一行4个),  片元着色器被调用16次 
gl_TexCoord[0] 会有16个取值,在片元着色器中被插值 
dFdx(gl_TexCoord[0].xyz) 表示每个像素点之间x y z 分量的差异,类似于求导

width的效果相当于以下

```c
w = fwidth(uv); 	
// 同上
w.x = abs(dFdx(uv).x);
w.y = abs(dFdy(uv).y);
```

##### 面的法线向量计算(flat shader)

偏导数函数可以用来在片元着色器中计算当前面（三角形）的法线向量。当前片元的世界坐标系的水平偏导数和垂直偏导数是两个三角形表面上的两个向量，它们的叉乘结果是一个垂直于表面的向量，该向量的归一化结果就是面的法线向量。需要特别注意的是两个向量的叉乘的顺序。下面是GLSL中通过镜头坐标系中坐标计算面法线向量的代码：

```
normalize(  cross(dFdx(pos),  dFdy(pos))  );
```