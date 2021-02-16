## WebGL 着色器语言（GLSL ES）
### GLSE中的数据类型

必须注意的是，如果WebGL需要的参数是浮点类型，例如10.0。如果传递10会报错，因为10被认为是整数。



### 类型化数组

| 数组类型     | 每个元素所占字节数 | 描述                             |
| ------------ | ------------------ | -------------------------------- |
| Int8Array    | 1                  | 8位整形数(signed char)           |
| UInt8Array   | 1                  | 8位无符号整形数(unsigned char)   |
| Int16        | 2                  | 16位整形数（signed short）       |
| UInt16Array  | 2                  | 16位无符号整形数(unsigned short) |
| Int32Array   | 4                  | 32位整形数（signed int）         |
| UInt32Array  | 4                  | 32位无符号整形数(unsigned int)   |
| Float32Array | 4                  | 单精度32位浮点数（float）        |
| Float64Array | 8                  | 双精度64位浮点数（double）       |

#### 类型化数组的方法、属性和常量

```c
get(index)//获取第index个元素值
set(index, value)//设置第index个元素的值为value
set(array, offset)//从第offset个元素开始将数组array中的值填充进去

length//数组长度
BYTES_PER_ELEMENT//数组中每个元素所占字节数
```



### 类型转换内置函数

| 转换            | 函数        | 描述                                                        |
| --------------- | ----------- | ----------------------------------------------------------- |
| float转换为int  | int(float)  | 将浮点数的小数部分删去，转换为整形数（比如，将3.14转换为3） |
| bool转换为int   | int(bool)   | true被转换为1，false被转换为0                               |
| int转换为float  | float(int)  | 将整形数转换为浮点数（比如，将8转换为8.0）                  |
| bool转换为float | float(bool) | true被转换为1.0，false被转换为0.0                           |
| int转换为bool   | bool(int)   | 0被转换为false，其他非0倍转换为true                         |
| float转换为bool | bool(float) | 0.0被转换为false，其他非0值被转换为true                     |

### 矢量和矩阵类型

| 类型 | GLSL ES 数据类型          | 描述                                                 |
| ---- | ------------------------- | ---------------------------------------------------- |
| 矢量 | float、vec2、vec3、vec4   | 具有1、2、3、4个浮点数元素的矢量                     |
| 矢量 | int、ivec2、ivec3、ivec4  | 具有1、2、3、4个整形数元素的矢量                     |
| 矢量 | uint、uvec2、uvec2、uvec4 | 具有1、2、3、4个无符号整形数元素的矢量               |
| 矢量 | bool、bvec2、bvec3、bvec4 | 具有1、2、3、4个布尔值元素的矢量                     |
| 矩阵 | mat2、mat3、mat4          | 2\*2、3\*3、4\*4的浮点数元素的矩阵（4、9、16个元素） |

#### 分量名

| 类别       | 描述             |
| ---------- | ---------------- |
| x、y、z、w | 获取顶点坐标分量 |
| r、g、b、a | 获取颜色分量     |
| s、t、p、q | 获取纹理坐标分量 |

由于矢量可用来存储顶点的坐标、颜色、纹理坐标，所以GLSL ES支持以上三种分量名称以增强程序的可读性。事实上，任何矢量的x、r或s分量都会返回第一个分量，y、g、t分量都返回第二个分量，等等。比如：

```c
vec3 v3 = vec3(1.0, 2.0, 3.0) //将v3设置为（1.0, 2.0, 3.0）
float f;
f = v3.x; //设置f为1.0
f = v3.y  //设置f为2.0
f = v3.z  //设置f为3.0

f = v3.r; //设置f为1.0
f = v3.s; //设置f为1.0
```

将多个分量名共同置于点运算符后，就可以从矢量中同时抽取出多个分量。这个过程称为混合（swizzing）。
下面的例子中，我们使用了x、y、z和w，其他的集合也有相同的效果：

