9.14 学习完毕

# 环境配置

1. 下载glfw的二进制格式库

https://www.glfw.org/download.html     【本次下载32bit，注意与vs中的x86模式配套】<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230901120222899.png" alt="image-20230901120222899" style="zoom:50%;" />

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230901120145689.png" alt="image-20230901120145689" style="zoom:50%;" />

2. 下载完毕，在项目文件夹中，创建新文件Dependencies->GLFW，将下载的文件中的include、lib-vs2022放入，；lib中只保留glfw3.lib【静态库】，其余文件删除

3. vs中链接静态库：

   * 项目-> 属性->c/c++->常规->附加包含目录->添加include路径

   ```
   $(SolutionDir)Dependencies\GLFW\include
   ```

   * 项目-> 属性->链接器->常规->附加库目录->添加lib路径

   ```
   $(SolutionDir)Dependencies\GLFW\lib-vc2022
   ```

   * 项目-> 属性->链接器->输入->附加依赖项>添加具体的库文件

   ```
   $(CoreLibraryDependencies);%(AdditionalDependencies);===【glfw3.lib;opengl32.lib】===新加
   ```

   注意：添加opengl32.lib：解决

   ```
   1>Application.obj : error LNK2019: 无法解析的外部符号 __imp__glClear@4，函数 _main 中引用了该符号
   1>D:\VisualStudio\Project\OpenGL\Debug\OpenGL.exe : fatal error LNK1120: 1 个无法解析的外部命令
   ```

4. 【处理库文件缺失问题】

​		复制输出的错误信息中缺少的库，复制到google，找到微软的msdn文件，找到表格中的链接库，复制添加到`项目-> 属性->链接器->输入->附加依赖项>添加具	体的库文件`



* 配置glew

1. 地址 https://glew.sourceforge.net/

2. 按照上述方式在vs内连接

   ```
   $(SolutionDir)Dependencies\GLEW\include
   $(SolutionDir)Dependencies\GLFW\lib\release\win32
   【glew32s.lib】
   
   // 除此之外：
   在设置->c/c++-> 预处理器->  添加：GLEW_STATIC
   ```

3. 使用：

   ```
   1 创建上下文
   2 创建上下文之后调用glewInit（）初始化函数
   glfwMakeContextCurrent(window);
   
   glewInit();
   
   if (glewInit() != GLEW_OK)
   	std::cout << "Error" << std::endl;
   ```

   [工作经历]
   [公司名称] - [工作地点] - [工作日期]
   职位：[您的职位]
   
   - 深入研究 Facebook Folly 库中的 FBVector 源代码，包括阅读和理解核心数据结构、算法和性能优化策略。
   - 分析 FBVector 的内部实现，包括如何管理内存、处理元素构造和销毁、处理异常等方面的细节。
   - 通过对源代码的逐行分析，解决了与 FBVector 相关的性能瓶颈问题，提出了内存管理的改进建议，并优化了部分核心算法。
   - 协同团队成员，分享对 FBVector 源代码的深刻理解，为团队提供专业建议，改进了整体代码库的性能和稳定性。
   - 编写技术文档，总结和分享 FBVector 的源代码分析成果，以便团队成员更好地理解和使用这一关键组件。

# 一 基础

函数帮助文档 https://docs.gl/

## 1 顶点缓冲对象（VBO）

--用于存放gpu所使用的数据

* 以一个顶点为单位，eg: {{顶点1位置，颜色，纹理}，{顶点2位置，颜色，纹理}，{顶点3位置，颜色，纹理}}

```cpp
// 顶点数据
float data[6] = {
    -0.5f, -0.5f,
    0.0f,  0.5f,
    0.5f, -0.5f
};
// 定义缓冲区（缓冲区有唯一标识id）
unsigned int buffer;
glGenBuffers(1, &buffer);

// 绑定缓冲区【设置缓冲区类型】
glBindBuffer(GL_ARRAY_BUFFER, buffer);

// 加入数据【数据整体类型、大小、数据、缓存区的访问形式（几次修改几次访问）】
glBufferData(GL_ARRAY_BUFFER, 6 * sizeof(float),data, GL_STATIC_DRAW);

// 设置缓存区布局【布局起始索引、n维坐标、数据类型、是否格式化、到下一个坐标的偏移、本组内的属性偏移】
glVertexAttribPointer(0, 2, GL_FLOAT, GL_FALSE, 2 * sizeof(float), 0);

// 启用布局【生效起始索引】
glEnableVertexAttribArray(0);

// 画图
while (!glfwWindowShouldClose(window))
    {
        glClear(GL_COLOR_BUFFER_BIT);

    	// glDrawArrays根据顶点缓冲区内数据画图
    	// 何一个绘制指令的调用都将把图元传递给OpenGL。这是其中的几个：GL_POINTS、GL_TRIANGLES、GL_LINE_STRIP。
        glDrawArrays(GL_TRIANGLES, 0, 3);

        glfwSwapBuffers(window);

        glfwPollEvents();
    }
```

## 2 顶点属性指针（layout）

* 属性配置是针对单个顶点来说
* 配置好一个顶点，其余顶点均按照此属性

```cpp
glVertexAttribPointer(	
GLuint index,		     // 指定一组顶点属性的第几个属性
GLint count,             // 一个属性内几个数值
GLenum type,             // 数值类型
GLboolean normalized,    // 是否需要规范化
GLsizei stride,		     // 步长 = count * sizeof(type)
    
// 本属性的起始位置（偏移量）即，前面所有属性的size和， offset += count * sizeof(float)
const GLvoid * pointer   stride;
);

```

![image-20230909115301738](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230909115301738.png)

```cpp
// 属性1 ： 位置坐标
glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, 8 * sizeof(float), (void *)0 );

// 属性2 ： 颜色
glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, 8 * sizeof(float), (void *) 3 * sizeof(float));

// 属性3 ： 纹理
glVertexAttribPointer(2, 2, GL_FLOAT, GL_FALSE, 8 * sizeof(float), (void *) 6 * sizeof(float));
     
```



