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

## 顶点数组对象

为一个 对象指定了一个VAO之后，可以通过对该VAO对象进行简单的绑定操作来导入对象的所有引用和状态。在之后绘制对象时候，不需要在手动来导入对象的引用和状态，VAO替你记住了它。
通过VAO可以简化缓冲区的绑定过程，即可以减少代码的调用次数，也提升了WebGL状态切换的效率。

- 减少代码量，提高开发效率

- 提高绘制效率（因为减少了WebGL相关函数的调用，并且WebGL内部对VAO进行了优化）

顶点数组对象(Vertex Array Object)，在WebGL1中，是一个扩展对象OES_vertex_array_object，而在WebGL2直接使用。在WebGL1中，使用代码如下：

```javascript
var ext = gl.getExtension("OES_vertex_array_object");
if (!ext) {
   // 告诉用户没有此扩展或者使用其他方式
  // tell user they don't have the required extension or work around it
} else {
  var aVAO = ext.createVertexArrayOES();
}
```

而在WebGL2中，代码如下：

```javascript
var aVAO = gl.createVertexArray();
```

实例
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

在OPENGL中，可以通过以下方法启动图元重启功能：

```javascript
glEnable(GL_PRIMITIVE_RESTART_FIXED_INDEX);
```

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

## 不可变纹理

使用不可变纹理，可以减少上文中提到的因检查而导致的性能开销。 不可变纹理指的是纹理的一种分配方式，而不是值纹理的内容。
不可变纹理的思路是：在给纹理加载纹理数据之前，先指定纹理的格式和大小，显卡驱动程序可以提前进行一致性、内存的检查，一旦指定了纹理的格式和大小之后，纹理的格式和尺寸就不能更改，但是可以通过gl.texSubImage2D、texSubImage3D（注意不能用gl.txtImage2D）等方法来加载纹理的数据，同时还可以使用such as render-to-texture, mipmap generation等方式加载纹理数据。

> texSubImage3D(以及后面提到的texStorage3D)方法与3D纹理有关，后续相关文章会介绍。

#### 创建不可变纹理

创建不可变纹理很简单，首先通过gl.bindTexture方法绑定纹理对象，然后通过调用gl. texStorage2D、texStorage3D指定纹理的大小和格式，来创建不可变的存储空间。代码如下：

```javascript
// -- Allocate storage for the texture
gl.texStorage2D(gl.TEXTURE_2D, 1, gl.RGB8, 512, 512);
gl.texSubImage2D(gl.TEXTURE_2D, 0, 0, 0, gl.RGB, gl.UNSIGNED_BYTE, image);
gl.generateMipmap(gl.TEXTURE_2D);
```

#### 不可变纹理可以优化性能

在很多情况下，我们的纹理的尺寸和数据格式都是不需要改变的。使用不可变纹理，可以避免驱动程序对这些不需要改变的尺寸和数据格式的纹理对象进行一致性和内存大小的检查，因此可以获得更佳的性能。



## 变换反馈

允许将顶点着色器的输出捕捉到缓冲区对象。输出缓冲区可以作为后续绘图调用中顶点数据来源。粒子动画或渲染到顶点的缓冲区的物理模型非常有用。

glTransformFeedbackVaryings(program, count,varying,bufferMode);