## GLSL 300 es

### GLSL 300 es的声明

要使用GLSL 300 es，需要在着色器代码中显示的声明，声明版本代码如下：

```c
#version 300 es
```

需要注意的是：

- 版本声明的代码需要在顶点着色器和片元着色器中同时指定
- 版本声明的代码必须严格在第一行

上文所说的严格的第一行的意思是说，该声明前面不能有任何的行，哪怕是空行和注释也不行，下面通过代码说明：
比如以下代码是错误的,因为在#version 300 es之前会有一个空行

```javascript
var vsSource = `
#version 300 es
`;
```

正确的代码应该是：

```javascript
var vsSource = `#version 300 es
`;
```

在比如，以下代码是错误的：

```html
<script id="vs" type="notjs">
#version 300 es
...
</script>
```

正确的代码应该是：

```html
<script id="vs" type="notjs">#version 300 es
...
</script>
```



### GLSL 300 es 的不同

GLSL 300 es 和GLSL 100 es 有一些不同的地方，以下会一一讲述这些差异的地方



#### 使用in代替attribute

在GLSL 100中，定义顶点数据的变量，使用attribute关键词，如下：

```c
attribute vec4 aPosition;
attribute vec2 aTexcoord;
attribute vec3 aNormal;
```

而在GLSL 300 es中，使用in关键词，代码如下：

```c
in vec4 aPosition;
in vec2 aTexcoord;
in vec3 aNormal;
```



#### varying 被in/out替代

在GLSL 100，在顶点着色器和片元着色器中，通过varying关键词来声明varying变量，代码如下：

```c
varying vec2 vTexcoord;
varying vec3 vNormal;
```

而在GLSL 300 es中，顶点着色器中的varying变量用out声明，表示输出：

```c
out vec2 vTexcoord;
out vec3 vNormal;
```

在片元着色器中的varying变量用in声明，表示输如：

```CQL
in vec2 vTexcoord;
in vec3 vNormal;
```



#### GLSL 300 es 中没有内置变量gl_FragColor

在GLSL 100 中，我们通过给内置变量gl_FragColor赋值来设置片元的输出颜色，代码如下：

```c
gl_FragColor = vec4(1,1,1, 1); // white
```

而在GLSL 300 es中，需要自己定义一个输出颜色的变量，并在main函数中设置颜色值，代码如下：

```c
out vec4 myOutputColor;
 ......
void main() {
   myOutputColor = vec4(1, 1, 1, 1);  // white
}
```

> 此处，输入变量名可以随意定义，不过不能使用gl_开通，因此你也不能这样定义 out vec4 gl_FragColor;



#### 用texture代替 texture2D、textureCube

在GLSL 100中，通过texture2D方法获取2D贴图的像素，textureCube方法获取立方体贴图的像素，代码如下：

```c
uniform sampler2D uTexture;
uniform samplerCube uCubeTexture;
 ......
void main(){
  vec4 color1 = texture2D(uTexture, ...);
  vec4 color2 = textureCube(uCubeTexture, ...);
}
```

而在GLSL 300 es中，只需要使用texture方法即可，该方法会通过传入的贴图对象，自动判断，代码如下：

```c
uniform sampler2D uTexture;
uniform samplerCube uCubeTexture;
 ......
void main(){
  vec4 color1 = texture(uTexture, ...);
  vec4 color2 = texture(uCubeTexture, ...);
}
```

### 着色器中可以获取纹理大小

在WebGL1中，如果你的着色器需要知道纹理的大小，你需要手动用uniform传递纹理大小。在WebGL2中你可以调用

```glsl
//lod: 纹理级别
vec2 size = textureSize(sampler2D sampler, int lod)
```

### 直接选取纹素

在纹理中很方便存储大量数组数据。WebGL 1中你可以这么做，但是你得用纹理坐标(0.0 到 1.0)来寻址。在WebGL2中，你可以在纹理中直接用像素／纹素坐标来选取值，使得数据获取简单一些。

```glsl
vec4 values = texelFetch(sampler2D sampler, ivec2 position, int lod);
```

### 移除着色器循环限制

WebGL1中，着色器中的循环必须使用常量整数表达式。 WebGL2移除了这个限制(GLSL 300 es)
WebGL1

```glsl
for(float i = 1.0; i <= 80.0; i++)
```

WebGL2 可以使用变量

```glsl
uniform float num;
for(float i = 1.0; i <= num; i++)
```



## 顶点数组对象

顶点数组对象（ VAO ）是这样一种对象： 它封装了与顶点处理器有关的所有数据，它记录了顶点缓存区和索引缓冲区的引用，以及顶点的各种属性的布局而不是实际的数据。