## 3 索引缓冲对象（EBO）

* 注意索引缓冲区必须为 ==unsigned int==

* 避免在顶点缓冲区内拷贝多个同一数据的顶点

```cpp
float data[] = {
    -0.5f, -0.5f, //0
    0.5f,  0.5f, //1
    0.5f, -0.5f, //2
    -0.5f, 0.5f  //3
};

// 索引缓冲区
unsigned int indies[]{
    0, 2, 1,
    1, 3, 0
};
unsigned int indexBuffer;
glGenBuffers(1, &indexBuffer);
glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, indexBuffer);
glBufferData(GL_ELEMENT_ARRAY_BUFFER, 6 * sizeof(unsigned int), indies, GL_STATIC_DRAW);

// 画图
while (!glfwWindowShouldClose(window))
    {
        glClear(GL_COLOR_BUFFER_BIT);

    	// glDrawElements根据索引缓冲区内数据索引到顶点缓冲区，根据数据画图
        glDrawElements(GL_TRIANGLES,6,GL_UNSIGNED_INT, nullptr);

        glfwSwapBuffers(window);

        glfwPollEvents();
    }
```



## 4 顶点数组对象（VAO）

![image-20230909160408326](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230909160408326.png)

* 绑定VAO后，整个环境在该VAO下进行，想使用其他VAO时，切换绑定新的VAO
* 

```cpp
#include <GL/glew.h>
#include <GLFW/glfw3.h>
#include <iostream>

// 定义顶点结构体
struct Vertex {
    float position[3];
    float color[3];
};

int main() {
    // 初始化 GLFW 和 GLEW

    // 创建窗口

    // 顶点数据1 {位置、颜色}
    Vertex vertices1[] = {
        {{-0.5f, -0.5f, 0.0f}, {1.0f, 0.0f, 0.0f}},
        {{0.5f, -0.5f, 0.0f}, {0.0f, 1.0f, 0.0f}},
        {{0.5f, 0.5f, 0.0f}, {0.0f, 0.0f, 1.0f}},
        // ...
    };
    // 数据2 {法线}
    float vertices2[] = {
        0.5f, 0.5f, 0.0f,
    };
	// 数据3 {位置、颜色}
    Vertex vertices3[] = {
        {{-0.7f, -0.7f, 0.0f}, {0.5f, 0.5f, 0.0f}},
        {{0.7f, -0.7f, 0.0f}, {0.0f, 0.5f, 0.5f}},
        {{0.7f, 0.7f, 0.0f}, {0.5f, 0.0f, 0.5f}},
        // ...
    };

    // 创建两个VAO
    GLuint vao[2];
    glGenVertexArrays(2, vao);

    // 绑定第一个VAO，并使用
    glBindVertexArray(vao[0]);

    // 创建并绑定第一个VBO
    GLuint vbo1;
    glGenBuffers(1, &vbo1);
    glBindBuffer(GL_ARRAY_BUFFER, vbo1);
    glBufferData(GL_ARRAY_BUFFER, sizeof(vertices1), vertices1, GL_STATIC_DRAW);

    // 配置第一个VBO的顶点属性指针1
    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, sizeof(Vertex), (void*)offsetof(Vertex, position));
    glEnableVertexAttribArray(0);
    // 配置第一个VBO的顶点属性指针2
    glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, sizeof(Vertex), (void*)offsetof(Vertex, color));
    glEnableVertexAttribArray(1);
    
    // 创建并绑定第二个VBO
    GLuint vbo2;
    glGenBuffers(1, &vbo2);
    glBindBuffer(GL_ARRAY_BUFFER, vbo2);
    glBufferData(GL_ARRAY_BUFFER, 3 * sizeof(float), vertices2, GL_STATIC_DRAW);

    // 配置第二个VBO的顶点属性指针
    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, sizeof(float), (void*)0);
    glEnableVertexAttribArray(0);
    

    // 绑定第二个VAO，切换到vao2下运行
    glBindVertexArray(vao[1]);

    // 创建并绑定第二个VBO
    GLuint vbo2;
    glGenBuffers(1, &vbo2);
    glBindBuffer(GL_ARRAY_BUFFER, vbo2);
    glBufferData(GL_ARRAY_BUFFER, sizeof(vertices2), vertices2, GL_STATIC_DRAW);

    // 配置第二个VBO的顶点属性指针
    glVertexAttribPointer(0, 3, GL_FLOAT, GL_FALSE, sizeof(Vertex), (void*)offsetof(Vertex, position));
    glEnableVertexAttribArray(0);
    glVertexAttribPointer(1, 3, GL_FLOAT, GL_FALSE, sizeof(Vertex), (void*)offsetof(Vertex, color));
    glEnableVertexAttribArray(1);

    // 渲染循环
    while (!glfwWindowShouldClose(window)) {
        // 渲染第一个VAO、第一个VBO的内容
        glBindVertexArray(vao[0]);
        glBindBuffer(GL_ARRAY_BUFFER, vbo1);
        glDrawArrays(GL_TRIANGLES, 0, 3); // 绘制第一个矩形
        // 渲染第一个VAO、第二个VBO的内容
        glBindBuffer(GL_ARRAY_BUFFER, vbo1);
        glDrawArrays(GL_TRIANGLES, 0, 3); // 绘制第一个矩形

        // 渲染第二个VAO的内容
        glBindVertexArray(vao[1]);
        glDrawArrays(GL_TRIANGLES, 0, 3); // 绘制第二个矩形

        // 处理输入等

        // 交换缓冲和事件处理
    }

    // 删除VAO和VBO

    // 清理 GLFW 和 GLEW

    return 0;
}

```



## 5 着色器

--写在gpu上的代码

1. 先创建着色器
2. 再创建program，着色器依附于program
3. 最后使用program