```c
vec2 v2;
v2 = v3.xy; //设v2为(1.0, 2.0)
v2 = v3.yz; //设v2位(2.0, 3.0) 可以省略任意分量
v2 = v3.xz; //设v2位(1.0, 3.0) 可以跳过任意分量
v2 = v3.yx; //设v2为(1.0, 1.0) 可以逆序 
v2 = v3.xx; //设v2为(1.0, 1.0) 可以重复任意分量  

vec3 v3a;
v3a = v3.zyx; //设v3a位(3.0, 2.0, 1.0)，可以使用所有分量

//多个分量名必须属于同一集合，比如不能使用v3.was
```



#### [ ] 运算符

矩阵中的元素任然是按照列主序读取的。与在javascript中一样，下表从0开始，所以通过[0]可以访问到矩阵中的第一列元素，[1]可以访问到第二列元素，[2]可以访问到第三列元素，连续两个[]运算符可以访问某列的某个元素：

```c
float m23 = m4[1][2]; //将 m23 设置为 m4 第二列中的第三个元素(7.0）
```

### 数组

GLSL ES只支持一维数组，而且数组对象不支持pop()和push()等操作，创建数组时也不需要使用new运算符。声明方式如下：

```c
float floatArray[4]; // 声明含有4个浮点数元素的数组
vec4 vec4Array[2]; // 声明含有两个vec4对象的数组
```
数组的长度必须是大于0的整形常量表达式，如下定义：
1. 整形字面量（0、1）
2. 用const限定字修饰的全局变量或局部变量，不包含函数参数，因此下面的代码将会出错：

```c
int size = 4;
vec4 vec4Array[size]; //错误。如果第一行为const int size = 4;则不会报错
```



### 存储限定字

在GLSL ES中，我们经常使用 attriute、varying和uniform限定字来修饰变量，此外，我们有时也会使用const限定字，它表示着色器中的某个变量使用恒定常量。它们都必须是全局变量。

本质上，着色器在任何顶点或片元中都应该以统一变量uinform的形式传入

| 类型      | 描述 |
| --------- | ---- |
| Attribute | attribute变量只能出现在顶点着色器中，被用来表示逐顶点的信息，它是只读的。通常存储位置，法线，纹理坐标，颜色。最大属性数量由gl_MaxVertexAttribs给出。 |
| uniform   | 统一变量，命名空间在顶点和片元着色器中是共享的。它是只读的，可以是除了数组或结构体之外的任意类型。如果顶点着色器和片元着色器中声明了同名的uniform变量，那么两个声明必须匹配。 |
| varying  | 顶点着色器输出变量，从顶点着色器向片元着色器传输数据用，传输到片元着色器后的输出变量值都是在光栅化阶段中经过插值处理的。必须在两种着色器中声明同名、同类型的varying变量。它的类型只能是以下类型：float、vec2、vec3、vec4、mat2、 mat 3、mat4。 |



### 精度限定字

GLSL ES新引入了精度限定字，目的是帮助着色器程序提高运行效率，消减内存开支。使用精度限定字，你就能够精细地控制程序在效果和性能间的平衡。然而，精度限定字是可选的，如果你不确定，可以使用下面这个适中的默认值：

```c
#ifdef GL_ES
  precision mediump float;
#endif
```
 如下所示，WebGL程序支持三种精度，其限定字分别为highp、mediump、lowp。
```c
mediump float size; //中精度的浮点型变量
highp vec4 position; //具有高精度浮点型元素的vec4对象
lowp vec4 color; //具有低精度浮点型元素的vec4对象

//为每个变量都声明精度很繁琐，可以使用关键字precision来声明着色器的默认精度，并且这行代码必须放在顶点着色器或片元着色器的顶部，格式如下：

precision mediump float;
```
我们已经发使用精度限定字一般都只在片元着色器中使用，这是因为，除了片元着色器的float类型没有默认精度，其他类型都有默认精度。



### 预处理指令

GLSL ES支持预处理指令。预处理指令用来在真正编译之前对代码进行预处理。都已#号开始。下面就是预定义float类型精度的预处理指令：

```c
#ifdef GL_ES
precision mediump float;
#endif
```

下面是我们在GLSL ES中可能用到的三种预处理指令。