#### 顶点数组对象的优点

一旦为一个 对象指定了一个VAO之后，可以通过对该VAO对象进行简单的绑定操作来导入对象的所有引用和状态。在之后绘制对象时候，不需要在手动来导入对象的引用和状态，VAO替你记住了它。
通过VAO可以简化缓冲区的绑定过程，即可以减少代码的调用次数，也提升了WebGL状态切换的效率。使用VAO对象的好处：

- 减少代码量，提高开发效率
- 提高绘制效率（因为减少了WebGL相关函数的调用，并且WebGL内部对VAO进行了优化）

#### WebGL1中如何使用VAO

在WebGL1.0中VAO是通过扩展方式提供的，首先需要获取对应的扩展对象：

```
var ext = gl.getExtension("OES_vertex_array_object");
```

如果返回的ext位null说明浏览器不支持该扩展。
有了上面的ext对象，便可以创建VAO了：

```
var vao = ext.createVertexArrayOES();
```

有了VAO对象之后，就可以进行绑定操作：

```
// bind
ext.bindVertexArrayOES(vao);
// unbind
ext.bindVertexArrayOES(null)
```

#### WebGL2中使用VAO

```javascript
// 创建
var triangleArray = gl.createVertexArray();
gl.bindVertexArray(triangleArray);

//绘制
gl.clear(gl.COLOR_BUFFER_BIT);// 清空颜色缓冲区
// 绘制第一个三角形
gl.bindVertexArray(triangleArray);
gl.drawArrays(gl.TRIANGLES, 0, 3);
// 绘制第二个三角形
gl.bindVertexArray(triangleArray2);
gl.drawArrays(gl.TRIANGLES, 0, 3);
```

#### 案例:用顶点数组对象绘制两个三角形

```javascript
// VAO 创建
var triangleArray = gl.createVertexArray();
gl.bindVertexArray(triangleArray);

var positions = new Float32Array([
  -0.5, -0.5, 0.0,
  0.0, -0.5, 0.0,
  0.0, 0.0, 0.0
]);
var positionBuffer = gl.createBuffer();
gl.bindBuffer(gl.ARRAY_BUFFER, positionBuffer);
gl.bufferData(gl.ARRAY_BUFFER, positions, gl.STATIC_DRAW);
gl.vertexAttribPointer(0, 3, gl.FLOAT, false, 0, 0);
gl.enableVertexAttribArray(0);

var colors = new Float32Array([
  1.0, 0.0, 0.0,
  0.0, 1.0, 0.0,
  0.0, 0.0, 1.0
]);
var colorBuffer = gl.createBuffer();
gl.bindBuffer(gl.ARRAY_BUFFER, colorBuffer);
gl.bufferData(gl.ARRAY_BUFFER, colors, gl.STATIC_DRAW);
gl.vertexAttribPointer(1, 3, gl.FLOAT, false, 0, 0);
gl.enableVertexAttribArray(1);
// VAO 创建
var triangleArray2 = gl.createVertexArray();
gl.bindVertexArray(triangleArray2);

var positions2 = new Float32Array([
  0.0, -0.0, 0.0,
  0.5, 0.0, 0.0,
  0.0, 0.5, 0.0
]);
var positionBuffer2 = gl.createBuffer();
gl.bindBuffer(gl.ARRAY_BUFFER, positionBuffer2);
gl.bufferData(gl.ARRAY_BUFFER, positions2, gl.STATIC_DRAW);
gl.vertexAttribPointer(0, 3, gl.FLOAT, false, 0, 0);
gl.enableVertexAttribArray(0);

var colors2 = new Float32Array([
  1.0, 1.0, 0.0,
  0.0, 1.0, 1.0,
  0.0, 1.0, 1.0
]);
var colorBuffer2 = gl.createBuffer();
gl.bindBuffer(gl.ARRAY_BUFFER, colorBuffer2);
gl.bufferData(gl.ARRAY_BUFFER, colors2, gl.STATIC_DRAW);
gl.vertexAttribPointer(1, 3, gl.FLOAT, false, 0, 0);
gl.enableVertexAttribArray(1);
////////////////
// DRAW
////////////////
gl.clear(gl.COLOR_BUFFER_BIT);
// VAO 切换
gl.bindVertexArray(triangleArray);
gl.drawArrays(gl.TRIANGLES, 0, 3);

gl.bindVertexArray(triangleArray2);
gl.drawArrays(gl.TRIANGLES, 0, 3);
```



## 图元重启

#### 启动图元重启功能

在OPENGL中，可以通过以下方法启动图元重启功能：

```
glEnable(GL_PRIMITIVE_RESTART_FIXED_INDEX);
```