```cpp
// 一 创建着色器
GLuint vshader = glCreateShader(GL_VERTEX_SHADER);
// 填充逻辑代码
glShaderSource(vshader, 1, &vertex_shader_source, NULL);
// 编译
glCompileShader(vshader);

// 是否编译成功
GLint vertex_compiled;
// 获取编译后的着色器信息
glGetShaderiv(vshader, GL_COMPILE_STATUS, &vertex_compiled);
if (vertex_compiled != GL_TRUE)
{
    GLsizei log_length = 0;
    GLchar message[1024];
    glGetShaderInfoLog(vshader, 1024, &log_length, message);
    // Write the error to a log
}

// 同理创建片段着色器
GLuint fshader = glCreateShader(GL_FRAGMENT_SHADER);
glShaderSource(fshader, 1, &fragment_shader_source, NULL); // fragment_shader_source is a GLchar* containing glsl shader source code
glCompileShader(fshader);

GLint fragment_compiled;
glGetShaderiv(fshader, GL_COMPILE_STATUS, &fragment_compiled);
if (fragment_compiled != GL_TRUE)
{
    GLsizei log_length = 0;
    GLchar message[1024];
    glGetShaderInfoLog(fshader, 1024, &log_length, message);
    // Write the error to a log
}


// 着色器运行在program上
// 二 创建program
GLuint program = glCreateProgram();

// 装入着色器
glAttachShader(program, vshader);
glAttachShader(program, fshader);
// 编译program
glLinkProgram(program);

// 是否编译成功
GLint program_linked;
glGetProgramiv(program, GL_LINK_STATUS, &program_linked);
if (program_linked != GL_TRUE)
{
    GLsizei log_length = 0;
    GLchar message[1024];
    glGetProgramInfoLog(program, 1024, &log_length, message);
    // Write the error to a log
}
```

==项目使用方式==

```cpp
#include <GL/glew.h>
#include <GLFW/glfw3.h>

#include <iostream>
#include <fstream>
#include <string>
#include <sstream>

struct ShaderCodeRes {
    std::string vertexShader;
    std::string fragmentShader;
};

// 着色器
//1 TODU: 从从指定文件中获取着色器逻辑代码
static ShaderCodeRes getCode(const std::string &filepath) 
    
    std::ifstream file(filepath);

    enum class ShaderType {
        NONE = -1, VERTEX = 0, FRAGMENT = 1
    };
    ShaderType type = ShaderType::NONE;

    std::string line;
    std::stringstream ss[2];
    while (getline(file, line))
    {
        // 分割不同的着色器
        if (line.find("#shader") != std::string::npos) {
            if (line.find("vertex") != std::string::npos) {
                // vertex shader
                type = ShaderType::VERTEX;
            }
            else if (line.find("fragment") != std::string::npos) {
                // fragment shader
                type = ShaderType::FRAGMENT;
            }
        }
        else {
            // 不分隔
            ss[(int)type] << line << '\n';

        }
    }
    return { ss[0].str(), ss[1].str()};
}

//2 TODU: 编译着色器
static int compileShader(GLenum shaderType ,const std::string &shader_source_code) {
    unsigned int shader = glCreateShader(shaderType);
    const char* src = shader_source_code.c_str();
    glShaderSource(shader, 1, &src, nullptr);
    glCompileShader(shader);

    // 编译是否成功
    int ret;
    glGetShaderiv(shader, GL_COMPILE_STATUS, &ret);
    if (ret == GL_FALSE) {
        int length;
        glGetShaderiv(shader, GL_INFO_LOG_LENGTH, &length);
        
        char *message = (char*)alloca(length * sizeof(char));
        glGetShaderInfoLog(shader, length, &length, message);
        
        std::cout << "failed to compile "
            << (shaderType == GL_VERTEX_SHADER ? "vertex" : "fragment")
            << std::endl;
        std::cout << message << std::endl;
        glDeleteShader(shader);
        return 0;
    }
    return shader;
}

//3 TODU: 创建program
static int creatShader(const std::string& filepath) {
    // program用于存放着色器，即着色器运行于program之上
    unsigned int program = glCreateProgram();
    // 创建编译好的着色器
    ShaderCodeRes mySource = getCode(filepath);
    unsigned int VShader = compileShader(GL_VERTEX_SHADER, mySource.vertexShader);
    unsigned int FShader = compileShader(GL_FRAGMENT_SHADER, mySource.fragmentShader);
    // attach着色器
    glAttachShader(program, VShader);
    glAttachShader(program, FShader );
    // 编译program
    glLinkProgram(program);

    glValidateProgram(program);

    glDeleteShader(VShader);
    glDeleteShader(FShader);

    return program;


}
int main(void)
{
    GLFWwindow* window;

    if (!glfwInit())
        return -1;

    window = glfwCreateWindow(640, 480, "Hello World", NULL, NULL);
    if (!window)
    {
        glfwTerminate();
        return -1;
    }

    glfwMakeContextCurrent(window);
    glewInit();
    if (glewInit() != GLEW_OK)
        std::cout << "Error" << std::endl;

    unsigned int buffer;
    float data[6] = {
        -0.5f, -0.5f,
         0.0f,  0.5f,
         0.5f, -0.5f
    };

    // 1、顶点缓冲区
    glGenBuffers(1, &buffer);
    glBindBuffer(GL_ARRAY_BUFFER, buffer);
    glBufferData(GL_ARRAY_BUFFER, 6 * sizeof(float),data, GL_STATIC_DRAW);

    // 设置布局
    glVertexAttribPointer(0, 2, GL_FLOAT, GL_FALSE, 2 * sizeof(float), 0);
    // 启用布局
    glEnableVertexAttribArray(0);

    // 2、着色器
    unsigned int shader = creatShader("./res/shaders/Basic.shader");
    glUseProgram(shader);

    while (!glfwWindowShouldClose(window))
    {
        /* Render here */
        glClear(GL_COLOR_BUFFER_BIT);

        glDrawArrays(GL_TRIANGLES, 0, 3);


        /* Swap front and back buffers */
        glfwSwapBuffers(window);

        /* Poll for and process events */
        glfwPollEvents();
    }

    glfwTerminate();
    return 0;
}

```