```c
#if 条件表达式
  //If 如果条件比倒是为真，执行这里
#endif

#ifdef 谋宏
  //如果定义了某宏，执行这里
#endif

#ifndef 某宏
  //如果没有定义某宏，执行这里
#endif

//可以进行宏定义：
#define 宏名 宏内容

//可以使用undef 宏名，结束宏定义。
// 使用 #else 配合 #if

#define NUM 100
#if NUM == 100
  //如果宏NUM为100，执行这里
#else
  //否则，执行这里
#endif

//预定义的内置宏有，宏/描述
GL_ES//在OpenGL ES2.0中定义为1
GL_FRAGEMENT_PRECISION_HIGH//片元着色器支持highp精度
```

### GLSL默认值 

顶点着色器

| 名称         | 类型  | 描述                                              |
| ------------ | ----- | ------------------------------------------------- |
| gl_Color     | vec4  | 输入变量-表示顶点的主颜色                         |
| gl_Normal    | vec3  | 输入变量-表示顶点的法线值                         |
| gl_Vertex    | vec4  | 输入变量-表示顶点位置                             |
| gl_PointSize | float | 输入变量-点的大小                                 |
| gl_Position  | vec4  | 输出变量-变换后的顶点位置，顶点着色器必须写这个值 |

​    

 片段着色器

| 名称           | 类型  | 描述                                                         |
| -------------- | ----- | ------------------------------------------------------------ |
| gl_Color       | vec4  | 插值后的主颜色，只读                                         |
| gl_TexCoord    | vec4  | 插值后的纹理坐标，只读                                       |
| gl_FragColor   | vec4  | 输出的颜色                                                   |
| gl_FragCoord   | vec4  | 片元在窗口的坐标位置：x, y, z, 1/w，只读, x和y是片段的窗口空间坐标，原点为窗口的左下角, z为深度值 |
| gl_FragDepth   | float | 输出变量-片段深度值，可设置片段的深度值，如果着色器没有写入值到gl_FragDepth，它会自动取用`gl_FragCoord.z`的值 |
| gl_PointCoord  | vec4  | 点的纹理坐标- 只读                                           |
| gl_FrontFacing | bool  | 是否为正面                                                   |



## 顶点着色器

一个顶点着色器的工作是生成裁剪空间坐标值，通常是以下的形式

```c
void main() {
   gl_Position = doMathToMakeClipspaceCoordinates
}
```

每个顶点调用一次（顶点）着色器，每次调用都需要设置一个特殊的全局变量 `gl_Position`，该变量的值就是裁减空间坐标值。

顶点着色器需要的数据，可以通过以下三种方式获得。

1. Attributes 属性 (从缓冲中获取的数据)
2. Uniforms 全局变量 (在一次绘制中对所有顶点保持一致值)
3. Textures 纹理 (从像素或纹理元素中获取的数据)

## Attributes 属性