而在WEBGL2中，图元重启功能默认是开启的，而且总是开启的，不能通过gl.enable和gl.disable方法来控制。
参考WebGL2 文档：[https://www.khronos.org/regis...](https://www.khronos.org/registry/webgl/specs/latest/2.0/#NO_PRIMITIVE_RESTART_FIXED_INDEX)

#### 图元重启标志

之前提到了图元重启是在遇到特定的标志才重启的，那么这个标志应该是多少了，一般而言gl.drawElements方法的索引值的类型可以是以下几种：

- gl.UNSIGNED_BYTE
- gl.UNSIGNED_SHORT
- gl.UNSIGNED_INT

那么分别对应的重启的标志就是

- 2^8 - 1
- 2^16 - 1
- 2^32 - 1

也就是說重启的标志的数值就是indices数组所能允许的最大值。这个值一般来说是不会被用到的，拿来当标志正好。

#### 代码片段

下面的代码，在定义的indices数组中加入了图元重启标志：

```javascript
/*https://www.khronos.org/registry/webgl/specs/latest/2.0/#5.18
 *WebGL 2.0 behaves as though PRIMITIVE_RESTART_FIXED_INDEX were always enabled. 
*/
var MAX_UNSIGNED_SHORT = 65535;
var num_vertices = 7;
var indices = new Uint16Array([
	0, 1, 2, MAX_UNSIGNED_SHORT, 2, 3, 1
]);
```



## 实例化数组

#### 实例化

如果能够讲数据一次性发送给GPU，然后告诉WebGL使用一个绘制函数，绘制多个物体，就会更方便。这种技术，便是实例化技术。这种技术的实现思路，就是把原本的uniform变量，比如变换矩阵，变成attribute变量，然后把多个对象的矩阵数据，写在一起，然后创建所有矩阵的VBO对象（顶点缓存区）； 创建好缓冲区后，把所有对象的矩阵数据通过bufferData 上传到缓冲区中，这和普通的attribute变量的缓冲区没什么差别。
接下来，就是和普通的VBO差异的部分：该缓冲区可以在多个对象之间共享。每个对象 取该缓冲区的一部分数据，作为attribute变量的值，方法如下:

```
gl.vertexAttribDivisor(index, divisor)
```

通过gl.vertexAttribDivisor方法指定缓冲区中的每一个值，用于多少个对象，比如divisor = 1，表示每一个值用于一个对象；如果divisor=2，表示一个值用于两个对象。 index表示的attribute变量的地址。

然后，通过调用如下方法进行绘制：

```
gl.drawArraysInstanced(mode, first, count, instanceCount);
gl.drawElementsInstanced(mode, count, type, offset, instanceCount);
```

这两个方法和 gl.drawArrays与gl.drawElements类似，不同的是多了第四个参数 instanceCount，表示一次绘制多少个对象。
通过这个方法，便能实现一次调用绘制多个对象的目标。

### 代码展示

本案例 将一次绘制多个四边形，代码如下：

```javascript
 var count = 3000;
 var positions = new Float32Array([
     -1/count, 1/count, 0.0,
     -1/count, -1/count, 0.0,
     1/count, 1/count, 0.0,
     1/count, -1/count, 0.0
 ]);
 var positionBuffer = gl.createBuffer();
 gl.bindBuffer(gl.ARRAY_BUFFER, positionBuffer);
 gl.bufferData(gl.ARRAY_BUFFER, positions, gl.STATIC_DRAW);
 gl.vertexAttribPointer(0, 3, gl.FLOAT, false, 0, 0);
 gl.enableVertexAttribArray(0);

 var colors = new Float32Array([
     1.0, 0.0, 0.0,
     0.0, 1.0, 0.0,
     0.0, 0.0, 1.0,
     1.0, 1.0, 1.0
 ]);
 var colorBuffer = gl.createBuffer();
 gl.bindBuffer(gl.ARRAY_BUFFER, colorBuffer);
 gl.bufferData(gl.ARRAY_BUFFER, colors, gl.STATIC_DRAW);
 gl.vertexAttribPointer(1, 3, gl.FLOAT, false, 0, 0);
 gl.enableVertexAttribArray(1);

var indices = new Uint8Array([
    0,1,2,
    2,1,3
]);
var indexBuffer = gl.createBuffer();
gl.bindBuffer(gl.ELEMENT_ARRAY_BUFFER,indexBuffer);
gl.bufferData(gl.ELEMENT_ARRAY_BUFFER,indices,gl.STATIC_DRAW); //给缓冲区填充数据

var offsetArray = [];
for(var i = 0;i < count;i ++){
    for(var j = 0; j < count; j ++){
        var x = ((i + 1) - count/2) / count * 4;
        var y = ((j + 1) - count/2) / count * 4;
        var z = 0;
        offsetArray.push(x,y,z);
		}
}

var offsets = new Float32Array(offsetArray)
var offsetBuffer = gl.createBuffer();
var aOffsetLocation = 2;
gl.bindBuffer(gl.ARRAY_BUFFER, offsetBuffer);
gl.bufferData(gl.ARRAY_BUFFER, offsets, gl.STATIC_DRAW);
gl.enableVertexAttribArray(aOffsetLocation);
gl.vertexAttribPointer(aOffsetLocation, 3, gl.FLOAT, false, 12, 0);
gl.vertexAttribDivisor(aOffsetLocation, 1);

// ////////////////
// DRAW
// ////////////////
gl.clear(gl.COLOR_BUFFER_BIT);// 清空颜色缓冲区
// // 绘制第一个三角形
gl.bindVertexArray(triangleArray);
gl.drawElementsInstanced(gl.TRIANGLES,indices.length,gl.UNSIGNED_BYTE,0,count * count);
```

## 不可变纹理

在很多情况下，我们的纹理的尺寸和数据格式都是不需要改变的。使用不可变纹理，可以避免驱动程序对这些不需要改变的尺寸和数据格式的纹理对象进行一致性和内存大小的检查，因此可以获得更佳的性能。 不可变纹理指的是纹理的一种分配方式，而不是值纹理的内容。
不可变纹理的思路是：在给纹理加载纹理数据之前，先指定纹理的格式和大小，显卡驱动程序可以提前进行一致性、内存的检查，一旦指定了纹理的格式和大小之后，纹理的格式和尺寸就不能更改，但是可以通过gl.texSubImage2D、texSubImage3D（注意不能用gl.txtImage2D）等方法来加载纹理的数据，同时还可以使用 render-to-texture, mipmap generation等方式加载纹理数据。

> texSubImage3D(以及后面提到的texStorage3D)方法与3D纹理有关。

#### 创建不可变纹理

创建不可变纹理很简单，首先通过gl.bindTexture方法绑定纹理对象，然后通过调用gl. texStorage2D、texStorage3D指定纹理的大小和格式，来创建不可变的存储空间。代码如下：

```javascript
// -- Allocate storage for the texture
gl.texStorage2D(gl.TEXTURE_2D, 1, gl.RGB8, 512, 512);
gl.texSubImage2D(gl.TEXTURE_2D, 0, 0, 0, gl.RGB, gl.UNSIGNED_BYTE, image);
gl.generateMipmap(gl.TEXTURE_2D);
```

#### void gl.texStorage2D(target, levels, internalformat, width, height);

设置纹理格式和尺寸

#### void gl.texSubImage2D(target, level, xoffset, yoffset, format, type, pixels);

加载纹理

gl.texSubImage2D 在 WebGL2.0 与 WebGL1.0 同名函数区别

```
// WebGL 1:
void gl.texSubImage2D(target, level, xoffset, yoffset, width, height, format, type, ArrayBufferView? pixels);
void gl.texSubImage2D(target, level, xoffset, yoffset, format, type, ImageData? pixels);
void gl.texSubImage2D(target, level, xoffset, yoffset, format, type, HTMLImageElement? pixels);
void gl.texSubImage2D(target, level, xoffset, yoffset, format, type, HTMLCanvasElement? pixels);
void gl.texSubImage2D(target, level, xoffset, yoffset, format, type, HTMLVideoElement? pixels);
void gl.texSubImage2D(target, level, xoffset, yoffset, format, type, ImageBitmap? pixels);

// WebGL 2:
void gl.texSubImage2D(target, level, xoffset, yoffset, format, type, GLintptr offset);
void gl.texSubImage2D(target, level, xoffset, yoffset, width, height, format, type, HTMLCanvasElement source);
void gl.texSubImage2D(target, level, xoffset, yoffset, width, height, format, type, HTMLImageElement source);
void gl.texSubImage2D(target, level, xoffset, yoffset, width, height, format, type, HTMLVideoElement source); 
void gl.texSubImage2D(target, level, xoffset, yoffset, width, height, format, type, ImageBitmap source); 
void gl.texSubImage2D(target, level, xoffset, yoffset, width, height, format, type, ImageData source);
void gl.texSubImage2D(target, level, xoffset, yoffset, width, height, format, type, ArrayBufferView srcData, srcOffset);
```



## 变换反馈

允许将顶点着色器的输出捕捉到缓冲区对象。输出缓冲区可以作为后续绘图调用中顶点数据来源。粒子动画或渲染到顶点的缓冲区的物理模型非常有用。

glTransformFeedbackVaryings(program, count,varying,bufferMode);1