### 5.1 着色器分类

一个完整的着色器，包括顶点着色器 and 片元着色器

==通过顶点着色器的layout参数，来分辨具体是哪个着色器==

顶点着色器和片元着色器在OpenGL中协作以完成图形渲染的过程。它们是渲染管线中的两个重要部分，分别负责不同的任务，如下所示：

1. **顶点着色器（Vertex Shader）**：
   - 顶点着色器是渲染管线的第一个阶段。
   - 它处理每个输入顶点，执行坐标变换和其他计算，并将变换后的顶点位置传递给后续阶段。
   - 主要任务包括将顶点从模型空间（Object Space）变换到世界空间（World Space），然后变换到投影空间（Projection Space）。
   - 通常，顶点着色器还可以计算法线、纹理坐标等附加数据。

2. **片元着色器（Fragment Shader）**：
   - 片元着色器是渲染管线的倒数第二个阶段，也是渲染管线中的最后一个可编程阶段。
   - 它处理每个像素（片元）的计算，根据输入的片元属性和纹理信息，计算最终的颜色值。
   - 主要任务包括执行光照计算、纹理采样、透明度计算等，以确定像素的最终颜色。
   - 片元着色器输出的颜色会传递给帧缓冲（Framebuffer），最终呈现在屏幕上。

协作过程：

1. **顶点着色器传递数据**：顶点着色器负责将顶点的位置和其他属性传递给片元着色器。这通常是通过设置输出变量（out variables）来实现的，例如，将顶点位置传递给片元着色器以用于光照计算或纹理采样。

2. **片元着色器计算颜色**：片元着色器根据输入的数据，如顶点着色器传递的位置、法线、纹理坐标等，以及应用程序传递的统一变量，执行各种计算，以确定片元的最终颜色。这包括光照模型的计算、纹理采样、颜色混合等。

3. **片元着色器输出颜色**：片元着色器计算完成后，将最终的颜色值设置为输出变量（通常是`gl_FragColor`），表示当前片元的颜色。

4. **渲染到帧缓冲**：最终的颜色值由片元着色器输出，并渲染到帧缓冲（Framebuffer）。帧缓冲包含了最终图像的像素数据。

5. **屏幕显示**：帧缓冲中的图像最终被显示在屏幕上，形成渲染的最终图像。

这种协作方式允许OpenGL实现高度可定制的渲染效果，包括复杂的光照、纹理映射、阴影、透明度效果等。顶点着色器和片元着色器的编写和组织是实现图形渲染效果的关键。通过在这两个阶段中进行计算和处理，你可以控制渲染过程中的各个方面，从而创建出所需的视觉效果。

### 5.2 着色器逻辑代码

* 通过GLSL语法编写
* 着色器构造：版本、输入、输出、main\
* ==在OpenGL中，`layout(location = 0)` 是一种用于指定变量在顶点数据中的位置索引（location index）的语法，通常用于顶点着色器中的输入变量。==

1. 顶点着色器

```cpp
#version 330 core
layout(location = 0) in vec4 aPos;

out vec4 vertexColor;// 顶点着色器可以省略

void main(){
	// 顶点着色器可以设置gl_Position来表示变换后的顶点位置,为vec4类型
    gl_Position = aPos;
}
```

2. 片元着色器

```cpp
#version 330 core
in vec4 vertexColor; 

out vec4 finalColor;

void main(){
    // 1. 片元着色器可以设置gl_FragColor或gl_FragData来表示片元的颜色(vec4型)
    gl_FragColor = vertexColor;
    gl_FragColor = vec4(0.5f, 0.5f, 0.5f ,1.0f); // 自己指定
    
    // 2. 也以定义自己的输出变量来表示最终的片元颜色或其他信息
	finalColor = vertexColor;
}
```

### 5.3 Uniform变量

Uniform是一种从CPU中的应用向GPU中的着色器发送数据的方式，但uniform和顶点属性有些不同。首先，uniform是全局的(Global)。全局意味着uniform变量必须在每个着色器程序对象中都是独一无二的，而且它可以被着色器程序的任意着色器在任意阶段访问。第二，无论你把uniform值设置成什么，uniform会一直保存它们的数据，直到它们被重置或更新。

我们可以在一个着色器中添加`uniform`关键字至类型和变量名前来声明一个GLSL的uniform。

==如果你声明了一个uniform却在GLSL代码中没用过，编译器会静默移除这个变量，导致最后编译出的版本中并不会包含它，这可能导致几个非常麻烦的错误===

```cpp
#version 330 core
out vec4 FragColor;

uniform vec4 ourColor; // 在OpenGL程序代码中设定这个变量

void main()
{
    FragColor = ourColor;
}
```

```cpp
// openGL代码
// TUDO 3: 着色器
unsigned int shader = creatShader("./res/shaders/Basic.shader");
glUseProgram(shader);

// 统一变量（先使用program后，才能使用统一变量）
int location = glGetUniformLocation(shader, "u_Color");
assert(location != -1);
glUniform4f(location, 0.8f, 0.5f, 0.8f, 1.0f);
```

## 6 小结完整代码