最常用的方法是缓冲和**属性**，在[工作原理](https://zhuanlan.zhihu.com/p/47454989)中讲到了缓冲和属性，你可以创建缓冲，

```js
var buf = gl.createBuffer();
```

将数据存入缓冲

```js
gl.bindBuffer(gl.ARRAY_BUFFER, buf);
gl.bufferData(gl.ARRAY_BUFFER, someData, gl.STATIC_DRAW);
```

初始化的时候，在（着色）程序中找到属性所在地址

```js
var positionLoc = gl.getAttribLocation(someShaderProgram, "a_position");
```

在渲染的时候告诉 WebGL 怎么从缓冲中获取数据传递给属性

```js
// 开启从缓冲中获取数据
gl.enableVertexAttribArray(positionLoc);

var numComponents = 3;  // (x, y, z)
var type = gl.FLOAT;    // 32位浮点数据
var normalize = false;  // 不标准化
var offset = 0;         // 从缓冲起始位置开始获取
var stride = 0;         // 到下一个数据跳多少位内存
                        // 0 = 使用当前的单位个数和单位长度 （ 3 * Float32Array.BYTES_PER_ELEMENT ）

gl.vertexAttribPointer(positionLoc, numComponents, type, false, stride, offset);
```

在 [WebGL 基础概念](https://zhuanlan.zhihu.com/p/45483053) 中示范了不做任何运算直接将数据传递给 `gl_Position`。

```c
attribute vec4 a_position;

void main() {
   gl_Position = a_position;
}
```

如果缓冲中存的是裁剪空间坐标就没什么问题。

属性可以用 `float`, `vec2`, `vec3`, `vec4`, `mat2`, `mat3` 和 `mat4` 数据类型。

## Uniforms 全局变量

全局变量在一次绘制过程中传递给着色器的值都一样，在下面的一个简单的例子中，用全局变量给顶点着色器添加了一个偏移量

```c
attribute vec4 a_position;
uniform vec4 u_offset;

void main() {
   gl_Position = a_position + u_offset;
}
```

现在可以把所有顶点偏移一个固定值，首先在初始化时找到全局变量的地址

```js
var offsetLoc = gl.getUniformLocation(someProgram, "u_offset");
```

然后在绘制前设置全局变量

```js
gl.uniform4fv(offsetLoc, [1, 0, 0, 0]);  // 向右偏移一半屏幕宽度
```

要注意的是全局变量属于单个着色程序，如果多个着色程序有同名全局变量，需要找到每个全局变量并设置自己的值。我们调用`gl.uniform???`的时候只是设置了**当前程序**的全局变量，当前程序是传递给 `gl.useProgram`的最后一个程序。

全局变量有很多类型，对应的类型有对应的设置方法。

### 普通数据类型设置

```js
gl.uniform1f (floatUniformLoc, v);                 // float
gl.uniform1fv(floatUniformLoc, [v]);               // float 或 float array
gl.uniform2f (vec2UniformLoc,  v0, v1);            // vec2
gl.uniform2fv(vec2UniformLoc,  [v0, v1]);          // vec2 或 vec2 array
gl.uniform3f (vec3UniformLoc,  v0, v1, v2);        // vec3
gl.uniform3fv(vec3UniformLoc,  [v0, v1, v2]);      // vec3 或 vec3 array
gl.uniform4f (vec4UniformLoc,  v0, v1, v2, v4);    // vec4
gl.uniform4fv(vec4UniformLoc,  [v0, v1, v2, v4]);  // vec4 或 vec4 array

gl.uniformMatrix2fv(mat2UniformLoc, false, [  4x element array ])  // mat2 或 mat2 array
gl.uniformMatrix3fv(mat3UniformLoc, false, [  9x element array ])  // mat3 或 mat3 array
gl.uniformMatrix4fv(mat4UniformLoc, false, [ 16x element array ])  // mat4 或 mat4 array

gl.uniform1i (intUniformLoc, v);                 // int
gl.uniform1iv(intUniformLoc, [v]);                 // int 或 int array
gl.uniform2i (ivec2UniformLoc, v0, v1);            // ivec2
gl.uniform2iv(ivec2UniformLoc, [v0, v1]);          // ivec2 或 ivec2 array
gl.uniform3i (ivec3UniformLoc, v0, v1, v2);        // ivec3
gl.uniform3iv(ivec3UniformLoc, [v0, v1, v2]);      // ivec3 or ivec3 array
gl.uniform4i (ivec4UniformLoc, v0, v1, v2, v4);    // ivec4
gl.uniform4iv(ivec4UniformLoc, [v0, v1, v2, v4]);  // ivec4 或 ivec4 array

gl.uniform1i (sampler2DUniformLoc,   v);           // sampler2D (textures)
gl.uniform1iv(sampler2DUniformLoc, [v]);           // sampler2D 或 sampler2D array

gl.uniform1i (samplerCubeUniformLoc,   v);         // samplerCube (textures)
gl.uniform1iv(samplerCubeUniformLoc, [v]);         // samplerCube 或 samplerCube array
```

还有一些类型 `bool`, `bvec2`, `bvec3`, 和 `bvec4`，它们可用 `gl.uniform?f?` 或 `gl.uniform?i?`。

### 数组类型设置

数组可以一次设置所有的全局变量

```js
// 着色器里
uniform vec2 u_someVec2[3];

// JavaScript 初始化时
var someVec2Loc = gl.getUniformLocation(someProgram, "u_someVec2");

// 渲染的时候
gl.uniform2fv(someVec2Loc, [1, 2, 3, 4, 5, 6]);  // 设置数组 u_someVec2
```

如果单独设置数组中的某个值，就要单独找到该值的地址

```js
// JavaScript 初始化时
var someVec2Element0Loc = gl.getUniformLocation(someProgram, "u_someVec2[0]");
var someVec2Element1Loc = gl.getUniformLocation(someProgram, "u_someVec2[1]");
var someVec2Element2Loc = gl.getUniformLocation(someProgram, "u_someVec2[2]");

// 渲染的时候
gl.uniform2fv(someVec2Element0Loc, [1, 2]);  // set element 0
gl.uniform2fv(someVec2Element1Loc, [3, 4]);  // set element 1
gl.uniform2fv(someVec2Element2Loc, [5, 6]);  // set element 2
```

### 结构体类型设置

创建结构体

```c
struct SomeStruct {
  bool active;
  vec2 someVec2;
};
uniform SomeStruct u_someThing;
```

找到每个元素的地址

```js
var someThingActiveLoc = gl.getUniformLocation(someProgram, "u_someThing.active");
var someThingSomeVec2Loc = gl.getUniformLocation(someProgram, "u_someThing.someVec2");
```

### 结构体数组类型设置

创建结构体数组

```c
struct Light {
    vec3 Position;
    vec4 Color;
};
uniform Light lights[4];
```

找到每个数组结构体属性

```js
var someThingActiveLoc = gl.getUniformLocation(someProgram, "lights[0].Position");
var someThingSomeVec2Loc = gl.getUniformLocation(someProgram, "lights[0].Color");
//light[1],light[2],...
```



## 片段着色器

一个片段着色器的工作是为当前光栅化的像素提供颜色值，通常是以下的形式

```c
precision mediump float;

void main() {
   gl_FragColor = doMathToMakeAColor;
}
```

每个像素都将调用一次片段着色器，每次调用需要从你设置的特殊全局变量 `gl_FragColor` 中获取颜色信息。

片段着色器所需的数据，可以通过以下三种方式获取

1. Uniforms 全局变量 (在一次绘制中对所有像素保持一致)
2. Textures 纹理 (从纹理，数据像素中获取的数据)
3. Varyings 可变量 (顶点着色器中提供并且经过差值后的数据)



## Textures 纹理

在着色器中获取纹理信息，可以先创建一个 `sampler2D` 类型全局变量，然后用GLSL方法 `texture2D` 从纹理中提取信息。

```c
precision mediump float;
uniform sampler2D u_texture;

void main() {
   vec2 texcoord = vec2(0.5, 0.5)  // 获取纹理中心的值
   gl_FragColor = texture2D(u_texture, texcoord);
}
```

从纹理中获取的数据取决于很多设置，至少要创建并给纹理填充数据，例如

```js
var tex = gl.createTexture();
gl.bindTexture(gl.TEXTURE_2D, tex);
var level = 0;
var width = 2;
var height = 1;
var data = new Uint8Array([
   255, 0, 0, 255,   // 一个红色的像素
   0, 255, 0, 255,   // 一个绿色的像素
]);
gl.texImage2D(gl.TEXTURE_2D, level, gl.RGBA, width, height, 0, gl.RGBA, gl.UNSIGNED_BYTE, data);
```

在初始化时找到全局变量的地址

```js
var someSamplerLoc = gl.getUniformLocation(someProgram, "u_texture");
```

在渲染的时候 WebGL 要求纹理必须绑定到一个纹理单元上

```js
var unit = 5;  // 挑选一个纹理单元
gl.activeTexture(gl.TEXTURE0 + unit);
gl.bindTexture(gl.TEXTURE_2D, tex);
```

然后告诉着色器你要使用的纹理在哪个纹理单元

```js
gl.uniform1i(someSamplerLoc, unit);
```

## Varyings 可变量

在[工作原理](https://zhuanlan.zhihu.com/p/47454989)提到过，可变量是一种顶点着色器给片段着色器传值的方式。

为了使用可变量，要在两个着色器中定义同名的可变量。给顶点着色器中可变量设置的值，会作为参考值进行差值，在绘制像素时传给片段着色器对应的可变量。

顶点着色器

```c
attribute vec4 a_position;
uniform vec4 u_offset;
varying vec4 v_positionWithOffset;

void main() {
  gl_Position = a_position + u_offset;
  v_positionWithOffset = a_position + u_offset;
}
```

片段着色器

```c
precision mediump float;
varying vec4 v_positionWithOffset;

void main() {
  // 从裁剪空间 (-1 <-> +1) 转换到颜色空间 (0 -> 1).
  vec4 color = v_positionWithOffset * 0.5 + 0.5
  gl_FragColor = color;
}
```

上方的示例没有太多意义，通常情况下直接将裁剪空间的值传给片段着色器当作颜色值是没有意义的，虽然它可以运行并且可以生成颜色值。

## GLSL

GLSL 全称是 Graphics Library Shader Language （图形库着色器语言），是着色器使用的语言。它有一些不同于 JavaScript的特性，主要目的是为栅格化图形提供常用的计算功能。所以它内建的数据类型例如`vec2`, `vec3` 和 `vec4` 分别代表两个值，三个值和四个值，类似的还有`mat2`, `mat3` 和 `mat4` 分别代表 2x2, 3x3 和 4x4 矩阵。

你可以做一些运算例如常量和矢量的乘法。

```c
vec4 a = vec4(1, 2, 3, 4);
vec4 b = a * 2.0;
// b 现在是 vec4(2, 4, 6, 8);
```

它同样可以做矩阵乘法以及矢量和矩阵的乘法

```c
mat4 a = ???
mat4 b = ???
mat4 c = a * b;

vec4 v = ???
vec4 y = c * v;
```

他还为矢量数据提供多种分量选择器，例如 vec4

```c
vec4 v;
```

- `v.x` 和 `v.s` 以及 `v.r` ， `v[0]` 表达的是同一个分量。
- `v.y` 和 `v.t` 以及 `v.g` ， `v[1]` 表达的是同一个分量。
- `v.z` 和 `v.p` 以及 `v.b` ， `v[2]` 表达的是同一个分量。
- `v.w` 和 `v.q` 以及 `v.a` ， `v[3]` 表达的是同一个分量。

它还支持矢量**调制**，意味者你可以交换或重复分量。

```c
v.yyyy //和下面是一样的
vec4(v.y, v.y, v.y, v.y)

v.bgra //和下面是等价的
vec4(v.b, v.g, v.r, v.a)
```

当构造一个矢量或矩阵时可以一次提供多个分量，例如

```c
vec4(v.rgb, 1)//和下面是一样的
vec4(v.r, v.g, v.b, 1)

vec4(1) //和下面相同
vec4(1, 1, 1, 1)
```



值得注意的是 GLSL 是一个强类型的语言。

```c
float f = 1;  // 错误，1是int类型，不能将int型赋值给float
```

正确的方式是

```c
float f = 1.0;      // 使用float
float f = float(1)  // 转换integer为float
```

上例中 `vec4(v.rgb, 1)` 不会因为 `1` 报错，因为 `vec4` 内部进行了转换类似 `float(1)` 。

GLSL 有一系列内置方法，其中大多数运算支持多种数据类型，并且一次可以运算多个分量，例如

```c
T sin(T angle)
```

T可以是 `float`, `vec2`, `vec3` 或 `vec4` 。如果你传的是 `vec4` 返回的也是 `vec4`, 返回结果对应每个分量的正弦值。换句话说如果 `v` 是 `vec4` 类型。那么

```c
vec4 s = sin(v);//和下面是一样的
vec4 s = vec4(sin(v.x), sin(v.y), sin(v.z), sin(v.w));
```



有时一个参数是浮点型而剩下的都是 `T` ，意思是那个浮点数据会作为所有其他参数的一个新分量。例如如果 `v1` 和 `v2` 是 `vec4` 同时 `f` 是浮点型，那么

```c
vec4 m = mix(v1, v2, f);
//和以下等价
vec4 m = vec4(
  mix(v1.x, v2.x, f),
  mix(v1.y, v2.y, f),
  mix(v1.z, v2.z, f),
  mix(v1.w, v2.w, f));
```

你可以在 [WebGL 引用表](https://link.zhihu.com/?target=https%3A//www.khronos.org/files/webgl/webgl-reference-card-1_0.pdf)最后一页看到所有GLSL方法的列表。如果你喜欢干货以更详细的东西你可以看看[ GLSL 规范](https://link.zhihu.com/?target=https%3A//www.khronos.org/files/opengles_shading_language.pdf)。