```cpp
#include <GL/glew.h>
#include <GLFW/glfw3.h>

#include <iostream>
#include <fstream>
#include <string>
#include <sstream>

#include <assert.h>

struct ShaderCodeRes {
    std::string vertexShader;
    std::string fragmentShader;
};

// 着色器

static ShaderCodeRes getCode(const std::string &filepath) {
    // 从指定文件中获取着色器逻辑代码
    std::ifstream file(filepath);

    enum class ShaderType {
        NONE = -1, VERTEX = 0, FRAGMENT = 1
    };
    ShaderType type = ShaderType::NONE;

    std::string line;
    std::stringstream ss[2];
    while (getline(file, line))
    {
        // 分割不同的着色器
        if (line.find("#shader") != std::string::npos) {
            if (line.find("vertex") != std::string::npos) {
                // vertex shader
                type = ShaderType::VERTEX;
            }
            else if (line.find("fragment") != std::string::npos) {
                // fragment shader
                type = ShaderType::FRAGMENT;
            }
        }
        else {
            // 不分隔
            ss[(int)type] << line << '\n';

        }
    }
    return { ss[0].str(), ss[1].str()};
}
static int compileShader(GLenum shaderType ,const std::string &shader_source_code) {
    unsigned int shader = glCreateShader(shaderType);
    const char* src = shader_source_code.c_str();
    glShaderSource(shader, 1, &src, nullptr);
    glCompileShader(shader);

    // 编译是否成功
    int ret;
    glGetShaderiv(shader, GL_COMPILE_STATUS, &ret);
    if (ret == GL_FALSE) {
        int length;
        glGetShaderiv(shader, GL_INFO_LOG_LENGTH, &length);

        char *message = (char*)alloca(length * sizeof(char));
        glGetShaderInfoLog(shader, length, &length, message);
        std::cout << "failed to compile "
            << (shaderType == GL_VERTEX_SHADER ? "vertex" : "fragment")
            << std::endl;
        std::cout << message << std::endl;

        // 编译完毕即可删除shader
        glDeleteShader(shader);
        return 0;
    }
    return shader;
}
static int creatShader(const std::string& filepath) {
    // program用于存放着色器，即着色器运行于program之上
    unsigned int program = glCreateProgram();
    // 创建编译好的着色器
    ShaderCodeRes mySource = getCode(filepath);
    unsigned int VShader = compileShader(GL_VERTEX_SHADER, mySource.vertexShader);
    unsigned int FShader = compileShader(GL_FRAGMENT_SHADER, mySource.fragmentShader);
    // attach着色器
    glAttachShader(program, VShader);
    glAttachShader(program, FShader );
    // 编译program
    glLinkProgram(program);

    glValidateProgram(program);

    glDeleteShader(VShader);
    glDeleteShader(FShader);

    return program;


}
int main(void)
{
    // TODU 0: 创建窗口
    GLFWwindow* window;

    if (!glfwInit())
        return -1;

    window = glfwCreateWindow(640, 480, "Hello World", NULL, NULL);
    if (!window)
    {
        glfwTerminate();
        return -1;
    }

    // 创建上下文
    glfwMakeContextCurrent(window);

    // 调整窗口刷新频率
    glfwSwapInterval(1);
    
    // 启动glew
    glewInit();
    if (glewInit() != GLEW_OK)
        std::cout << "Error" << std::endl;

    
    float data[] = {
        -0.5f, -0.5f, //0
         0.5f,  0.5f, //1
         0.5f, -0.5f, //2
         -0.5f, 0.5f  //3
    };

    // 索引缓冲区
    unsigned int indies[]{
        0, 2, 1,
        1, 3, 0
    };

    // 顶点数组对象（VAO）
    unsigned int vao;
    glGenVertexArrays(1, &vao);
    glBindVertexArray(vao);

    // TODU 1: 顶点缓冲区
    unsigned int buffer;
    glGenBuffers(1, &buffer);
    glBindBuffer(GL_ARRAY_BUFFER, buffer);
    glBufferData(GL_ARRAY_BUFFER, 8 * sizeof(float), data, GL_STATIC_DRAW);

    // 设置顶点属性指针（设置布局）
    glVertexAttribPointer(0, 2, GL_FLOAT, GL_FALSE, 2 * sizeof(float), (void *)0);
    // 启用布局
    glEnableVertexAttribArray(0);

    // TODU 2: 索引缓冲区
    unsigned int indexBuffer;
    glGenBuffers(1, &indexBuffer);
    glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, indexBuffer);
    glBufferData(GL_ELEMENT_ARRAY_BUFFER, 6 * sizeof(unsigned int), indies, GL_STATIC_DRAW);

    // TUDO 3: 着色器
    unsigned int shader = creatShader("./res/shaders/Basic.shader");
    glUseProgram(shader);

    // 统一变量
    int location = glGetUniformLocation(shader, "u_Color");
    assert(location != -1);
    glUniform4f(location, 0.8f, 0.5f, 0.8f, 1.0f);
    // 颜色变换
    float r = 0.8f;
    float tmp = 0.1f;

    while (!glfwWindowShouldClose(window))
    {
        // 清楚颜色缓存
        glClear(GL_COLOR_BUFFER_BIT);

        // TODU 4: 画图
        //glDrawArrays(GL_TRIANGLES, 0, 3);
        //glBindVertexArray(vao);
        glDrawElements(GL_TRIANGLES,6,GL_UNSIGNED_INT, nullptr);

        // 颜色变化
        glUniform4f(location, r, 0.5f, 0.8f, 1.0f);
        if (r > 1.0f) tmp = -0.1f;
        else if (r < 0.0f) tmp = 0.1f;
        r += tmp;


        /* Swap front and back buffers */
        glfwSwapBuffers(window);

        /* Poll for and process events */
        glfwPollEvents();
    }

    glfwTerminate();
    return 0;
}

```

# 二 抽象成类

## 1 VAO

```cpp
// .c
#pragma once

#include "VertexBuffer.h"
//#include "VertexBufferLayout.h"


class VertexBufferLayout;

class VertexArray
{
private:
	unsigned int VaoID_;

public:
	VertexArray();
	~VertexArray();

	void bind() const;
	void unBind() const;

	// 添加VBO和顶点属性指针（layout布局）
	void addBuffer(const VertexBuffer &vb, const VertexBufferLayout &layout) ;

};
```

```cpp
// .cpp
#include "VertexArray.h"
#include "Renderer.h"

#include "VertexBufferLayout.h"

VertexArray::VertexArray()
{
    GLCall(glGenVertexArrays(1,&VaoID_));
    GLCall(glBindVertexArray(VaoID_));

}

VertexArray::~VertexArray()
{
    GLCall(glDeleteVertexArrays(1, &VaoID_));
}

void VertexArray::bind() const
{
    GLCall(glBindVertexArray(VaoID_));
}

void VertexArray::unBind() const
{
    GLCall(glBindVertexArray(0));
}

void VertexArray::addBuffer(const VertexBuffer& vb, const VertexBufferLayout& layout)
{   
    this->bind();
    vb.bind();

    // element:{2个顶点，GL_FLOAT类型， false不需要规范化}
    // elements 为一个vector
    const auto& elements = layout.getElements();
    unsigned int offset = 0;
    for (unsigned int i = 0; i < elements.size(); i++) {
        const auto &element = elements[i];
        
        GLCall(glVertexAttribPointer(i, element.count, element.type, element.normalized, layout.getStride(), (const void*)offset));
        offset += layout.getStride();

        // 启用布局
        GLCall(glEnableVertexAttribArray(i));
    }


}
```



## 2 VBO

```cpp
// .c
#pragma once
#include <GL/glew.h>


class VertexBuffer {
private:
	unsigned int bufferID_;

public:
	VertexBuffer(const void* data, unsigned int size);
	~VertexBuffer();

	void bind() const;
	void unBind() const;

};
```

```cpp
// .cpp
#include "VertexBuffer.h"
#include "Renderer.h"

VertexBuffer::VertexBuffer(const void* data, unsigned int size)
{

	GLCall(glGenBuffers(1, &bufferID_));
	GLCall(glBindBuffer(GL_ARRAY_BUFFER, bufferID_));
	GLCall(glBufferData(GL_ARRAY_BUFFER, size, data, GL_STATIC_DRAW));
}

VertexBuffer::~VertexBuffer()
{
	GLCall(glDeleteBuffers(1, &bufferID_));

}

void VertexBuffer::bind() const
{
	GLCall(glBindBuffer(GL_ARRAY_BUFFER, bufferID_));
}

void VertexBuffer::unBind() const
{
	GLCall(glBindBuffer(GL_ARRAY_BUFFER, 0));
}

```



## 3 layout

```cpp
// c.
#pragma once

#include <vector>
#include <GL/glew.h>
#include "Renderer.h"

struct VertexBufferElement {
	unsigned int count;
	unsigned int type;
	unsigned char normalized;

	static unsigned int getSizeOfType(unsigned int type) {
		switch (type)
		{
		case GL_FLOAT: return 4;
		case GL_UNSIGNED_INT: return 4;
		case GL_UNSIGNED_BYTE: return 1;
		}
		ASSERT(false);
		return 0;

	}
};
class VertexBufferLayout
{
private:
	std::vector<VertexBufferElement> m_Elements;
	unsigned int m_stride;
public:
	VertexBufferLayout() : m_stride(0) {};
	~VertexBufferLayout() {};


	// 只需要插入每一组属性的个数，自动计算出所有待填充的属性
	template<typename T>
	void push(unsigned int count) {
		static_assert(sizeof(T) == 0, "Invalid type used in push<T>");
	}

	// glVertexAttribPointer(0, 2, GL_FLOAT, GL_FALSE, 2 * sizeof(float), (void *)0);
	template<>
	void push<float>(unsigned int count) {
		m_Elements.push_back({ count, GL_FLOAT, GL_FALSE } );
		m_stride += count * VertexBufferElement::getSizeOfType(GL_FLOAT);

	}
	template<>
	void push<unsigned int>(unsigned int count) {
		m_Elements.push_back({ count, GL_UNSIGNED_INT, GL_FALSE });
		m_stride += count * VertexBufferElement::getSizeOfType(GL_UNSIGNED_INT);

	}
	template<>
	void push<unsigned char>(unsigned int count) {
		m_Elements.push_back({ count, GL_UNSIGNED_BYTE, GL_TRUE });
		m_stride += count * VertexBufferElement::getSizeOfType(GL_UNSIGNED_BYTE);

	}

	//element:{2个顶点，GL_FLOAT类型， false不需要规范化}
	inline const std::vector<VertexBufferElement> getElements() const { return m_Elements; }
	inline const unsigned int getStride() const { return m_stride; }

};


```



## 4 EBO

```cpp
// .c
#pragma once
#include <GL/glew.h>

class IndexBuffer {
private:
	unsigned int bufferID_;
	unsigned int count_;

public:
	IndexBuffer(const unsigned int* data, unsigned int count);
	~IndexBuffer();

	void bind() const;
	void unBind() const;

	inline unsigned int getCount() const { return count_; }

};

```

```cpp
// .cpp
#include "IndexBuffer.h"
#include "Renderer.h"

IndexBuffer::IndexBuffer(const unsigned int* data, unsigned int count) : count_(count)
{
	ASSERT(sizeof(unsigned int) == sizeof(GLuint));

	glGenBuffers(1, &bufferID_);
	glBindBuffer(GL_ELEMENT_ARRAY_BUFFER, bufferID_);
	glBufferData(GL_ELEMENT_ARRAY_BUFFER, count * sizeof(unsigned int), data, GL_STATIC_DRAW);
}

IndexBuffer::~IndexBuffer()
{
	GLCall(glDeleteBuffers(1, &bufferID_));

}

void IndexBuffer::bind() const
{
	GLCall(glBindBuffer(GL_ARRAY_BUFFER, bufferID_));
}

void IndexBuffer::unBind() const
{
	GLCall(glBindBuffer(GL_ARRAY_BUFFER, 0));
}

```

## 5 shader

```cpp
// .c
#pragma once
#include <GL/glew.h>

#include <iostream>
#include <fstream>
#include <string>
#include <sstream>

struct ShaderCodeRes {
    std::string vertexShader;
    std::string fragmentShader;
};
class Shader
{
private:

    unsigned int shader_;

    static ShaderCodeRes getCode( const std::string& filepath) ;

    static unsigned int compileShader(GLenum shaderType, const std::string& shader_source_code);

    static int creatShader(const std::string& filepath);

    // int getUniformLocation(const std::string& ele_name) const;
public:
    Shader(const std::string& filepath);
    ~Shader();

    void bind() const;
    void unBind() const;

    void setUniform4f(const std::string& name, float v0, float v1, float v2, float v3) ;

};
```

```cpp
// .cpp
#include "Shader.h"
#include "Renderer.h"

// 着色器

ShaderCodeRes Shader::getCode( const std::string& filepath) {
    // 从指定文件中获取着色器逻辑代码
    std::ifstream file(filepath);

    enum class ShaderType {
        NONE = -1, VERTEX = 0, FRAGMENT = 1
    };
    ShaderType type = ShaderType::NONE;

    std::string line;
    std::stringstream ss[2];
    while (getline(file, line))
    {
        // 分割不同的着色器
        if (line.find("#shader") != std::string::npos) {
            if (line.find("vertex") != std::string::npos) {
                // vertex shader
                type = ShaderType::VERTEX;
            }
            else if (line.find("fragment") != std::string::npos) {
                // fragment shader
                type = ShaderType::FRAGMENT;
            }
        }
        else {
            // 不分隔
            ss[(int)type] << line << '\n';

        }
    }
    return { ss[0].str(), ss[1].str() };
}

unsigned int Shader::compileShader(GLenum shaderType, const std::string& shader_source_code) {
    unsigned int shader = glCreateShader(shaderType);
    const char* src = shader_source_code.c_str();
    glShaderSource(shader, 1, &src, nullptr);
    glCompileShader(shader);

    // 编译是否成功
    int ret;
    glGetShaderiv(shader, GL_COMPILE_STATUS, &ret);
    if (ret == GL_FALSE) {
        int length;
        glGetShaderiv(shader, GL_INFO_LOG_LENGTH, &length);

        char* message = (char*)alloca(length * sizeof(char));
        glGetShaderInfoLog(shader, length, &length, message);
        std::cout << "failed to compile "
            << (shaderType == GL_VERTEX_SHADER ? "vertex" : "fragment")
            << std::endl;
        std::cout << message << std::endl;

        // 编译完毕即可删除shader
        glDeleteShader(shader);
        return 0;
    }
    return shader;
}

int Shader::creatShader(const std::string& filepath)
{
    // program用于存放着色器，即着色器运行于program之上
    unsigned int program = glCreateProgram();
    // 创建编译好的着色器
    ShaderCodeRes mySource = getCode(filepath);
    unsigned int VShader = compileShader(GL_VERTEX_SHADER, mySource.vertexShader);
    unsigned int FShader = compileShader(GL_FRAGMENT_SHADER, mySource.fragmentShader);
    // attach着色器
    glAttachShader(program, VShader);
    glAttachShader(program, FShader);
    // 编译program
    glLinkProgram(program);

    glValidateProgram(program);

    glDeleteShader(VShader);
    glDeleteShader(FShader);

    return program;
}

Shader::Shader(const std::string& filepath) : shader_(creatShader(filepath))
{
}

Shader::~Shader()
{
    glDeleteProgram(shader_);
}

void Shader::bind() const 
{
    glUseProgram(shader_);
}

void Shader::unBind() const
{
    glDeleteProgram(shader_);
}

void Shader::setUniform4f(const std::string& name, float v0, float v1, float v2, float v3)
{
    GLCall(unsigned int location = glGetUniformLocation(shader_, name.data()));
    ASSERT(location != -1);
    glUniform4f(location, v0, v1, v2, v3);
}
```

## 6 renderer

```cpp
// .c
#pragma once

#include <GL/glew.h>
#include <iostream>

#include "VertexArray.h"
#include "VertexBuffer.h"
#include "Shader.h"
#include "IndexBuffer.h"

#define ASSERT(x) if (!(x)) __debugbreak();
#define GLCall(x) GLClearError();\
	x;\
	ASSERT(GLLogCall(#x, __FILE__, __LINE__))

void GLClearError();

bool GLLogCall(const char* funcion, const char* file, int line);

class Renderer {
private:
	
public:
	void draw(const VertexArray& va, const VertexBuffer& vb, const Shader& shader, const IndexBuffer& ib) const;
	void clear() const;
};

```

```cpp
// .cpp
#include "Renderer.h"

void GLClearError() {
	while (glGetError() != GL_NO_ERROR);
}

bool GLLogCall(const char* funcion, const char* file, int line) {
	while (GLenum error = glGetError()) {
		std::cout << "[OpenGL Error] (" << error << "): " << funcion <<
			" " << file << ":" << line << std::endl;
		return false;
	}
	return true;
}

void Renderer::draw(const VertexArray& va, const VertexBuffer& vb,  const Shader& shader, const IndexBuffer& ib) const 
{
	va.bind();
	vb.bind();
	ib.bind();
	shader.bind();
	glDrawElements(GL_TRIANGLES, ib.getCount(), GL_UNSIGNED_INT, nullptr);
}

void Renderer::clear() const
{
	glClear(GL_COLOR_BUFFER_BIT);
}

```

## 7 main代码

```cpp
#include <GL/glew.h>
#include <GLFW/glfw3.h>

#include <iostream>
#include <fstream>
#include <string>
#include <sstream>

#include <assert.h>
#include "Renderer.h"

#include "VertexBuffer.h"
#include "IndexBuffer.h"
#include "VertexArray.h"
#include "VertexBufferLayout.h"
#include "Shader.h"

int main(void)
{
    // TODU 0: 创建窗口
    GLFWwindow* window;

    if (!glfwInit())
        return -1;

    window = glfwCreateWindow(640, 480, "Hello World", NULL, NULL);
    if (!window)
    {
        glfwTerminate();
        return -1;
    }

    // 创建上下文
    glfwMakeContextCurrent(window);

    // 调整窗口刷新频率
    glfwSwapInterval(1);
    
    // 启动glew
    glewInit();
    if (glewInit() != GLEW_OK)
        std::cout << "Error" << std::endl;

    
    float data[] = {
        -0.5f, -0.5f, //0
         0.5f,  0.5f, //1
         0.5f, -0.5f, //2
         -0.5f, 0.5f  //3
    };

    // 索引缓冲区
    unsigned int indies[]{
        0, 2, 1,
        1, 3, 0
    };

    // TODU 1: 顶点数组对象（VAO）
    VertexArray va;

    // TODU 2: 顶点缓冲区
    VertexBuffer vb(data, 8 * sizeof(float));

    // TODU 3: 设置顶点属性指针（设置布局）
    VertexBufferLayout layout;
    layout.push<float>(2);
    va.addBuffer(vb, layout);


    // TODU 4: 索引缓冲区
    IndexBuffer ib(indies, 6);

    // TUDO 5: 着色器
    Shader shader("./res/shaders/Basic.shader");
    shader.bind();
    
    // 颜色变换
    float r = 0.8f;
    float tmp = 0.1f;

    while (!glfwWindowShouldClose(window))
    {
        // TODU 6: 渲染
        Renderer renderer;
        renderer.clear();
        renderer.draw(va, vb, shader, ib);

        // TODU 7: uniform变量
        shader.setUniform4f("u_Color", r, 0.5f, 0.8f, 1.0f);
        if (r > 1.0f) tmp = -0.1f;
        else if (r < 0.0f) tmp = 0.1f;
        r += tmp;


        /* Swap front and back buffers */
        glfwSwapBuffers(window);

        /* Poll for and process events */
        glfwPollEvents();
    }
    
    // 删除着色器
    shader.unBind();
    
    glfwTerminate();
    return 0;
}

```



# 三 坐标变换



<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230913163138430.png" alt="image-20230913163138430" style="zoom:67%;" />

## 3.1 向量和矩阵

| type        | +-       | 标量+*       | 同类型相乘   | 矩阵 * 向量          |
| :---------- | -------- | ------------ | ------------ | -------------------- |
| 向量 vector | 同类相加 | 每一位同时+* | 点乘、叉乘   | 对向量的线性空间变换 |
| 矩阵 matrix | 同类相加 | 每一位同时+* | 矩阵相乘法则 |                      |

向量点乘：<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230914100056932.png" alt="image-20230914100056932" style="zoom: 50%;" />



向量叉乘： <img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230914100134179.png" alt="image-20230914100134179" style="zoom:50%;" />

## 3.2 GLM库

下载：https://github.com/g-truc/glm/releases/tag/0.9.9.8

### 3.2.1 头文件

```cpp
//GLM对于矩阵数据类型的定义
#include "glm/glm.hpp"

// 生成变换矩阵的函数。
#inclde "glm/gtc/matrix_transform.hpp"

// 生成投影矩阵的函数。
#include "glm/ext/matrix_clip_space.hpp"

// 将数组转换成矩阵的函数、以及glm::value_ptr函数
#inclde "glm/gtc/type_ptr.hpp"
```



### 3.2.2 矩阵函数

一、常用类型

- vec2 二维向量
- vec3 三维向量
- vec4 四维向量
- mat2 二阶矩阵
- mat3 三阶矩阵
- mat4 四阶矩阵

二、默认构造

```cpp
glm::mat4 trans;  // 0矩阵
glm::mat4 trans = glm::mat4(1.0f); // 单位矩阵
```



三、常用函数
| 返回值       | 函数                                                         | 参数                                           |
| ------------ | ------------------------------------------------------------ | ---------------------------------------------- |
| 平移矩阵     | `glm::translate(matrix, vec3(1.0, 2.0, 3.0))`                | （单位矩阵、各个方向的平移量）                 |
| 旋转矩阵     | `glm::rotate(matrix, glm::radians(90.0f), vec3(1.0, 0.0, 0.0))` | (单位矩阵， 逆时针旋转弧度， 旋转轴（可多选）) |
| 角度 -> 弧度 | `glm::radians(90.0f)`                                        |                                                |
| 缩放矩阵     | `glm::scale(matrix, vec3(0.5f, 0.5f, 0.5f))`                 | (单位矩阵， 各轴缩放系数)                      |
| 正交投影矩阵 | `glm::ortho(float left, float right, float bottom, float top, float zNear, float zFar);` |                                                |
| 透视投影矩阵 | `glm::perspective(float fovy, float aspect, float zNear, float zFar)` |                                                |
| 矩阵 -> 数组 | `glm::value_ptr(matrix)`                                     | 搭配 `glUniformMatrix4fv` 向着色器传矩阵       |

glm::ortho(float left, float right, float bottom, float top, float zNear, float zFar);
正交投影矩阵。前四个参数分别是视口的左、右、上、下坐标。第五和第六个参数则定义了近平面和远平面的距离。
glm::perspective(float fovy, float aspect, float zNear, float zFar);
透视投影矩阵。第一个参数为视锥上下面之间的夹角，第二个参数为视口宽高比，第三、四个参数分别为近平面和远平面的深度。



四、着色器函数

```cpp
void glUniformMatrix4fv (GLint location, GLsizei count, GLboolean transpose, const GLfloat * value);
location: uniform的位置。
count: 需要加载数据的数组元素的数量或者需要修改的矩阵的数量。
transpose: 列优先矩阵传false,行优先矩阵传true。
value: 指向由count个元素的数组的指针。
    
void glUniformMatrix4fv (location, 1, GL_FALSE, glm::value_ptr(matrix));
```



![image-20230914113738415](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20230914113738415.png)
