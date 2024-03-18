#  零  windows环境配置

[课程框架](https://www.0voice.com/uiwebsite/html/courses/av.html)

[开发环境搭建](E:\typora索引文件\ffmpeg\Windows 10开发环境搭建.pdf)

[技术博客](https://www.cnblogs.com/leisure_chn/category/1351812.html?page=1)

## 0.1 QT配置



![image-20231106220236475](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231106220236475.png)

* 在项目文件中，加入ffmpeg的源码 `D:\videoTools\ffmpeg-4.2.1-win32-dev`

* 编译器选择msvc的c++版本下x86（32bit），使用CDB进行调试

```
注意：调试必须： 编译器--cdb--ffmpeg源码 均为 32bit或者64bit，否则调试失败
```



* .pro文件补充ffmprg路径

```cpp
TEMPLATE = app
CONFIG += console c++11
CONFIG -= app_bundle
CONFIG -= qt

SOURCES += \
        main.cpp
win32 {
INCLUDEPATH += $$PWD/ffmpeg-4.2.1-win32-dev/include
LIBS += $$PWD/ffmpeg-4.2.1-win32-dev/lib/avformat.lib \
$$PWD/ffmpeg-4.2.1-win32-dev/lib/avcodec.lib \
$$PWD/ffmpeg-4.2.1-win32-dev/lib/avdevice.lib \
$$PWD/ffmpeg-4.2.1-win32-dev/lib/avfilter.lib \
$$PWD/ffmpeg-4.2.1-win32-dev/lib/avutil.lib \
$$PWD/ffmpeg-4.2.1-win32-dev/lib/postproc.lib \
$$PWD/ffmpeg-4.2.1-win32-dev/lib/swresample.lib \
$$PWD/ffmpeg-4.2.1-win32-dev/lib/swscale.lib
}

```

* main 文件中导入头文件，ffmpeg是c头文件，标明

```cpp
#include <iostream>

using namespace std;

extern "C"
{
#include "libavutil/avutil.h"
}

int main()
{
    cout << "Hello FFMPEG," << av_version_info() << endl;
    return 0;
}

```

## 0.2 编译ffmpeg

### 0.2.1 安装msys

* 下载：百度网盘/计算机/音视频/02环境搭建/msys2-x86_64-20231026.exe，  安装到文件msys64

* 按照安装文档：启动命令行，跳过前面的修改文件步骤

* 配置环境，将vs路径集成到msys环境内

既然你已经确定了 `lib.exe` 的路径，你可以按照以下步骤将其添加到 `msys` 环境的 PATH 变量中：

1. 打开 `msys` 终端。

```
msys2_shell.cmd -mingw64
```



1. 使用文本编辑器打开你的用户目录下的 `.bashrc` 文件。你可以使用 `nano` 或 `vim`，例如：

   ```bash
   nano ~/.bashrc
   ```

2. 在 `.bashrc` 文件的末尾，添加以下行来更新 PATH 环境变量，替换成你的 `lib.exe` 路径：

   ```bash
   export PATH=$PATH:/d/VisualStudio/IDE/VC/Tools/MSVC/14.35.32215/bin/Hostx86/x86
   ```

   ctrl + x 保存， 回车确定，CTRL+x 退出

3. 保存 `.bashrc` 文件并退出文本编辑器。

4. 为了让更改生效，你需要重新加载 `.bashrc` 文件，可以通过关闭并重新打开 `msys` 终端，或者在终端中输入以下命令：

   ```bash
   source ~/.bashrc
   ```

5. 验证 `lib` 命令是否可以在 `msys` 终端中使用，通过键入：

   ```bash
   lib /?
   ```

这应该会显示 `lib` 命令的帮助信息，表明它已经被正确地添加到 PATH 中，并且可以在 `msys` 终端中调用了。

### 0.2.2 安装第三方库

* 执行4.3 安装编译环境
* 补充 4.5.1编译脚本
  * 编译x264

```
./configure --prefix=/d/videoTools/ffmpeg/build/libx264 --host=x86_64-w64-mingw32 --enable-shared --enable-static --extra-ldflags=-Wl,--output-def=libx264.def

make -j4

make install
```

编译成功

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231107002610269.png" alt="image-20231107002610269" style="zoom:50%;" />

* 编译fdk

```
cd fdk-aac

./configure --prefix=/d/videoTools/ffmpeg/build/libfdk-aac --enable-static --enable-shared

make -j4
make install
```

* 编译mp3

```
cd lame

./configure --prefix=/d/videoTools/ffmpeg/build/libmp3lame --disable-shared --disable-frontend --enable-static

make -j4
make install
```

*  编译libvpx

```
cd libvpx

./configure --prefix=/d/videoTools/ffmpeg/build/libvpx --disable-examples --disable-unit-tests --enable-vp9-highbitdepth --as=yasm

make -j4
make install
```

### 0.2.3 安装ffmpeg

* 编译ffmpeg

  将下面写到文件中 命名 `build_ffmpeg.sh`，放入ffmpeg源码文件中

```
./configure \
    --prefix=/d/videoTools/ffmpeg/build/ffmepg-4.2 \
    --arch=x86_64 \
    --enable-shared \
    --enable-gpl \
    --enable-libfdk-aac \
    --enable-nonfree \
    --enable-libvpx \
    --enable-libx264 \
    --enable-libmp3lame \
    --extra-cflags="-I/d/videoTools/ffmpeg/build/libfdk-aac/include" \
    --extra-ldflags="-L/d/videoTools/ffmpeg/build/libfdk-aac/lib" \
    --extra-cflags="-I/d/videoTools/ffmpeg/build/libvpx/include" \
    --extra-ldflags="-L/d/videoTools/ffmpeg/build/libvpx/lib" \
    --extra-cflags="-I/d/videoTools/ffmpeg/build/libx264/include" \
    --extra-ldflags="-L/d/videoTools/ffmpeg/build/libx264/lib" \
    --extra-cflags="-I/d/videoTools/ffmpeg/build/libmp3lame/include" \
    --extra-ldflags="-L/d/videoTools/ffmpeg/build/libmp3lame/lib"
```

* 执行脚本

  `sh build_ffmpeg.sh`

* 编译

  `make -j8`

  报错：

  <img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231107171405164.png" alt="image-20231107171405164" style="zoom:50%;" />

  解决方案：修改`D:\videoTools\ffmpeg\ffmpeg\libavcodec\x86\mathops.h`

https://github.com/FFmpeg/FFmpeg/commit/effadce6c756247ea8bae32dc13bb3e6f464f0eb

修改后的文件：百度网盘\计算机、音视频\开发环境搭建\windows编译ffmpeg\ffmpeg\mathops.h

* 安装 make install
* 进入`D:\videoTools\ffmpeg\build\ffmepg-4.2\bin`,执行ffmepg.exe，缺少动态库
* 手动添加动态库，通过/other_soft/everything软件，查找缺失的动态库，选择x64版本放入bin目录下

## 0.3 QT调用ffmepg库

* 将`D:\videoTools\ffmpeg\build`的 `ffmepg-4.2`导入工程目录
* 工程文件`.pro`中加入

```shell
INCLUDEPATH += \
    $$PWD/ffmepg-4.2/include
LIBS += $$PWD/ffmepg-4.2/bin/avformat.lib \
    $$PWD/ffmepg-4.2/bin/avcodec.lib \
    $$PWD/ffmepg-4.2/bin/avdevice.lib \
    $$PWD/ffmepg-4.2/bin/avfilter.lib \
    $$PWD/ffmepg-4.2/bin/avutil.lib \
    $$PWD/ffmepg-4.2/bin/swresample.lib \
    $$PWD/ffmepg-4.2/bin/swscale.lib

```

* 执行，产生build-xxxxx-debug文件
* 将[ffmepg-4.2的](D:\QT\project\audio_vedio_learn\ffmpeg副本\audio_decode\ffmepg-4.2)bin目录下的`.ddl`文件，拷贝到build-xx-debug文件中，与debug、release文件并列
* 若编译错误，删除debug和release文件，重新执行

```c
#include <stdio.h>

#include "libavutil/avutil.h"

int main()
{
    printf("Hello FFMPEG, version is %s\n", av_version_info());
    return 0;
 }

```

```cpp
#include <iostream>
extern "C" {
    #include "libavutil/avutil.h"
}

using namespace std;

int main()
{
    cout << "Hello ffmpeg is" << av_version_info() << endl;
    return 0;
}

```

## 0.4 vs调用ffmpeg库

[windows编译好的库下载地址](https://www.ffmpeg.org/download.html)

=== 将bin下的dll文件，拷贝到项目运行目录下<x64/debug>

![image-20231212123143658](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231212123143658.png)

## 0.5 软件

* PCM : AudaCity
* aac / mp3 : ffplay
* YUV: YUVPlayer
* h264 :H264Visa
* MP4 : H64Visa  MediaInfo
* FLV : H264Visa MediaInfo

# 名词

## 帧率

帧率：FPS（每秒钟要多少帧画面）以及Gop（表示多少秒一个I帧）。帧率就是在1秒钟时间里传输的图片的帧数，也可以理解为图形处理器每秒钟能够刷新几次。

帧率影响的是**画面流畅度**，与画面流畅度成正：帧率越大，画面越流畅；帧率越小，画面越有跳动感。

如果限定一个码率，比如800kbps，那么帧率越高，编码器就必须加大对单帧画面的压缩比，也就是通过降低画质来承载足够多的帧数。如果视频源来自摄像头，24FPS已经是肉眼极限，所以一般20帧的FPS就已经可以达到很好的用户体验了。

音视频一帧的字节数取决于多个因素，包括编码格式、分辨率、色彩深度、采样率等。不同的编码标准和参数设置会导致每一帧的数据量差异很大。

以下是一些常见的音视频编码格式及其一帧的字节数的大致范围：

### 视频帧：

1. **H.264（AVC）：** 视频帧的大小会根据分辨率、帧率和压缩质量等因素而变化。对于典型的高清视频，一帧的大小可能在几十KB到几百KB之间。

2. **H.265（HEVC）：** H.265 相对于 H.264 具有更高的压缩效率，因此相同质量的视频帧通常会更小。一帧的大小可能在几十KB到几百KB之间。

3. **VP9：** VP9 是谷歌开发的开放式视频编码标准，一帧的大小可能与 H.264 相似。

### 音频帧：

1. **AAC：** 对于 AAC 音频编码，一帧的大小通常在几十到几百字节之间，具体取决于采样率、声道数和压缩比例。

2. **MP3：** MP3 是一种较为老旧但仍然常用的音频编码标准，一帧的大小通常在几十到几百字节之间。

3. **PCM（脉冲编码调制）：** PCM 是一种未压缩的音频格式，一帧的大小取决于采样率、位深度和声道数。例如，对于 16 位深度的立体声音频，一帧的大小为 4 字节 * 采样率 * 声道数。

需要注意的是，这里提到的大小范围仅供参考，具体情况可能会有所不同。在实际应用中，你可以通过查看音视频文件的属性或使用专业的工具来获取更准确的信息。

## 码率

码率：编码器每秒编出的数据大小，单位是kbps，视频文件在单位时间内使用的数据流量，也叫码流率。码率越大，说明单位时间内取样率越大，数据流精度就越高。

码率影响的是**视频清晰度**：码率越大，视频画面越清晰画质越高。

码率不是越大越好，它是把每秒显示的图片进行压缩后的数据量。影响体积，与体积成正比，码率越大，体积越大;码率越小，体积越小。(体积=码率×时间)

## 分辨率

分辨率：单位英寸中所包含的像素点数； VGA：Video Graphics Array（视频图像分辨率）矩形图片的尺寸，即长度和宽度

分辨率影响**视频图像的大小**，与视频图像大小成正比：视频分辨率越高，图像越大，对应的视频文件本身大小也会越大

如果限定一个码率，比如800kbps，那么分辨率越高就会让编码器越 “为难" ，可以想象，它必须拆东墙补西墙，通过减少色彩信息或者引入马赛克这种“鱼目混珠”的手段来承载足够多的像素点。所以，同样的是2G的一个电影文件，1080p画质的版本可能不如720p画质的版本看起来更清晰。

## 联系

> 总结：视频分辨率会影响清晰度，视频帧速率会影响流畅度，码率会影响视频的清晰度和流畅度。

码率单位kbps，kb每秒，意思是这一秒包含了多少数据量。更大的分辨率，更复杂的色彩明暗关系，更快速的像素变化，都会需要更大的码率来容纳。如果码率数据量达不到这些要求，就会降低压制的质量，一般会表现为很明显的马赛克。

流畅的话，码率过高导致播放时电脑性能跟不上，也可能造成卡顿延迟。

**码率：**如果为10Mb/s，代表1秒钟有10M bit的视频数据，对于YUV422格式的1080P视频而言，一帧图像是 1920x1080x2x8/1024/1024 = 31.64Mbit，1秒钟30帧图像的话，则有949.2Mb/s，可见其数据量之大，不压缩根本无法网上传播，所以一定要经过视频压缩处理，不要以为1080P的视频就一定是高清的，清晰度还跟视频码率密切相关，对于1080P的视频而言，蓝光视频的码率是20Mb/s，一般下载的视频码率大都是10Mb/s，一些IPCamera/无人机的码率是2～8Mb/s，而很多视频网站的码率甚至低于5M/s，其实有时还不如高码率的720P清晰。

**帧率：**影 画面流畅度，与画面流畅度成正比：帧率越大，画面越流畅；帧率越小，画面越有跳动感。

如果码率为变量，则帧率也会影响体积，帧率越高，每秒钟经过的画面越多，需要的码率也越高，体积也越大。帧率就是在1秒钟时间里传输的图片的帧数，也可以理解为图形处理器每秒钟能够刷新几次。

**分辨率：**影响图像大小，与图像大小成正比：分辨率越高，图像越大；分辨率越低，图像越小。

**清晰度：所谓“清晰”，是指画面细腻，没有马赛克，并不是分辨率越高图像就越清晰**

在码率一定的情况下，分辨率与清晰度成反比关系：分辨率越高，图像越不清晰，分辨率越低，图像越清晰。

在分辨率一定的情况下，码率与清晰度成正比关系，码率越高，图像越清晰；码率越低，图像越不清晰。

**帧率X分辨率**=压缩前的每秒数据量(单位应该是若千个字节)―压缩比=压缩前的每秒数据量/码率 (对于同一个视频源并采用同—种视频编码算法，则:压缩比越高，画面质量越差。)

如果不做码率大小上的限制，那么分辨率越高，画质越细腻；帧率越高，视频也越流畅，但相应的码率也会很大，因为每秒钟需要用更多的数据来承载较高的清晰度和流畅度。这对云服务厂商而言这是好事（收入跟流量呈正比），但可能意味着更多的费用开支。



# 一 ffmpeg命令行 

## 1.1 流程

| type  | 原始        | 压缩编码              |      |
| ----- | ----------- | --------------------- | ---- |
| video | YUV         | H.264(AVC),  H.265    |      |
| audio | PCM         | MP3，AAC              |      |
| 封装  | H.264 + AAC | FLV(flash video), MP4 |      |







![image-20231107234312568](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231107234312568.png)

## 1.2 分类查询

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231108000351355.png" alt="image-20231108000351355" style="zoom: 80%;" />

```shell
// 查看某个encoders的全名
ffmpeg -encoders | findstr x264

// 查看具体分类支持的参数(去掉s)
ffmpeg -h muxer=flx

// 分页查看
ffmpeg -filters | more

// 查看像素格式
ffmpeg -pix_fmts
FLAGS NAME 类型            NB_COMPONENTS （类型数）BITS_PER_PIXEL（总共bit）
-----
IO... yuv420p                3            12
```

## 1.3 播放控制

![image-20231108002634398](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231108002634398.png)

## 1.4 常用参数

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231108123324549.png" alt="image-20231108123324549" style="zoom:80%;" />

## 1.5 常见命令

### 1.5.0 ffplay播放格式命令

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231108135451217.png" alt="image-20231108135451217" style="zoom:67%;" />

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231108135506010.png" alt="image-20231108135506010" style="zoom:67%;" />

### 1.5.1 提取音视频

* `test.mp4`,默认封装：`AVC + AAC`

```shell
// 保留封装格式
// 提取音频
ffmpeg -i test.mp4 -acodec copy -vn audio.mp4

// 提取视频
ffmpeg -i test.mp4 -vcodec copy -an video.mp4
```

```shell
// 单独提取
// 提取音频，保留编码格式
ffmpeg -i test.mp4 -acodec copy -vn audio.aac
// 修改音频编码，指定编码器
ffmpeg -i test.mp4 -acodec libmp3lame -vn audio.mp3

// 提取视频，保留编码格式
ffmpeg -i test.mp4 -vcodec copy -an video.h264
// 修改视频编码格式，指定编码器
ffmpeg -i test.mp4 -vcodec libx265 -an video.h265
```

### 1.5.2 提取封装前像素

* video

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231108135221273.png" alt="image-20231108135221273" style="zoom: 67%;" />

------------

* audio

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231108135313894.png" alt="image-20231108135313894" style="zoom:67%;" />



### 1.5.3 修改封装格式

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231108195507019.png" alt="image-20231108195507019" style="zoom: 67%;" />

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231108200147065.png" alt="image-20231108200147065" style="zoom:67%;" />

### 1.5.4 视频时长裁剪合并

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231108202825470.png" alt="image-20231108202825470" style="zoom: 50%;" />

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231108202356220.png" alt="image-20231108202356220" style="zoom:67%;" />

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231108202418093.png" alt="image-20231108202418093" style="zoom:67%;" />

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231108202711178.png" alt="image-20231108202711178" style="zoom:67%;" />

### 1.5.5 视频转图片，gif

```cpp
// 从第5s开始，的后10s内，每s随机抽取5帧，分辨率未720x360,一共10x5=50张图片
//文件名：haha01.jpg, haha02.jpg, haha03.jpg ......
ffmpeg -i test.mp4 -ss 5 -t 10 -r 5 -s 720x360 haha%03d.jpg
    
// 生成1张图片
ffmpeg -i t.mp4 -t 1 -r 1 one.jpg
```

```cpp
// 图片生成视频
// -f注明是图片类型
// 将所有图片以每s25帧的方式变为视频
ffmpeg -f image2 -i haha%03d.jpg -r 25 video.mp4
```



```cpp
// 生成gif
// 从第5s的后10s内，每s随机抽取5帧，制作gif，gif的时间仍为10s
ffmpeg -i t.mp4 --ss 5 -t 10 -r 5 -s 720x360 gif.gif
```

```cpp
// gif变视频
ffmpeg -f gif -i image2.gif image2.mp
```



### 1.5.6 filter过滤器







### 1.5 推拉流

![image-20231110110841060](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231110110841060.png)

# 二 SDL

[学习文档](F:\学习视频\音视频\资料\06-SDL音视频渲染实战\SDL音视频渲染实战.pdf)

## 2.1 导入sdl库

调用SDL库,[库位置](D:\videoTools\other_lib\SDL2-2.28.5)

* 1 执行hello world生成 buld---debug目录

* 2 将库文件放在qt项目目录
* 3 在.pro文件加入头文件和lib库文件路径

```c
win32{
INCLUDEPATH += $$PWD/SDL2-2.28.5/include
LIBS += $$PWD/SDL2-2.28.5/lib/x86/SDL2.lib
}

```

* 4 将lib下的dll动态库文件放入build--debug目录下
* 5 若报错，删除build-debug下的debug release目录，重新执行

```c
#include <stdio.h>
#include <SDL.h>

#undef main
int main()
{
    printf("Hello World!\n");

    SDL_Window *window = NULL;

    SDL_Init(SDL_INIT_VIDEO);

    window = SDL_CreateWindow("Basic Window",
                              SDL_WINDOWPOS_UNDEFINED, // 默认生成窗口位置：中心
                              SDL_WINDOWPOS_UNDEFINED,
                              640,						// 窗口大小
                              480,
                              SDL_WINDOW_OPENGL | SDL_WINDOW_RESIZABLE);
    if(!window){
        printf("can't create window:%s\n", SDL_GetError());
        return 1;

    }

    SDL_Delay(10000); // 延迟10s

    // 释放资源
    SDL_DestroyWindow(window);
    SDL_Quit();


    return 0;
}

```



## 2.2 着色器、纹理

```c
#include <stdio.h>
#include <SDL.h>
#undef main
int main()
{
    int run = 1;
    SDL_Window *window = NULL;
    SDL_Renderer *renderer = NULL;
    SDL_Texture *texture = NULL;
    SDL_Rect rect; // 长方形，原点在左上角
    rect.w = 50;    //方块大小
    rect.h = 50;

    SDL_Init(SDL_INIT_VIDEO);//初始化函数,可以确定希望激活的子系统

    window = SDL_CreateWindow("2 Window",
                              SDL_WINDOWPOS_UNDEFINED,
                              SDL_WINDOWPOS_UNDEFINED,
                              640,
                              480,
                              SDL_WINDOW_OPENGL | SDL_WINDOW_RESIZABLE);// 创建窗口

    if (!window)
    {
        return -1;
    }
    renderer = SDL_CreateRenderer(window, -1, 0);//基于窗口创建渲染器
    if (!renderer)
    {
        return -1;
    }

    texture = SDL_CreateTexture(renderer,
                                   SDL_PIXELFORMAT_RGBA8888,
                                   SDL_TEXTUREACCESS_TARGET,
                                   640,
                                   480); //创建纹理

    if (!texture)
    {
        return -1;
    }

    int show_count = 0;
    while (run)
    {
        rect.x = rand() % 600;
        rect.y = rand() % 400;

        SDL_SetRenderTarget(renderer, texture); // 设置渲染目标为纹理
        SDL_SetRenderDrawColor(renderer, 255, 0, 0, 255); // 纹理背景为黑色
        SDL_RenderClear(renderer); //清屏

        SDL_RenderDrawRect(renderer, &rect); //绘制一个长方形
        SDL_SetRenderDrawColor(renderer, 0, 255, 255, 255); //长方形为白色
        SDL_RenderFillRect(renderer, &rect);

        SDL_SetRenderTarget(renderer, NULL); //恢复默认，渲染目标为窗口
        SDL_RenderCopy(renderer, texture, NULL, NULL); //拷贝纹理到CPU

        SDL_RenderPresent(renderer); //输出到目标窗口上
        SDL_Delay(300);
        if(show_count++ > 30)
        {
            run = 0;        // 不跑了
        }
    }

    SDL_DestroyTexture(texture);
    SDL_DestroyRenderer(renderer);
    SDL_DestroyWindow(window); //销毁窗口
    SDL_Quit();
    return 0;
}

```

## 2.3 事件

```c
#include <SDL.h>
#include <stdio.h>
#define FF_QUIT_EVENT    (SDL_USEREVENT + 2) // 用户自定义事件
#undef main
int main(int argc, char* argv[])
{
    SDL_Window *window = NULL;              // Declare a pointer
    SDL_Renderer *renderer = NULL;

    SDL_Init(SDL_INIT_VIDEO);               // Initialize SDL2

    // Create an application window with the following settings:
    window = SDL_CreateWindow(
                "An SDL2 window",                  // window title
                SDL_WINDOWPOS_UNDEFINED,           // initial x position
                SDL_WINDOWPOS_UNDEFINED,           // initial y position
                640,                               // width, in pixels
                480,                               // height, in pixels
                SDL_WINDOW_SHOWN | SDL_WINDOW_BORDERLESS// flags - see below
                );

    // Check that the window was successfully created
    if (window == NULL)
    {
        // In the case that the window could not be made...
        printf("Could not create window: %s\n", SDL_GetError());
        return 1;
    }

    /* We must call SDL_CreateRenderer in order for draw calls to affect this window. */
    renderer = SDL_CreateRenderer(window, -1, 0);

    /* Select the color for drawing. It is set to red here. */
    SDL_SetRenderDrawColor(renderer, 255, 0, 0, 255);

    /* Clear the entire screen to our selected color. */
    SDL_RenderClear(renderer);

    /* Up until now everything was drawn behind the scenes.
       This will show the new, red contents of the window. */
    SDL_RenderPresent(renderer);

    SDL_Event event;
    int b_exit = 0;
    for (;;)
    {
        SDL_WaitEvent(&event);
        switch (event.type)
        {
        case SDL_KEYDOWN:	/* 键盘事件 */
            switch (event.key.keysym.sym)
            {
            case SDLK_a:
                printf("key down a\n");
                break;
            case SDLK_s:
                printf("key down s\n");
                break;
            case SDLK_d:
                printf("key down d\n");
                break;
            case SDLK_q:
                printf("key down q and push quit event\n");
                SDL_Event event_q;
                event_q.type = FF_QUIT_EVENT;
                SDL_PushEvent(&event_q);
                break;
            default:
                printf("key down 0x%x\n", event.key.keysym.sym);
                break;
            }
            break;
        case SDL_MOUSEBUTTONDOWN:			/* 鼠标按下事件 */
            if (event.button.button == SDL_BUTTON_LEFT)
            {
                printf("mouse down left\n");
            }
            else if(event.button.button == SDL_BUTTON_RIGHT)
            {
                printf("mouse down right\n");
            }
            else
            {
                printf("mouse down %d\n", event.button.button);
            }
            break;
        case SDL_MOUSEMOTION:		/* 鼠标移动事件 */
            printf("mouse movie (%d,%d)\n", event.button.x, event.button.y);
            break;
        case FF_QUIT_EVENT:
            printf("receive quit event\n");
            b_exit = 1;
            break;
        }
        if(b_exit)
            break;
    }

    //destory renderer
    if (renderer)
        SDL_DestroyRenderer(renderer);

    // Close and destroy the window
    if (window)
        SDL_DestroyWindow(window);

    // Clean up
    SDL_Quit();
    return 0;
}


```

## 2.4 多线程

![image-20240109151058437](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20240109151058437.png)

`SDL_CreateThread` 是 Simple DirectMedia Layer (SDL) 库中用于创建新线程的函数。通过 `SDL_CreateThread`，你可以创建一个新的线程并在其中运行指定的函数。

下面是 `SDL_CreateThread` 函数的基本用法：

```c
SDL_Thread* SDL_CreateThread(SDL_ThreadFunction fn, const char* name, void* data);
```

其中：

- `fn`: 这是一个函数指针，指向你希望在线程中执行的函数。此函数应该接受一个 `void*` 类型的参数，并返回一个 `int` 类型的值。
- `name`: 这是一个字符串，用于为线程提供一个名称。这在调试或日志记录时可能很有用。
- `data`: 这是一个 `void*` 类型的数据指针，你可以传递任何数据给线程函数。

----------------------------------

`SDL_WaitThread` 是 Simple DirectMedia Layer (SDL) 库中的一个函数，它的主要作用是等待一个线程完成其执行。

当你创建一个线程并启动它来执行某些任务后，可能会想要等待这个线程执行完毕后再继续执行其他操作。为了实现这一点，你可以使用 `SDL_WaitThread` 函数。

以下是 `SDL_WaitThread` 的基本用法：

```c
int SDL_WaitThread(SDL_Thread *thread, int *status);
```

- `thread`: 这是指向你想要等待的线程的指针。
- `status`: 这是一个整数指针，它用于存储线程的返回值。当线程完成执行后，它的返回值将被存储在 `status` 指向的位置。

使用 `SDL_WaitThread` 时，调用线程将会阻塞，直到指定的线程完成执行。

简而言之，`SDL_WaitThread` 函数允许你等待一个特定的线程完成其任务，并获取该线程的返回值。

--------------------



下面是一个简单的示例，展示如何使用 `SDL_CreateThread` 创建一个新的线程：

```c
#include <SDL2/SDL.h>
#include <stdio.h>

// 线程函数
int threadFunction(void* data) {
    printf("Thread started with data: %s\n", (char*)data);
    SDL_Delay(5000);  // 延迟5秒
    printf("Thread finished.\n");
    return 0;
}

int main(int argc, char* argv[]) {
    // 初始化 SDL
    if (SDL_Init(SDL_INIT_VIDEO | SDL_INIT_TIMER) != 0) {
        printf("SDL_Init error: %s\n", SDL_GetError());
        return 1;
    }

    // 创建线程
    SDL_Thread* thread = SDL_CreateThread(threadFunction, "MyThread", (void*)"Hello from main!");

    if (thread == NULL) {
        printf("SDL_CreateThread error: %s\n", SDL_GetError());
    } else {
        // 等待线程结束
        int threadReturnValue;
        SDL_WaitThread(thread, &threadReturnValue);
        printf("Thread returned value: %d\n", threadReturnValue);
    }

    // 清理 SDL
    SDL_Quit();
    return 0;
}
```

在上面的示例中，`threadFunction` 函数是线程将要执行的函数。当你运行上面的程序时，它将创建一个新的线程并输出相关信息。

# 三 ffmpeg库

## 3.0 数据结构

[学习文档](E:\typora索引文件\ffmpeg\06-01-FFmpeg编程入门.pdf)

![image-20231113214730008](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231113214730008.png)

| 数据结构          | 用途                                              |
| ----------------- | ------------------------------------------------- |
| AVIOContext       | 自定义读取文件                                    |
| AVFormatContext   | 封装格式上下文，记录打开一个封装音频的所有信息    |
| AVInputFormat     | 解封装格式信息                                    |
| AVoutputFormat    | 封装格式信息                                      |
| AVStream          | 标识视频/音频流                                   |
|                   |                                                   |
| AVCodecContext    | 编码器格式上下文                                  |
| AVCodec           | 编码器信息                                        |
| AVSampleFormat    | 枚举类型，包含编码器所支持的所有的音频采样格式    |
|                   |                                                   |
| AVPacket          | 压缩数据包（AAC/AVC）                             |
| AVFrame           | 原始数据包（PCM/YUV）                             |
|                   |                                                   |
| AVCodecParameters |                                                   |
| AVBSFContext      | 比特率过滤器上下文                                |
| AVBitStreamFilter | 比特流过滤器，用于从MP4中分离H264文件             |
|                   |                                                   |
| AVRational        | 分数形式，常用 time_base、sample_rate等属性的表示 |





----

## 3.1 AVFormatContext

`AVFormatContext` 和 `AVPacket` 是 FFmpeg 中用于处理音视频数据的两个关键结构体。

1. **AVFormatContext:**
   - `AVFormatContext` 主要用于存储整个音视频文件的格式相关信息，包括文件的封装格式、音视频流的详细信息、文件的全局元数据等。其主要字段包括：
     - `streams`: 一个指针数组，包含了文件中每个音视频流的信息，每个元素是一个 `AVStream` 结构体。
     - `nb_streams`: 表示流的个数。
     - `duration`: 表示文件的总时长。
     - `metadata`: 包含文件的全局元数据，如标题、作者等信息。

   通过 `AVFormatContext`，你可以访问和操作文件级别的信息，例如确定文件中有多少个音视频流、获取文件的时长等。

2. **AVPacket:**
   - `AVPacket` 用于存储编解码器产生的压缩后的音视频数据（帧）。
   
   通过 `AVPacket`，你可以访问和操作音视频数据的压缩包，其中包括数据本身以及与播放顺序和时间轴相关的时间戳信息。

总的来说，`AVFormatContext` 存储了整个文件的格式和元数据信息，而 `AVPacket` 存储了压缩后的音视频数据以及与时间相关的信息。这两个结构体在 FFmpeg 中一起协同工作，用于解析和处理音视频文件。

### 3.1.1 内置属性

* `保存一个完整封装的音频（MP4，FLV），将其压缩的音频流和视频流分开`

| 属性                                 | 解释                                                      |
| ------------------------------------ | --------------------------------------------------------- |
| `nb_streans`                         | `流个数`                                                  |
| `streams`                            | `遍历循环AVStreams[]数组，得到准确的视频流/音频流的index` |
| `duration`                           | `微秒，除以100w变为秒，duration / AV_TIME_BASE`           |
| `start_time`                         | `开始时间，与seek操作有关`                                |
| `url`                                | `媒体路径`                                                |
| `bit_rate`                           | `码率`                                                    |
| `metadata`                           | `包含文件的全局元数据，如标题、作者等信息`                |
| `AVIOContext *pb`                    | `用于存放通过IO打开文件的上下文信息`                      |
| `AVInputFormat *iformat`             | `demuxing only`                                           |
| `AVOutputFormat *oformat`            | `muxing only，包含封装时所需要的信息`                     |
|                                      |                                                           |
| `AVIOInterruptCB interrupt_callback` | 回调函数结构体，具体包含`指向回调函数的指针`              |
|                                      |                                                           |

在 FFmpeg（一个开源的多媒体处理工具库）中，`AVInputFormat` 和 `AVOutputFormat` 是两个结构体，分别用于处理输入（读取）和输出（写入）多媒体数据的格式信息。这两个结构体提供了关于媒体文件格式的元数据，包括文件格式名称、文件扩展名、支持的编解码器等信息。

1. **`AVInputFormat`**：
   - 用于处理输入媒体数据，即读取媒体文件。
   - 包含有关输入格式的信息，如文件格式名称、扩展名、支持的封装器格式等。
   - 提供一组回调函数，用于实现打开、读取帧、关闭等操作，以便处理特定格式的媒体文件。

2. **`AVOutputFormat`**：
   - 用于处理输出媒体数据，即写入媒体文件。
   - 包含有关输出格式的信息，如文件格式名称、扩展名、支持的封装器格式等。
   - 提供一组回调函数，用于实现打开、写入帧、关闭等操作，以便生成特定格式的媒体文件。

这两个结构体的作用在于提供统一的接口，使得FFmpeg能够处理各种不同格式的媒体文件，而无需在主要的处理逻辑中关心底层的文件格式细节。在使用FFmpeg进行媒体处理时，你可以通过这些结构体来注册和识别各种不同的输入和输出格式，以实现对不同媒体文件的读写操作。



```cpp
typedef struct AVFormatContext {
    const AVClass *av_class;  // 类型信息
    struct AVInputFormat *iformat;  // 输入格式
    struct AVOutputFormat *oformat;  // 输出格式
    void *priv_data;  // 私有数据
    AVIOContext *pb;  // I/O 上下文
    int ctx_flags;  // 上下文标志
    unsigned int nb_streams;  // 流的数量
    AVStream **streams;  // 流数组
    char filename[1024];  // 文件名或流的 URL
    int64_t start_time;  // 媒体流的起始时间
    int64_t duration;  // 媒体流的总时长
    int bit_rate;  // 比特率
    unsigned int packet_size;  // 数据包的大小
    int max_delay;  // 最大延迟
    int flags;  // 标志
    int probesize;  // 探测大小
    int max_analyze_duration;  // 最大分析时长
    const uint8_t *key;  // 解密密钥
    int keylen;  // 解密密钥长度
    unsigned int nb_programs;  // 节目数量
    AVProgram **programs;  // 节目数组
    int video_codec_id;  // 视频编解码器 ID
    int audio_codec_id;  // 音频编解码器 ID
    int subtitle_codec_id;  // 字幕编解码器 ID
    int max_index_size;  // 最大索引大小
    int max_picture_buffer;  // 最大图片缓冲
    int nb_chapters;  // 章节数量
    AVChapter **chapters;  // 章节数组
    AVDictionary *metadata;  // 元数据
    int64_t start_time_realtime;  // 实际起始时间
    int fps_probe_size;  // 探测帧率的大小
    int error_recognition;  // 错误识别
    AVIOInterruptCB interrupt_callback;  // 中断回调
    int debug;  // 调试信息
    int64_t max_interleave_delta;  // 最大交叉差值
    int strict_std_compliance;  // 严格标准兼容性
    int event_flags;  // 事件标志
    int max_ts_probe;  // 最大时间戳探测
    int avoid_negative_ts;  // 避免负时间戳
    int ts_id;  // 时间戳 ID
    int audio_preload;  // 音频预加载
    int max_chunk_duration;  // 最大块时长
    int max_chunk_size;  // 最大块大小
    int use_wallclock_as_timestamps;  // 使用壁钟作为时间戳
    int avio_flags;  // I/O 标志
    int duration_estimation_method;  // 时长估算方法
    int skip_initial_bytes;  // 跳过初始字节
    int correct_ts_overflow;  // 修正时间戳溢出
    int seek2any;  // 允许在任何地方定位
    int flush_packets;  // 刷新数据包
    int probe_score;  // 探测分数
    int format_probesize;  // 格式探测大小
    AVCodecParameters *audio_codecpar;  // 音频编解码器参数
    AVCodecParameters *video_codecpar;  // 视频编解码器参数
    AVCodecParameters *subtitle_codecpar;  // 字幕编解码器参数
    AVFormatInternal *internal;  // 内部数据结构
    int io_repositioned;  // I/O 重定位
    AVCodec *video_codec;  // 视频编解码器
    AVCodec *audio_codec;  // 音频编解码器
    AVCodec *subtitle_codec;  // 字幕编解码器
    AVCodec *data_codec;  // 数据编解码器
    AVOpenCallback open_cb;  // 打开回调
    AVReadHeaderCallback read_header;  // 读取头部回调
    AVReadPacketCallback read_packet;  // 读取数据包回调
    AVReadCloseCallback read_close;  // 读取关闭回调
    AVReadPauseCallback read_pause;  // 读取暂停回调
    AVReadPlayCallback read_play;  // 读取播放回调
    AVSeekCallback seek2;  // 定位回调
    AVWriteHeaderCallback write_header;  // 写入头部回调
    AVWritePacketCallback write_packet;  // 写入数据包回调
    AVWriteTrailerCallback write_trailer;  // 写入尾部回调
    int64_t pb_pos;  // I/O 位置
    int64_t pb_duration;  // I/O 时长
    AVPacketList *packet_buffer;  // 数据包缓冲
    AVPacketList *packet_buffer_end;  // 数据包缓冲尾部
    int64_t data_offset;  // 数据偏移
    int raw_packet_buffer_size;  // 原始数据包缓冲大小
    AVPacketList *raw_packet_buffer;  // 原始数据包缓冲
    AVPacketList *raw_packet_buffer_end;  // 原始数据包缓冲尾部
    AVPacketList *parse_queue;  // 解析队列
    AVPacketList *parse_queue_end;  // 解析队列尾部
    int raw_packet_buffer_remaining_size;  // 原始数据包缓冲剩余大小
    AVIndexEntry *index_entries;  // 索引条目
    int nb_index_entries;  // 索引条目数量
    unsigned int index_entries_allocated_size;  // 分配的索引条目大小
    int init_range;  // 初始化范围
    int update_initial_durations_done;  // 更新初始时长标志
    int fps_probe_failed;  // 帧率探测失败标志
    int64_t fps_probe_size;  // 帧率探测大小
    int64_t fps_probe_count;  // 帧率探测数量
    int64_t last_duration;  // 上一个时长
    int64_t last_dts;  // 上一个 DTS
    int probe_packets;  // 探测数据包
    int codec_whitelist_depth;  // 编解码器白名单深度
    AVPacket *flush_packets;  // 刷新数据包
    AVPacket *flush_packets_end;  // 刷新数据包尾部
    int64_t first_dts;  // 第一个 DTS
    int probesize2;  // 第二探测大小
    int max_analyze_duration2;  // 第二最大分析时长
    const char *protocol_whitelist;  // 协议白名单
    int io_flags;  // I/O 标志
    int event_flags;  // 事件标志
    int max_ts_probe2;  // 第二最大时间戳探测
    int max_size;  // 最大大小
    int use_wallclock_as_timestamps;  // 使用壁钟作为时间戳
    int avio_flags;  // I/O 标志
    int duration_estimation_method;  // 时长估算方法
    int skip_initial_bytes;  // 跳过初始字节
    int correct_ts_overflow;  // 修正时间戳溢出
    int seek2any;  // 允许在任何地方定位
    int flush_packets;  // 刷新数据包
    int probe_score;  // 探测分数
    int format_probesize;  // 格式探测大小
    AVCodecParameters *audio_codecpar;  // 音频编解码器参数
    AVCodecParameters *video_codecpar;  // 视频编解码器参数
    AVCodecParameters *subtitle_codecpar;  // 字幕编解码器参数
    AVFormatInternal *internal;  // 内部数据结构
    int io_repositioned;  // I/O 重定位
    AVCodec *video_codec;  // 视频编解码器
    AVCodec *audio_codec;  // 音频编解码器
    AVCodec *subtitle_codec;  // 字幕编解码器
    AVCodec *data_codec;  // 数据编解码器
    AVOpenCallback open_cb;  // 打开回调
    AVReadHeaderCallback read_header;  // 读取头部回调
    AVReadPacketCallback read_packet;  // 读取数据包回调
    AVReadCloseCallback read_close;  // 读取关闭回调
    AVReadPauseCallback read_pause;  // 读取暂停回调
    AVReadPlayCallback read_play;  // 读取播放回调
    AVSeekCallback seek2;  // 定位回调
    AVWriteHeaderCallback write_header;  // 写入头部回调
    AVWritePacketCallback write_packet;  // 写入数据包回调
    AVWriteTrailerCallback write_trailer;  // 写入尾部回调
    int64_t pb_pos;  // I/O 位置
    int64_t pb_duration;  // I/O 时长
    AVPacketList *packet_buffer;  // 数据包缓冲
    AVPacketList *packet_buffer_end;  // 数据包缓冲尾部
    int64_t data_offset;  // 数据偏移
    int raw_packet_buffer_size;  // 原始数据包缓冲大小
    AVPacketList *raw_packet_buffer;  // 原始数据包缓冲
    AVPacketList *raw_packet_buffer_end;  // 原始数据包缓冲尾部
    AVPacketList *parse_queue;  // 解析队列
    AVPacketList *parse_queue_end;  // 解析队列尾部
    int raw_packet_buffer_remaining_size;  // 原始数据包缓冲剩余大小
    AVIndexEntry *index_entries;  // 索引条目
    int nb_index_entries;  // 索引条目数量
    unsigned int index_entries_allocated_size;  // 分配的索引条目大小
    int init_range;  // 初始化范围
    int update_initial_durations_done;  // 更新初始时长标志
    int fps_probe_failed;  // 帧率探测失败标志
    int64_t fps_probe_size;  // 帧率探测大小
    int64_t fps_probe_count;  // 帧率探测数量
    int64_t last_duration;  // 上一个时长
    int64_t last_dts;  // 上一个 DTS
    int probe_packets;  // 探测数据包
    int codec_whitelist_depth;  // 编解码器白名单深度
    AVPacket *flush_packets;  // 刷新数据包
    AVPacket *flush_packets_end;  // 刷新数据包尾部
    int64_t first_dts;  // 第一个 DTS
    int probesize2;  // 第二探测大小
    int max_analyze_duration2;  // 第二最大分析时长
    const char *protocol_whitelist;  // 协议白名单
    int io_flags;  // I/O 标志
    int event_flags;  // 事件标志
    int max_ts_probe2;  // 第二最大时间戳探测
    int max_size;  // 最大大小
} AVFormatContext;

```



### 3.1.2 接口函数

1 . MP4文件==输入的接口==函数

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231115161727817.png" alt="image-20231115161727817" style="zoom:67%;" />



```cpp
// 打开本地/网络视频文件
int avformat_open_input(AVFormatContext **ps, 
                        const char *url, 
                        ff_const59 AVInputFormat *fmt, 
                        AVDictionary **options);
// ps:格式上下文呢
// url：本地/网络 视频路径
// fmt：NULL
// options：NULL  配置项
int ret = avformat_open_input(&ifmt_ctx, in_filename, NULL, NULL);

// 断开与文件的关系，并将s置为NULL
void avformat_close_input(AVFormatContext **s);
```



```cpp
// 读取音/视频流信息到packet
// 通过packet的stream_id 判断是音/视频
int av_read_frame(AVFormatContext *s, AVPacket *pkt);

AVPacket *pkt = av_packet_alloc();
ret = av_read_frame(ifmt_ctx, pkt);
```

```cpp
// 查找音视频流的id
audio_index = av_find_best_stream(ifmt_ctx, AVMEDIA_TYPE_AUDIO, -1, -1, NULL, 0);

// 或者遍历streams[]查询
```

2. MP4文件==输出接口==函数

| 函数名                           | 作用                                       |
| :------------------------------- | ------------------------------------------ |
| `avformat_alloc_output_context2` | 为待输出的MP4文件创建绑定上下文            |
| `avio_open`                      | 为MP4创建io流上下文，绑定到fmt_ctx的pb指针 |
|                                  |                                            |
|                                  |                                            |

```cpp
int ret = avformat_alloc_output_context2(&fmt_ctx_, NULL, NULL,url);
```

```cpp
int ret = avio_open(&fmt_ctx_->pb, url_.c_str(), AVIO_FLAG_WRITE)
```



## 3.2 AVStream

### 3.2.1 对比

`AVPacket` 和 `AVStream` 是 FFmpeg 中两个不同的结构体，分别用于处理音视频数据的帧和表示音视频流的信息。它们在功能和用途上有不同的设计和定位，因此在使用时有各自的优势。

1. **AVPacket:**
   - `AVPacket` 结构体用于存储编解码器产生的压缩后的音视频数据，即压缩后的帧。它包含了压缩数据的指针、大小、时间戳等信息。
   - 主要用于在音视频的编码和解码过程中传递数据。在解封装（Demuxing）阶段，你会使用 `av_read_frame` 从文件或网络流中读取 `AVPacket`。
   - 在封装（Muxing）阶段，你将通过编码器得到的 `AVFrame` 编码为 `AVPacket`，再写入文件。

2. **AVStream:**
   - `AVStream` 结构体代表了媒体文件中的一个音视频流，包含了该流的详细信息，比如编解码器的参数、时基信息等。
   - 主要用于表示文件中的媒体流的属性，在文件的打开和读取阶段，你可以通过 `AVStream` 获取有关音视频流的信息，例如编码器类型、帧率、分辨率等。
   - 在解封装阶段，`AVStream` 用于表示文件中的不同音视频流，你可以根据需要处理这些流。

简而言之，`AVPacket` 主要用于处理音视频数据的帧，而 `AVStream` 主要用于表示音视频流的信息。它们分别服务于不同的层面，`AVPacket` 在数据处理阶段更为重要，而 `AVStream` 在文件的元数据表示和读取阶段更为关键。

虽然你可以使用 `AVStream` 来间接访问 `AVPacket`，但直接使用 `AVPacket` 更直观，因为它包含了处理帧数据所需的所有信息。在处理音视频数据的过程中，通常会频繁使用 `AVPacket` 来传递、处理和释放数据。
### 3.2.2 属性

| AVStream                      | 功能                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| `int index`                   | 流在avformatContext的索引序号，创建stream时候，由ffmpeg库自动分配，无需手动设置 |
| `id`                          |                                                              |
| `AVCodecContext *codec`       | 编码器上下文                                                 |
| `void *priv_data`             |                                                              |
| `AVRational time_base`        | 时间基                                                       |
| `int64_t duration`            | 流总长时间戳                                                 |
| `nt64_t nb_frames`            |                                                              |
| `AVCodecParameters *codecpar` | 编码器参数                                                   |
| `enum AVDiscard discard;`     | 丢弃策略，下方详细描述                                       |
| `int disposition`             | 标志位，如何处理这个stream，下方详细                         |

| AVStream->codecpar                                      | 功能                 |
| ------------------------------------------------------- | -------------------- |
| `AVMEDIA_TYPE_AUDIO == in_stream->codecpar->codec_type` | 音频                 |
|                                                         |                      |
| sample_rate                                             | 音编码器采样率，Hz   |
| format   （`AV_SAMPLE_FMT_FLTP`/ `AV_SAMPLE_FMT_S16P`） | 音频采样格式         |
| channels                                                | 音频信道数目         |
| codec_id （`AV_CODEC_ID_AAC`\ `AV_CODEC_ID_MP3`）       | 音频压缩编码格式     |
| profile                                                 | 用于配置adts头部信息 |
|                                                         |                      |
| `AVMEDIA_TYPE_VIDEO == in_stream->codecpar->codec_type` | 视频                 |
| codec_id （`AV_CODEC_ID_MPEG4`\ `AV_CODEC_ID_h264`）    | 视频压缩编码格式     |
| width height                                            | 视频宽高             |
|                                                         |                      |
|                                                         |                      |

* 在 FFmpeg 的 `AVStream` 结构体中，`enum AVDiscard discard` 参数用于指定何时丢弃某些特定的流数据。这个参数是用来控制解码过程中丢弃哪些数据的，从而在某些情况下提高性能或达到特定的应用需求。

  `enum AVDiscard` 定义了以下的枚举值：

  - **AVDISCARD_NONE**: 不丢弃任何数据。
  - **AVDISCARD_DEFAULT**: 使用编解码器的默认策略来决定是否丢弃数据。
  - **AVDISCARD_NONREF**: 丢弃所有非参考帧。
  - **AVDISCARD_BIDIR**: 丢弃双向预测（B 帧）。
  - **AVDISCARD_NONKEY**: 丢弃所有非关键帧。
  - **AVDISCARD_ALL**: 丢弃所有数据。

  1. **性能优化**：在某些应用场景中，例如仅进行预览或快速浏览时，可能不需要所有的帧数据。通过设置 `discard` 参数，可以选择性地丢弃某些帧，从而提高解码性能和播放效率。

  2. **特定需求**：某些应用或场景可能需要特定类型的数据，例如仅关键帧。通过设置 `discard` 参数，可以确保仅解码和使用特定类型的帧，以满足特定的应用需求。

  3. **控制解码器行为**：`discard` 参数允许开发者有选择地控制解码器的行为。例如，在某些网络条件不佳的情况下，可能希望丢弃某些帧以确保流畅的播放。

  总的来说，`AVStream` 结构体中的 `discard` 参数提供了一种机制，允许开发者控制解码过程中哪些流数据应该被丢弃。这为实现性能优化、满足特定应用需求或调整解码器行为提供了灵活性。

* 在FFmpeg中，`AVStream` 结构体用于表示媒体流（例如音频流、视频流）的相关信息。`AVStream` 结构体中的 `disposition` 属性用于描述该特定流的一些附加信息或标志，这些信息可以帮助应用程序决定如何处理或呈现这个流。

  `disposition` 属性是一个位掩码（bitmask），允许你设置或检查多个标志。以下是一些常见的 `disposition` 标志及其含义：

  1. **AV_DISPOSITION_DEFAULT**: 表示这是默认流。例如，当一个文件中有多个音频流时，这个标志可以用于标记哪个是默认的音频流。
  
  2. **AV_DISPOSITION_DUB**: 表示这是一个配音流。在某些情况下，一个媒体文件可能会有多个语言版本的音频，这个标志可以用于标记特定的音频流是某种配音版本。
  
  3. **AV_DISPOSITION_ORIGINAL**: 表示这是原始流。例如，当一个文件中有多个音频流时，这个标志可以用于标记哪个是原始录制的音频流。
  
  4. **AV_DISPOSITION_COMMENT**: 表示这是一个评论或注释流。这可以用于标记包含导演评论、字幕或其他相关信息的流。
  
  5. **AV_DISPOSITION_LYRICS**: 表示这是一个歌词流。在某些音频文件中，可能会包含歌词信息，这个标志可以用于标记这样的流。
  
  除了上述列举的标志之外，`disposition` 属性还可能包含其他的标志，具体取决于媒体流的类型和内容。
  
  总的来说，`disposition` 属性提供了关于媒体流的附加信息，这些信息可以帮助应用程序和工具更好地处理和解释媒体文件中的内容。当解码或处理媒体流时，了解这些标志可以帮助你做出更加明智的决策。
  
* `codec_type类型`

  ```cpp
  enum AVMediaType {
      AVMEDIA_TYPE_UNKNOWN = -1,  ///< 错误
      AVMEDIA_TYPE_VIDEO,
      AVMEDIA_TYPE_AUDIO,
      AVMEDIA_TYPE_DATA,          ///< Opaque data information usually continuous
      AVMEDIA_TYPE_SUBTITLE,      /// 字幕
      AVMEDIA_TYPE_ATTACHMENT,    /// 音频专辑封面图
      AVMEDIA_TYPE_NB             /// 媒体类型的数目
  };
  
  ```

* `codec_id类型`

  ```cpp
  // 音视频编码器类型
  enum AVCodecID {
      AV_CODEC_ID_NONE,
  
      /* video codecs */
      AV_CODEC_ID_MPEG1VIDEO,
      AV_CODEC_ID_MPEG2VIDEO, ///< preferred ID for MPEG-1/2 video decoding
      AV_CODEC_ID_H261,
      AV_CODEC_ID_H263,
      AV_CODEC_ID_RV10,
      .
      .
      .
  ```

* `format类型`

  ```cpp
  // 音频采样格式
  enum AVSampleFormat {
      AV_SAMPLE_FMT_NONE = -1,
      AV_SAMPLE_FMT_U8,          ///< unsigned 8 bits
      AV_SAMPLE_FMT_S16,         ///< signed 16 bits
      AV_SAMPLE_FMT_S32,         ///< signed 32 bits
      AV_SAMPLE_FMT_FLT,         ///< float
      AV_SAMPLE_FMT_DBL,         ///< double
  
      AV_SAMPLE_FMT_U8P,         ///< unsigned 8 bits, planar
      AV_SAMPLE_FMT_S16P,        ///< signed 16 bits, planar
      AV_SAMPLE_FMT_S32P,        ///< signed 32 bits, planar
      AV_SAMPLE_FMT_FLTP,        ///< float, planar
      AV_SAMPLE_FMT_DBLP,        ///< double, planar
      AV_SAMPLE_FMT_S64,         ///< signed 64 bits
      AV_SAMPLE_FMT_S64P,        ///< signed 64 bits, planar
  
      AV_SAMPLE_FMT_NB           ///< Number of sample formats. DO NOT USE if linking dynamically
  };
  ```

### 3.2.3 接口

```cpp
// 创建流
AVStream *st = avformat_new_stream(fmt_ctx_, NULL);
```

```cpp
// 从编码器拷贝参数信息
avcodec_parameters_from_context(st->codecpar, codec_ctx);
av_dump_format(fmt_ctx_, 0, url_.c_str(), 1);
```

```cpp
int av_find_best_stream(AVFormatContext *ic,
                        enum AVMediaType type, // vedio audio
                        int wanted_stream_nb,  // 选择的流索引
                        int related_stream,  // 相关的流索引，audio相关vedio， subtitile相关vedio或audio
                        AVCodec **decoder_ret,
                        int flags);
```

`av_find_best_stream` 是一个 FFmpeg 库中的函数，用于从给定的 `AVFormatContext`（表示媒体格式的上下文）中找到最佳的流。该函数可以用于选择最合适的流，例如，如果你想从一个容器（例如，MP4、MKV 或其他格式）中提取音频或视频流。

让我们逐步解析该函数的参数和功能：



1. **`int related_stream`**:
   - 这个参数表示相关的流索引。在某些情况下，一个流可能与另一个流有关联，例如，视频流可能有一个对应的音频流。这个参数允许你指定与要查找的流相关联的另一个流的索引。

2. **`AVCodec **decoder_ret`**:
   - 这是一个 `AVCodec` 结构体指针的指针，用于接收找到的解码器。这意味着，如果函数找到了一个合适的流，它将为你提供一个指向该流解码器的指针。

3. **`int flags`**:
   - 这是一个整数，用于设置或指定特定的标志。例如，你可以使用不同的标志来改变查找流的行为或优先级。

### 功能：
- `av_find_best_stream` 函数的主要目的是从给定的媒体格式上下文中查找与指定类型最匹配的最佳流。它会根据不同的标准（例如，流的质量、优先级等）来评估流，并返回最佳的流索引及其相应的解码器。

这个函数在视频和音频处理、转码、媒体播放器等应用中都非常有用，因为它允许开发者轻松地从复杂的媒体容器中提取所需的流。

### 3.2.4 示例

[关于打印stream时间的详细解释](#av_q2d )

```cpp
#include <stdio.h>
#include <libavformat/avformat.h>


int main(int argc, char **argv)
{
    //打开网络流。这里如果只需要读取本地媒体文件，不需要用到网络功能，可以不用加上这一句
//    avformat_network_init();

    const char *default_filename = "believe.mp4";

    char *in_filename = NULL;

    if(argv[1] == NULL)
    {
        in_filename = default_filename;
    }
    else
    {
        in_filename = argv[1];
    }
    printf("in_filename = %s\n", in_filename);

    //AVFormatContext是描述一个媒体文件或媒体流的构成和基本信息的结构体
    AVFormatContext *ifmt_ctx = NULL;           // 输入文件的demux

    int videoindex = -1;        // 视频索引
    int audioindex = -1;        // 音频索引


    // 打开文件，主要是探测协议类型，如果是网络文件则创建网络链接
    int ret = avformat_open_input(&ifmt_ctx, in_filename, NULL, NULL);
    if (ret < 0)  //如果打开媒体文件失败，打印失败原因
    {
        char buf[1024] = { 0 };
        av_strerror(ret, buf, sizeof(buf) - 1);
        printf("open %s failed:%s\n", in_filename, buf);
        goto failed;
    }

    // mp4不需要，flv需要？？？？？
    ret = avformat_find_stream_info(ifmt_ctx, NULL);
    if (ret < 0)  //如果打开媒体文件失败，打印失败原因
    {
        char buf[1024] = { 0 };
        av_strerror(ret, buf, sizeof(buf) - 1);
        printf("avformat_find_stream_info %s failed:%s\n", in_filename, buf);
        goto failed;
    }

    //打开媒体文件成功
    printf_s("\n==== av_dump_format in_filename:%s ===\n", in_filename);
    
    av_dump_format(ifmt_ctx, 0, in_filename, 0);
    printf_s("\n==== av_dump_format finish =======\n\n");
    
    // url: 调用avformat_open_input读取到的媒体文件的路径/名字
    printf("media name:%s\n", ifmt_ctx->url);
    
    // nb_streams: nb_streams媒体流数量
    printf("stream number:%d\n", ifmt_ctx->nb_streams);
    // bit_rate: 媒体文件的码率,单位为bps
    printf("media average ratio:%lldkbps\n",(int64_t)(ifmt_ctx->bit_rate/1024));
    // 时间
    int total_seconds, hour, minute, second;
    // duration: 媒体文件时长，单位微妙
    total_seconds = (ifmt_ctx->duration) / AV_TIME_BASE;  // 1000us = 1ms, 1000ms = 1秒
    hour = total_seconds / 3600;
    minute = (total_seconds % 3600) / 60;
    second = (total_seconds % 60);
    //通过上述运算，可以得到媒体文件的总时长
    printf("total duration: %02d:%02d:%02d\n", hour, minute, second);
    printf("\n");
    /*
     * 老版本通过遍历的方式读取媒体文件视频和音频的信息
     * 新版本的FFmpeg新增加了函数av_find_best_stream，也可以取得同样的效果
     */
    for (uint32_t i = 0; i < ifmt_ctx->nb_streams; i++)
    {
        AVStream *in_stream = ifmt_ctx->streams[i];// 音频流、视频流、字幕流
        //如果是音频流，则打印音频的信息
        if (AVMEDIA_TYPE_AUDIO == in_stream->codecpar->codec_type)
        {
            printf("----- Audio info:\n");
            // index: 每个流成分在ffmpeg解复用分析后都有唯一的index作为标识
            printf("index:%d\n", in_stream->index);
            // sample_rate: 音频编解码器的采样率，单位为Hz
            printf("samplerate:%dHz\n", in_stream->codecpar->sample_rate);
            // codecpar->format: 音频采样格式
            if (AV_SAMPLE_FMT_FLTP == in_stream->codecpar->format)
            {
                printf("sampleformat:AV_SAMPLE_FMT_FLTP\n");
            }
            else if (AV_SAMPLE_FMT_S16P == in_stream->codecpar->format)
            {
                printf("sampleformat:AV_SAMPLE_FMT_S16P\n");
            }
            // channels: 音频信道数目
            printf("channel number:%d\n", in_stream->codecpar->channels);
            // codec_id: 音频压缩编码格式
            if (AV_CODEC_ID_AAC == in_stream->codecpar->codec_id)
            {
                printf("audio codec:AAC\n");
            }
            else if (AV_CODEC_ID_MP3 == in_stream->codecpar->codec_id)
            {
                printf("audio codec:MP3\n");
            }
            else
            {
                printf("audio codec_id:%d\n", in_stream->codecpar->codec_id);
            }
            // 音频总时长，单位为秒。注意如果把单位放大为毫秒或者微妙，音频总时长跟视频总时长不一定相等的
            if(in_stream->duration != AV_NOPTS_VALUE)
            {
                int duration_audio = (in_stream->duration) * av_q2d(in_stream->time_base);
                //将音频总时长转换为时分秒的格式打印到控制台上
                printf("audio duration: %02d:%02d:%02d\n",
                       duration_audio / 3600, (duration_audio % 3600) / 60, (duration_audio % 60));
            }
            else
            {
                printf("audio duration unknown");
            }

            printf("\n");

            audioindex = i; // 获取音频的索引
        }
        else if (AVMEDIA_TYPE_VIDEO == in_stream->codecpar->codec_type)  //如果是视频流，则打印视频的信息
        {
            printf("----- Video info:\n");
            printf("index:%d\n", in_stream->index);
            // avg_frame_rate: 视频帧率,单位为fps，表示每秒出现多少帧
            printf("fps:%lffps\n", av_q2d(in_stream->avg_frame_rate));
            if (AV_CODEC_ID_MPEG4 == in_stream->codecpar->codec_id) //视频压缩编码格式
            {
                printf("video codec:MPEG4\n");
            }
            else if (AV_CODEC_ID_H264 == in_stream->codecpar->codec_id) //视频压缩编码格式
            {
                printf("video codec:H264\n");
            }
            else
            {
                printf("video codec_id:%d\n", in_stream->codecpar->codec_id);
            }
            // 视频帧宽度和帧高度
            printf("width:%d height:%d\n", in_stream->codecpar->width,
                   in_stream->codecpar->height);
            //视频总时长，单位为秒。注意如果把单位放大为毫秒或者微妙，音频总时长跟视频总时长不一定相等的
            if(in_stream->duration != AV_NOPTS_VALUE)
            {
                int duration_video = (in_stream->duration) * av_q2d(in_stream->time_base);
                printf("video duration: %02d:%02d:%02d\n",
                       duration_video / 3600,
                       (duration_video % 3600) / 60,
                       (duration_video % 60)); //将视频总时长转换为时分秒的格式打印到控制台上
            }
            else
            {
                printf("video duration unknown");
            }

            printf("\n");
            videoindex = i;
        }
    }

    AVPacket *pkt = av_packet_alloc();

    int pkt_count = 0;
    int print_max_count = 10;
    printf("\n-----av_read_frame start\n");
    while (1)
    {
        ret = av_read_frame(ifmt_ctx, pkt);
        if (ret < 0)
        {
            printf("av_read_frame end\n");
            break;
        }

        if(pkt_count++ < print_max_count)
        {
            if (pkt->stream_index == audioindex)
            {
                printf("audio pts: %lld\n", pkt->pts);
                printf("audio dts: %lld\n", pkt->dts);
                printf("audio size: %d\n", pkt->size);
                printf("audio pos: %lld\n", pkt->pos);
                printf("audio duration: %lf\n\n",
                       pkt->duration * av_q2d(ifmt_ctx->streams[audioindex]->time_base));
            }
            else if (pkt->stream_index == videoindex)
            {
                printf("video pts: %lld\n", pkt->pts);
                printf("video dts: %lld\n", pkt->dts);
                printf("video size: %d\n", pkt->size);
                printf("video pos: %lld\n", pkt->pos);
                printf("video duration: %lf\n\n",
                       pkt->duration * av_q2d(ifmt_ctx->streams[videoindex]->time_base));
            }
            else
            {
                printf("unknown stream_index:\n", pkt->stream_index);
            }
        }

        av_packet_unref(pkt);
    }

    if(pkt)
        av_packet_free(&pkt);
failed:
    if(ifmt_ctx)
        avformat_close_input(&ifmt_ctx);


    getchar(); //加上这一句，防止程序打印完信息马上退出
    return 0;
}

```



-------



## 3.3 内存模型

[学习文档](E:\typora索引文件\ffmpeg\06-02-FFmpeg内存模型.pdf)

------------

![image-20231112232723405](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231112232723405.png)

![image-20231112232931366](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231112232931366.png)

### 3.3.1 avpacket

| 属性                | 作用                                                     |
| ------------------- | -------------------------------------------------------- |
| `AVBufferRef *buf;` |                                                          |
| `int64_t pts;`      | 显示时间戳，单位为 time_base 时间基                      |
| `int64_t dts`       | 解码时间戳，单位为 time_base时间基                       |
| `int64_t duration;` | 视频帧的时长，单位 time_base时间基， 音频帧的samples数目 |
| `uint8_t *data;`    | 直接指向 `AVPacket` 中的压缩数据的内存位置               |
| `int size`          | packet的数据大小（字节大小）                             |
| `int stream_index`  | 该帧所属的 stream_id                                     |
|                     |                                                          |
| `int flags`         |                                                          |
| `int64_t pos`       | `当前packet在音视频文件中位置（偏移量）`                 |
|                     |                                                          |



![image-20231112233247117](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231112233247117.png)

`AVPacket *pkt = NULL;`

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231112162220811.png" alt="image-20231112162220811" style="zoom:50%;" />

`pkt = av_packet_alloc();` buf为0

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231112162415115.png" alt="image-20231112162415115" style="zoom:50%;" />

`ret = av_new_packet(pkt, MEM_ITEM_SIZE);`初始化buf为1

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231112162456034.png" alt="image-20231112162456034" style="zoom:50%;" />

`av_packet_free(&pkt);`释放所有内存，内调用`av_packet_unref`

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231112162528612.png" alt="image-20231112162528612" style="zoom:50%;" />

`av_init_packrt`：将buf置为0，再次调用`av_packet_free`造成内存泄漏，不要乱用

`av_packet_ref(pkt2, pkt)`: 是一个用于引用（引用计数）`AVPacket` 结构的函数。这个函数会增加 `AVPacket` 结构的引用计数，从而												允许多个地方引用同一个数据包而不复制其内容。这在并发处理和内存效率方面很有用

`av_packet_unref(pkt)`: 用于取消引用（减少引用计数）`AVPacket` 结构的函数。这个函数会减少 `AVPacket` 结构的引用计数，如果引												用计数变为零，将释放相关的内存。

`av_packet_move_ref`:将buff拷贝给另一个，原来buff置为0，内部调用`av_init_packet`



```cpp
// av_read_frame 函数用于从音视频文件中读取一帧数据，但它并不关心这一帧是音频还是视频。
// 该函数从文件中读取下一个压缩后的数据包（AVPacket），而文件中可能包含音频流和视频流，或者其他类型的流，取决于文件的内容。

要区分音频和视频，你需要检查 AVPacket 中的 stream_index 字段，该字段表示数据包所属的流的索引。在 AVFormatContext 结构体的 streams 数组中，通过 stream_index 可以获取到对应的流。
int av_read_frame(AVFormatContext *s, AVPacket *pkt);

AVPacket *pkt = av_packet_alloc();
ret = av_read_frame(ifmt_ctx, pkt);
```



### 3.3.2 avframe

#### 0 属性



[具体内容](#frame)

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231127114336862.png" alt="image-20231127114336862" style="zoom:67%;" />

| 属性                    | 作用                                                         |
| ----------------------- | ------------------------------------------------------------ |
| `data`                  | 指向解压后的数据                                             |
| `pts`                   | frame的显示顺序，解码阶段， 视频frame由==pkt_pts==赋值，音频frame采用==默认的pts并转换时间基== |
|                         |                                                              |
|                         |                                                              |
| 音频                    |                                                              |
| `sample_rate`           | 采样率                                                       |
| `formate`               | 采样格式                                                     |
| `channels`              | 通道数                                                       |
| `channel_layout`        |                                                              |
| `nb_samples`            | 采样数                                                       |
| `duration`              | 音频帧持续时间 = nb_samples / sample_rate                    |
| 视频                    |                                                              |
| `sample_rate`           | 解码：函数计算获取                                           |
| `pkt_pts`               | packet的显示顺序，由解码前的packet.pts赋值                   |
| `best_effort_timestamp` | 在没有具体的pts下，由解码器尽力估算的一个时间戳              |
| `format`                |                                                              |
| `weight x height`       |                                                              |
| `duration`              | 视频中持续时间 = 1 / sample_rate                             |
|                         |                                                              |
|                         |                                                              |
|                         |                                                              |

```cpp
// 获取视频帧率
AVRational av_guess_frame_rate(AVFormatContext *ctx, AVStream *stream, AVFrame *frame);

// 使用
AVRational frame_rate = av_guess_frame_rate(is->ic, is->video_st, NULL);
```



![image-20231112233258499](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231112233258499.png)

`av_frame_free` 和 `av_frame_unref` 都与 `AVFrame` 结构体（表示视频或音频帧）相关，但它们的作用和用途有所不同：

1. **av_frame_free**:（结构体 + 指针指向的内存）
   - `av_frame_free` 用于完全释放 `AVFrame` 结构体和其相关的所有缓冲区。这意味着它不仅会释放 `AVFrame` 结构体本身的内存，还会释放该结构体中引用的所有缓冲区（如图像数据、声音数据等）的内存。
   - 当你使用 `av_frame_alloc` 或其他方式为 `AVFrame` 结构体分配内存并且你希望完全释放这些内存时，你应该使用 `av_frame_free`。
   - 调用 `av_frame_free` 之后，`AVFrame` 结构体和其所有相关数据将不再可用。

2. **av_frame_unref**: （指针指向的内存）
   - `av_frame_unref` 用于解除 `AVFrame` 结构体中数据的引用，但不会释放 `AVFrame` 结构体本身的内存。这意味着它会清除 `AVFrame` 中指向数据（如图像、音频等）的引用，但 `AVFrame` 结构体本身仍然存在。
   - 当你想要重用 `AVFrame` 结构体，但首先需要清除它引用的数据时，应使用 `av_frame_unref`。
   - 调用 `av_frame_unref` 会使 `AVFrame` 结构体的数据引用指向 `NULL` 或初始状态，这样你可以为其分配新的数据或重新使用它。

简而言之，`av_frame_free` 用于完全释放 `AVFrame` 及其所有相关的数据内存，而 `av_frame_unref` 仅用于解除 `AVFrame` 对其引用的数据的引用，使其可以重新使用或重新分配新的数据。





#### 1 编码音频设置

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231129120117918.png" alt="image-20231129120117918" style="zoom:67%;" />

```cpp
AVFrame frame = av_frame_alloc();
if (!frame)
{
    fprintf(stderr, "Could not allocate audio frame\n");
    exit(1);
}
/* 每次送多少数据给编码器由：
     *  (1)frame_size(每帧单个通道的采样点数);
     *  (2)sample_fmt(采样点格式);
     *  (3)channel_layout(通道布局情况);
     * 3要素决定
     */

// 设置输入音频帧参数
frame->nb_samples     = codec_ctx->frame_size; // 帧大小
frame->format         = codec_ctx->sample_fmt;
frame->channel_layout = codec_ctx->channel_layout;
frame->channels = av_get_channel_layout_nb_channels(frame->channel_layout);
printf("frame nb_samples:%d\n", frame->nb_samples);
printf("frame sample_fmt:%d\n", frame->format);
printf("frame channel_layout:%lu\n\n", frame->channel_layout);
/* 为frame分配buffer */

// 分配输出缓冲区
ret = av_frame_get_buffer(frame, 0);
if (ret < 0)
{
    fprintf(stderr, "Could not allocate audio data buffers\n");
    exit(1);
}

// infile.pcm  ->  buffer  -> frame
// 每次传送1帧数据，帧大小 = 样本数 x 样本大小 x 通道数
int frame_bytes = av_get_bytes_per_sample(frame->format) \
            * frame->channels \
            * frame->nb_samples;

// 将读取到的PCM数据填充到frame去，但要注意格式的匹配, 是planar还是packed都要区分清楚
// 将本地的f32le packed模式的数据转为float palanar
memset(pcm_temp_buf, 0, frame_bytes);
f32le_convert_to_fltp((float *)pcm_buf, (float *)pcm_temp_buf, frame->nb_samples);
ret = av_samples_fill_arrays(frame->data, frame->linesize,
                             pcm_temp_buf, frame->channels,
                             frame->nb_samples, frame->format, 0);

```

#### 2 编码视频设置

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231205163307668.png" alt="image-20231205163307668" style="zoom:67%;" />

```cpp
AVFrame frame = av_frame_alloc();
if (!frame) {
    fprintf(stderr, "Could not allocate video frame\n");
    exit(1);
}

// 为frame分配buffer
frame->format = codec_ctx->pix_fmt;
frame->width  = codec_ctx->width;
frame->height = codec_ctx->height;
ret = av_frame_get_buffer(frame, 0);
if (ret < 0) {
    fprintf(stderr, "Could not allocate the video frame data\n");
    exit(1);
}
// 计算出每一帧的数据 像素格式 * 宽 * 高
// 1382400
int frame_bytes = av_image_get_buffer_size(frame->format, frame->width,
                                           frame->height, 1);
printf("frame_bytes %d\n", frame_bytes);
uint8_t *yuv_buf = (uint8_t *)malloc(frame_bytes);
if(!yuv_buf) {
    printf("yuv_buf malloc failed\n");
    return 1;
}
```

#### 3  解码后视频帧处理

![image-20240116193257732](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20240116193257732.png)

1. **pkt_pts**：解码过程中，根据pkt的dts顺序开始解码，并将pts传给frame的==pkt_pts==
2. **pts**：视频帧frame的pkt_pts==保持不变==

```cpp
ret = avcodec_receive_frame(d->avctx, frame);
if (ret >= 0) {
    if (decoder_reorder_pts == -1) {
        // 解码完成后，pkt的pts不存在，则vedio_frame的pts由ffmpeg计算获取
        frame->pts = frame->best_effort_timestamp;
    } else if (!decoder_reorder_pts) { 
        // 解码完成后，如果pkt的pts存在，则vedio_frame的`pts = pkt_pts`，视频帧的pts等于视频包的pts
        frame->pts = frame->pkt_dts;  
    }
}
```

3. **time_base**：视频解码器的time_base==保持不变==
4. pts化为秒单位

```cpp
AVRational tb = vedio_stream->time_base;
pts = frame->pts * av_q2d(tb)
```



#### 4 解码后音频帧处理

![image-20240116215402578](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20240116215402578.png)

1. **pts**：采用解码器生成的pts，经过时间基转换

   时间基转换：从AVStream的time_base  到 解码过程中的 time_base

   AVStream 的time_base不同的封装格式固定

   解码器的time_base 设置为采样率的倒数

```cpp
AVRational tb = (AVRational){1, frame->sample_rate}; 
frame->pts = av_rescale_q(frame->pts, d->avctx->pkt_timebase, tb);
```



## 3.4 AVBitStreamFilter

> MP4提取H264使用

### 3.4.1 接口

在FFmpeg中，`AVBitStreamFilter`是用于处理比特流的结构体。它通常与`AVBSFContext`结合使用，`AVBSFContext`表示比特流过滤器的上下文。下面是使用`AVBitStreamFilter`的一般步骤：

1. **获取比特流过滤器：**
   ```c
   const AVBitStreamFilter *bsfilter = av_bsf_get_by_name("h264_mp4toannexb");
   ```

   这一步获取了一个特定名称的比特流过滤器。你可以根据需要选择不同的过滤器。

2. **分配比特流过滤器上下文：**
  
   ```c
   AVBSFContext *bsf_ctx = NULL;
   av_bsf_alloc(bsfilter, &bsf_ctx);
   ```
   
   使用选定的比特流过滤器bsfilter，分配一个上下文。
   
   ```cpp
   // AVBSFContext结构体属性
   typedef struct AVBSFContext {
   
       const AVClass *av_class;
   
       const struct AVBitStreamFilter *filter;
   
       AVBSFInternal *internal;
   
       void *priv_data;
   
       // 存放编码器的属性
       AVCodecParameters *par_in;
   
       AVCodecParameters *par_out;
   
       AVRational time_base_in;
       
       AVRational time_base_out;
   } AVBSFContext;
   ```
   
   
   
3. **添加编码器属性（通过拷贝）：**
  
   ```cpp
   avcodec_parameters_copy(bsf_ctx->par_in, ifmt_ctx->streams[videoindex]->codecpar);
   ```
   
   将输入流的参数复制到过滤器的上下文中。
   
4. **初始化比特流过滤器上下文：**
   ```cpp
   av_bsf_init(bsf_ctx);
   ```

   进行比特流过滤器上下文的初始化。

5. **给filter上下文发送packet，处理比特流数据：**
  
   ```cpp
   AVPacket pkt = ... // 输入的AVPacket
   av_bsf_send_packet(bsf_ctx, &pkt);
   av_packet_unref(pkt);
   ```

   使用`av_bsf_send_packet`发送输入的比特流包。
   
6. **接收处理后的比特流数据：**
   ```c
   while (av_bsf_receive_packet(bsf_ctx, &pkt) == 0) {
       // 处理处理后的比特流包
       // 写入文件
       size_t size = fwrite(pkt->data, 1, pkt->size, outfp);
   }
   ```

   使用`av_bsf_receive_packet`接收处理后的比特流包。
   
7. **释放资源：**
   ```c
   av_bsf_free(&bsf_ctx);
   ```

   在使用完毕后释放比特流过滤器上下文。



## 3.5 AVCodecontext

### 3.5.1 音频属性

下面是一些常见的 `AVCodecContext` 结构体属性，以表格形式展示：

| 属性                             | 类型     | 描述                                                         |
| -------------------------------- | -------- | ------------------------------------------------------------ |
| `AVCodec *codec`                 | 指针     | 当前 `AVCodecContext` 实例使用的编解码器                     |
| `enum AVCodecID codec_id`        | 枚举     | codec的id                                                    |
|                                  |          |                                                              |
| `int sample_rate`                | 整数     | 音频采样率                                                   |
| `enum AVSampleFormat sample_fmt` | 枚举     | 音频采样格式                                                 |
| `uint64_t channel_layout`        | 64位整数 | 音频通道布局(单声道，多声道，更加详细)                       |
| `int channels`                   | 整数     | 音频通道数（单声道等、只设置通道数目，具体信息在channel_layout中） |
| `int frame_size`                 | 整数     | 音频帧大小，pcm常见1帧1024sample                             |
|                                  |          |                                                              |
| `bit_rate`                       |          |                                                              |
| `profile`                        |          | 编码不使用，解码用户设定                                     |
| `time_base`                      |          | 解码过程中使用的time_base【采样率的倒数】                    |
| ` pkt_timebase`                  |          | AVStream中的time_base，用于计算frame的pts【时间基转换】      |
|                                  |          |                                                              |



### 3.5.2 编码音频设置

* 编码时配置context
* 即，设置codec打开文件的时的参数
* 设置完毕后，各参数需要与codec内置的参数比较，看是否能按要求打开

以下的参数值相等，根据context的值，比较codec值， 同时给frame赋值

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231129120057019.png" alt="image-20231129120057019" style="zoom:67%;" />

```cpp
// user赋值
codec_ctx->codec_id = codec_id;
codec_ctx->codec_type = AVMEDIA_TYPE_AUDIO;
codec_ctx->channel_layout = AV_CH_LAYOUT_STEREO;
codec_ctx->sample_fmt = AV_SAMPLE_FMT_FLTP;
codec_ctx->sample_rate    = 48000; //48000;
codec_ctx->channels       = av_get_channel_layout_nb_channels(codec_ctx->channel_layout);
codec_ctx->bit_rate = 128*1024;
codec_ctx->profile = FF_PROFILE_AAC_LOW;    

// 与 codec比较
if(strcmp(codec->name, "aac") == 0) {
    codec_ctx->sample_fmt = AV_SAMPLE_FMT_FLTP;
} else if(strcmp(codec->name, "libfdk_aac") == 0) {
    codec_ctx->sample_fmt = AV_SAMPLE_FMT_S16;
} else {
    codec_ctx->sample_fmt = AV_SAMPLE_FMT_FLTP;
}

// 给frame赋值
frame->nb_samples     = codec_ctx->frame_size; // 帧大小
frame->format         = codec_ctx->sample_fmt;
frame->channel_layout = codec_ctx->channel_layout;
frame->channels = av_get_channel_layout_nb_channels(frame->channel_layout);
```

### 3.5.3 视频属性

| 属性                      | 类型 | 描述                                     |
| ------------------------- | ---- | ---------------------------------------- |
| `AVCodec *codec`          | 指针 | 当前 `AVCodecContext` 实例使用的编解码器 |
| `enum AVCodecID codec_id` | 枚举 | codec的id                                |
|                           |      |                                          |
| pix_fmt                   |      | 像素格式                                 |
| width x  height           |      | 分辨率                                   |
| framerate                 |      | 帧率 `=(AVRational){25, 1}`              |
| time_base                 |      | 频率 `=(AVRational){1, 25}`              |
| gop_size                  |      | I帧间隔   = 25                           |
| max_b_frames              |      | b 帧数量， 不要则=0                      |
|                           |      |                                          |
| priv_data                 |      | 设置 preset 、 profile、 tune            |
| bit_rate                  |      |                                          |

### 3.5.4 编码视频设置

```cpp
codec_ctx->pix_fmt = AV_PIX_FMT_YUV420P;
/* 设置time base */
codec_ctx->time_base = (AVRational){1, 25};
codec_ctx->framerate = (AVRational){25, 1};
    
/* 设置分辨率 */
codec_ctx->width = 1280;
codec_ctx->height = 720;

/* 设置I帧间隔
 * 如果frame->pict_type设置为AV_PICTURE_TYPE_I, 则忽略gop_size的设置，一直当做I帧进行编码
*/
codec_ctx->gop_size = 25;   // I帧间隔
codec_ctx->max_b_frames = 2; // 如果不想包含B帧则设置为0

// option
if (codec->id == AV_CODEC_ID_H264) {
    // 相关的参数可以参考libx264.c的 AVOption options
    // ultrafast all encode time:2270ms
    // medium all encode time:5815ms
    // veryslow all encode time:19836ms
    ret = av_opt_set(codec_ctx->priv_data, "preset", "medium", 0);
    if(ret != 0) {
        printf("av_opt_set preset failed\n");
    }
    ret = av_opt_set(codec_ctx->priv_data, "profile", "main", 0); // 默认是high
    if(ret != 0) {
        printf("av_opt_set profile failed\n");
    }
    ret = av_opt_set(codec_ctx->priv_data, "tune","zerolatency",0); // 直播是才使用该设置
    //        ret = av_opt_set(codec_ctx->priv_data, "tune","film",0); //  画质film
    if(ret != 0) {
        printf("av_opt_set tune failed\n");
    }
}
/* 设置bitrate */
codec_ctx->bit_rate = 3000000;
```

### 3.5.5 音频解码的时间基

上下文在解码过程中，`time_base` 和 `pkt_timebase`的变化

![image-20240116220150829](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20240116220150829.png)



## 3.6 AVCodec

* 默认aac编码器

![image-20231128153852736](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231128153852736.png)

### 3.6.1 属性

| 属性                                               | 类型   | 描述                                       |
| -------------------------------------------------- | ------ | ------------------------------------------ |
| `const char *name`                                 | 字符串 | 编解码器的名称（固定）                     |
| `enum AVCodecID id`                                | 枚举   | 编解码器的ID，唯一ID                       |
| `enum AVMediaType type`                            | 枚举   | 编解码器所处理的媒体类型，vedio、audio     |
|                                                    |        |                                            |
| `const enum AVSampleFormat *sample_fmt`            | 指针   | 支持的音视频采样格式                       |
| `const int supported_samplerates`                  | int    | 支持的音视频采样率                         |
| `const int channel_layouts`                        | int    | 支持的音视频通道数                         |
|                                                    |        |                                            |
| `const char *long_name`                            | 字符串 | 对编解码器的较为详细的描述                 |
|                                                    |        |                                            |
| `const AVProfile *profiles`                        | 指针   | 编解码器支持的配置文件（profiles）         |
| `const AVClass *priv_class`                        | 指针   | 编解码器的私有类                           |
| `const AVCodecCapabilities *capabilities`          | 指针   | 编解码器的功能和特性，设置支持可变尺寸音频 |
| `int priv_data_size`                               | 整数   | 编解码器的私有数据大小                     |
| `const AVProfile *defaults`                        | 指针   | 编解码器的默认配置文件                     |
| `const struct AVCodecHWConfigInternal *hw_configs` | 指针   | 编解码器支持的硬件配置                     |

对音频来说，如果 AV_CODEC_CAP_VARIABLE_FRAME_SIZE(在 AVCodecContext.codec.capabilities 变量中，只读)标志有效，表示编码器支持可变尺寸音频帧，送入编码器的音频帧可以包含任意数量的采样点。如果此标志无效，则每一个音频帧的采样点数目(frame->nb_samples)必须等于编码器设定的音频帧尺寸(avctx->frame_size)，最后一帧除外，最后一帧音频帧采样点数可以小于 avctx->frame_size

#### AVCodecID

```cpp
// 以解码编码的文件的样本格式命名
enum AVCodecID {
    AV_CODEC_ID_NONE,

    /* video codecs */
    AV_CODEC_ID_H263,
    AV_CODEC_ID_H264,
    AV_CODEC_ID_MPEG4,
    
    /* audio codecs */
    AV_CODEC_ID_MP3, ///< preferred ID for decoding MPEG audio layer 1, 2 or 3
    AV_CODEC_ID_AAC,
    
    /* various PCM "codecs" */
    AV_CODEC_ID_PCM_S16BE,
    AV_CODEC_ID_PCM_U16LE,
    AV_CODEC_ID_PCM_U16BE,
    AV_CODEC_ID_PCM_S32LE,
    AV_CODEC_ID_PCM_S32BE,
    AV_CODEC_ID_PCM_U32LE,
    AV_CODEC_ID_PCM_U32BE,
    AV_CODEC_ID_PCM_S24LE,
    AV_CODEC_ID_PCM_S24BE,
    AV_CODEC_ID_PCM_U24LE,
    AV_CODEC_ID_PCM_U24BE,
    AV_CODEC_ID_PCM_S24DAUD,
    AV_CODEC_ID_PCM_DVD,
    AV_CODEC_ID_PCM_F32BE,
    AV_CODEC_ID_PCM_F32LE,
    AV_CODEC_ID_PCM_F64BE,
    AV_CODEC_ID_PCM_F64LE,
    AV_CODEC_ID_PCM_S8_PLANAR,
    AV_CODEC_ID_PCM_S24LE_PLANAR,
    AV_CODEC_ID_PCM_S32LE_PLANAR,
    AV_CODEC_ID_PCM_S16BE_PLANAR
}
```



#### AVMediaType 

```cpp
enum AVMediaType {
    AVMEDIA_TYPE_UNKNOWN = -1,  ///< Usually treated as AVMEDIA_TYPE_DATA
    AVMEDIA_TYPE_VIDEO,
    AVMEDIA_TYPE_AUDIO,
    AVMEDIA_TYPE_DATA,          ///< Opaque data information usually continuous
    AVMEDIA_TYPE_SUBTITLE,
    AVMEDIA_TYPE_ATTACHMENT,    ///< Opaque data information usually sparse
    AVMEDIA_TYPE_NB
};
```

#### AVSampleFormat 

[两种模式的区别](E:\typora索引文件\ffmpeg\08-01-音频编码实战.pdf)

```cpp
// 支持的音视频采样格式
enum AVSampleFormat {
// packet模式
    AV_SAMPLE_FMT_NONE = -1,
    AV_SAMPLE_FMT_U8,          ///< unsigned 8 bits
    AV_SAMPLE_FMT_S16,         ///< signed 16 bits
    AV_SAMPLE_FMT_S32,         ///< signed 32 bits
    AV_SAMPLE_FMT_FLT,         ///< float
    AV_SAMPLE_FMT_DBL,         ///< double
// planar模式
    AV_SAMPLE_FMT_U8P,         ///< unsigned 8 bits, planar
    AV_SAMPLE_FMT_S16P,        ///< signed 16 bits, planar
    AV_SAMPLE_FMT_S32P,        ///< signed 32 bits, planar
    AV_SAMPLE_FMT_FLTP,        ///< float, planar
    AV_SAMPLE_FMT_DBLP,        ///< double, planar
    AV_SAMPLE_FMT_S64,         ///< signed 64 bits
    AV_SAMPLE_FMT_S64P,        ///< signed 64 bits, planar

    AV_SAMPLE_FMT_NB           ///< Number of sample formats. DO NOT USE if linking dynamically
};

/* 检测该编码器是否支持该采样格式 */
static int check_sample_fmt(const AVCodec *codec, enum AVSampleFormat sample_fmt)
{
    const enum AVSampleFormat *p = codec->sample_fmts;

    while (*p != AV_SAMPLE_FMT_NONE) { // 通过AV_SAMPLE_FMT_NONE作为结束符
        if (*p == sample_fmt)
            return 1;
        p++;
    }
    return 0;
}
```



### 3.6.2 接口

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231121112407259.png" alt="image-20231121112407259" style="zoom:67%;" />



```c
ret = av_parser_parse2(parser, codec_ctx, &pkt->data, &pkt->size,
                       data, data_size,
                       AV_NOPTS_VALUE, AV_NOPTS_VALUE, 0);
```

1. `ret`: 这是一个变量，用于存储函数调用的返回值。通常，这样的函数会返回一个表示操作是否成功的整数值。

2. `av_parser_parse2`: 这是FFmpeg库中的函数，用于解析媒体数据。具体而言，它的作用是从输入数据中提取有效的音频或视频帧。

3. `parser`: 这是一个解析器的实例，用于指定解析的方式。解析器通常根据媒体的类型（例如，音频或视频）来确定如何从输入数据中提取有效的帧。

4. `codec_ctx`: 这是一个编解码器上下文（codec context），提供了有关媒体编解码的信息，如编解码器类型、参数等。

5. `&pkt->data`, `&pkt->size`: 这是指向`pkt`结构体中数据和大小字段的指针。`pkt`可能是一个AVPacket结构体，用于存储解析后的媒体帧数据。

6. `data`, `data_size`: 这是输入数据的指针和大小。通常，这些是从文件中读取的原始音频或视频数据。

7. `AV_NOPTS_VALUE`: 这是FFmpeg中用于表示没有时间戳的特殊值。在这里，它表示解析器应该自己计算时间戳。

8. `0`: 这是一些附加的标志或参数，通常用于配置解析器的行为。

总体而言，这行代码的作用是使用FFmpeg的解析器解析输入数据，并将解析后的帧数据和大小存储在`pkt`结构体中。函数的返回值`ret`可能表示解析操作是否成功，以及是否有一些特殊的状态。

---------------

```c
data_size = fread(inbuf, 1, AUDIO_INBUF_SIZE, infile);
```

这一行使用了C标准库中的`fread`函数，从文件 `infile` 中读取数据到 `inbuf` 中。具体解释如下：

- `inbuf` 是用于存储读取数据的缓冲区。
- `1` 表示每次读取一个字节。
- `AUDIO_INBUF_SIZE` 是指定每次最多读取的字节数。
- `infile` 是指向要读取的文件的指针。

`fread` 函数返回实际读取的元素个数，这个值被赋给 `data_size`。因此，`data_size` 将包含实际读取的字节数。

总的来说，这段代码的作用是从文件中读取数据到缓冲区 `inbuf` 中，并记录实际读取的字节数到 `data_size`。

___

* 解码步骤

```cpp
// 1 avformat_open_input()     2 AVIOContext       3 fread()   
// 读取文件到packet

// 查找AAC解码器（法1）
const AVCodec *codec = avcodec_find_decoder(AV_CODEC_ID_AAC);  // AV_CODEC_ID_AAC
if (!codec) {
    fprintf(stderr, "Codec not found\n");
    exit(1);
}
// 查找解码器（法2）
const AVCodec *codec = avcodec_find_decoder_by_name("aac");

// 获取AAC裸流的解析器 AVCodecParserContext(数据)  +  AVCodecParser(方法)
parser = av_parser_init(codec->id);
if (!parser) {
    fprintf(stderr, "Parser not found\n");
    exit(1);
}
// 分配codec上下文
codec_ctx = avcodec_alloc_context3(codec);
if (!codec_ctx) {
    fprintf(stderr, "Could not allocate audio codec context\n");
    exit(1);
}

// 将解码器和解码器上下文进行关联
if (avcodec_open2(codec_ctx, codec, NULL) < 0) {
    fprintf(stderr, "Could not open codec\n");
    exit(1);
}

// 解码
 while (1) {
        ret = av_read_frame(format_ctx, packet);
        if(ret < 0) {
            printf("av_read_frame failed:%s\n", av_err2str(ret));
            break;
        }
        decode(codec_ctx, packet, frame, out_file);
    }
```



### 3.6.3 解码示例

```cpp
/**
* @projectName   07-05-decode_audio
* @brief         解码音频，传入aac或mp3，输出acm
* @author        张宇乔
* @date          2023-11-21
* @shell         ffplay -ar 48000 -ac 2 -f f32le believe.pcm
*/
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#include <libavutil/frame.h>
#include <libavutil/mem.h>

#include <libavcodec/avcodec.h>

#define AUDIO_INBUF_SIZE 20480
#define AUDIO_REFILL_THRESH 4096

static char err_buf[128] = {0};
static char* av_get_err(int errnum)
{
    av_strerror(errnum, err_buf, 128);
    return err_buf;
}

static void print_sample_format(const AVFrame *frame)
{
    printf("ar-samplerate: %uHz\n", frame->sample_rate);
    printf("ac-channel: %u\n", frame->channels);
    printf("f-format: %u\n", frame->format);// 格式需要注意，实际存储到本地文件时已经改成交错模式
}

static void decode(AVCodecContext *dec_ctx, AVPacket *pkt, AVFrame *frame,
                   FILE *outfile)
{
    int i, ch;
    int ret, data_size;
    // 发送到解码器解码
    ret = avcodec_send_packet(dec_ctx, pkt);
    if(ret == AVERROR(EAGAIN))
    {
        fprintf(stderr, "Receive_frame and send_packet both returned EAGAIN, which is an API violation.\n");
    }
    else if (ret < 0)
    {
        fprintf(stderr, "Error submitting the packet to the decoder, err:%s, pkt_size:%d\n",
                av_get_err(ret), pkt->size);
//        exit(1);
        return;
    }

    // 对于1个send，需要循环receive
    while (ret >= 0)
    {
        // 对于frame, avcodec_receive_frame内部每次都先调用
        ret = avcodec_receive_frame(dec_ctx, frame);

        if (ret == AVERROR(EAGAIN) || ret == AVERROR_EOF)
            return;
        else if (ret < 0)
        {
            fprintf(stderr, "Error during decoding\n");
            exit(1);
        }

        // receive成功后执行
        data_size = av_get_bytes_per_sample(dec_ctx->sample_fmt);
        if (data_size < 0)
        {
            /* This should not occur, checking just for paranoia */
            fprintf(stderr, "Failed to calculate data size\n");
            exit(1);
        }
        static int s_print_format = 0;
        if(s_print_format == 0)
        {
            s_print_format = 1;
            print_sample_format(frame);
        }
        /**
            P表示Planar（平面），其数据格式排列方式为 :
            LLLLLLRRRRRRLLLLLLRRRRRRLLLLLLRRRRRRL...（每个LLLLLLRRRRRR为一个音频帧）
            而不带P的数据格式（即交错排列）排列方式为：
            LRLRLRLRLRLRLRLRLRLRLRLRLRLRLRLRLRLRL...（每个LR为一个音频样本）
         播放范例：   ffplay -ar 48000 -ac 2 -f f32le believe.pcm
          */
        for (i = 0; i < frame->nb_samples; i++)
        {
            for (ch = 0; ch < dec_ctx->channels; ch++)  // 交错的方式写入, 大部分float的格式输出
                fwrite(frame->data[ch] + data_size*i, 1, data_size, outfile);
        }
    }
}
// 播放范例：   ffplay -ar 48000 -ac 2 -f f32le believe.pcm
int main(int argc, char **argv)
{
    const char *outfilename;
    const char *filename;

    AVCodecContext *codec_ctx= NULL;
    AVCodecParserContext *parser = NULL;
    int len = 0;
    int ret = 0;

    size_t   data_size = 0;



    if (argc <= 2)
    {
        fprintf(stderr, "Usage: %s <input file> <output file>\n", argv[0]);
        exit(0);
    }
    filename    = argv[1];
    outfilename = argv[2];

    enum AVCodecID audio_codec_id = AV_CODEC_ID_AAC;
    
    // aac 和 MP3 都可以解码
    if(strstr(filename, "aac") != NULL)
    {
        audio_codec_id = AV_CODEC_ID_AAC;
    }
    else if(strstr(filename, "mp3") != NULL)
    {
        audio_codec_id = AV_CODEC_ID_MP3;
    }
    else
    {
        printf("default codec id:%d\n", audio_codec_id);
    }

    // 查找解码器
    const AVCodec *codec;
    codec = avcodec_find_decoder(audio_codec_id);  // AV_CODEC_ID_AAC
    if (!codec) {
        fprintf(stderr, "Codec not found\n");
        exit(1);
    }
    // 【可以不用解析】获取裸流的解析器 AVCodecParserContext(数据)  +  AVCodecParser(方法)
    parser = av_parser_init(codec->id);
    if (!parser) {
        fprintf(stderr, "Parser not found\n");
        exit(1);
    }
    // 分配codec上下文
    codec_ctx = avcodec_alloc_context3(codec);
    if (!codec_ctx) {
        fprintf(stderr, "Could not allocate audio codec context\n");
        exit(1);
    }

    // 将解码器和解码器上下文进行关联
    if (avcodec_open2(codec_ctx, codec, NULL) < 0) {
        fprintf(stderr, "Could not open codec\n");
        exit(1);
    }

    FILE *infile = NULL;
    FILE *outfile = NULL;

    // 打开输入文件
    infile = fopen(filename, "rb");
    if (!infile) {
        fprintf(stderr, "Could not open %s\n", filename);
        exit(1);
    }
    // 打开输出文件
    outfile = fopen(outfilename, "wb");
    if (!outfile) {
        av_free(codec_ctx);
        exit(1);
    }

    uint8_t inbuf[AUDIO_INBUF_SIZE + AV_INPUT_BUFFER_PADDING_SIZE];
    uint8_t *data = NULL;
    // 读取文件进行解码
    data      = inbuf;
    // 从infile中每次读取AUDIO_INBUF_SIZE(20480)字节数据到inbuf中
    data_size = fread(inbuf, 1, AUDIO_INBUF_SIZE, infile);

    AVPacket *pkt = NULL;
    pkt = av_packet_alloc();
    AVFrame *decoded_frame = NULL;


    // 读取1次，循环解析+解码
    while (data_size > 0)
    {
        // 一、解析原数据（Ox数据  -> packet）
        if (!decoded_frame)
        {
            if (!(decoded_frame = av_frame_alloc()))
            {
                fprintf(stderr, "Could not allocate audio frame\n");
                exit(1);
            }
        }

        // 解析读取的数据，每次解析1帧(348B)
        ret = av_parser_parse2(parser, codec_ctx, &pkt->data, &pkt->size,
                               data, data_size,
                               AV_NOPTS_VALUE, AV_NOPTS_VALUE, 0);
        if (ret < 0)
        {
            fprintf(stderr, "Error while parsing\n");
            exit(1);
        }
        data      += ret;   // 跳过已经解析的数据
        data_size -= ret;   // 对应的缓存大小也做相应减小

        // 二、解码 （packet -> frame -> outfile）
        if (pkt->size)
            decode(codec_ctx, pkt, decoded_frame, outfile);

        // 三、再次读取数据
        if (data_size < AUDIO_REFILL_THRESH)    // 如果数据少了则再次读取
        {
            memmove(inbuf, data, data_size);    // 把之前剩的数据拷贝到buffer的起始位置
            data = inbuf;
            // 读取数据 长度: AUDIO_INBUF_SIZE - data_size
            len = fread(data + data_size, 1, AUDIO_INBUF_SIZE - data_size, infile);
            if (len > 0)
                data_size += len;
        }
    }

    /* 冲刷解码器 */
    pkt->data = NULL;   // 让其进入drain mode
    pkt->size = 0;
    decode(codec_ctx, pkt, decoded_frame, outfile);

    fclose(outfile);
    fclose(infile);

    avcodec_free_context(&codec_ctx);
    av_parser_close(parser);
    av_frame_free(&decoded_frame);
    av_packet_free(&pkt);

    printf("main finish, please enter Enter and exit\n");
    return 0;
}

```

## 3.7 AVIOContext

###  3.7.1 打开文件的方式

下面提供一个简单的示例代码，演示了两种读取文件的方法：一种是直接使用 `avformat_open_input` 打开文件，另一种是通过创建自定义的 `AVIOContext` 进行文件读取。请注意，这个示例只是为了说明概念，实际使用中可能需要添加更多的错误处理和资源释放代码。

```c
#include <libavformat/avformat.h>

// 读取文件的回调函数
int custom_read(void* opaque, uint8_t* buf, int buf_size) {
    // 在这里实现自定义的文件读取逻辑
    // 将文件数据拷贝到 buf 中，并返回实际读取的字节数
    // 如果到达文件末尾，返回 0 表示结束
    // 返回负值表示出现错误

    // 示例中直接返回 0，表示文件结束
    return 0;
}

int main() {
    // 方法1: 直接使用 avformat_open_input 打开文件
    AVFormatContext* format_ctx1 = avformat_alloc_context();
    int ret1 = avformat_open_input(&format_ctx1, "input_file.mp4", NULL, NULL);
    ...
    avformat_close_input(&format_ctx1);

    // 方法2: 使用自定义的 AVIOContext 打开文件
    AVFormatContext* format_ctx2 = avformat_alloc_context();
    
    // 创建自定义的 AVIOContext
    AVIOContext* avio_ctx = avio_alloc_context(
        malloc(4096),  // 缓冲区
        4096,          // 缓冲区大小
        0,             // 是否自动释放缓冲区
        fd,          // 用户数据，这里可以传递自定义结构体  FILE *fd = fread();
        custom_read,   // 读取数据的回调函数
        NULL,          // 写入数据的回调函数，这里不需要写入
        NULL           // 定位文件指针的回调函数，这里不需要定位
    );

    format_ctx2->pb = avio_ctx;

    int ret2 = avformat_open_input(&format_ctx2, NULL, NULL, NULL);

    if (ret2 < 0) {
        // 错误处理
        fprintf(stderr, "Error opening file using custom AVIOContext: %s\n", av_err2str(ret2));
        return ret2;
    }

    // 处理 format_ctx2，例如读取音视频流等...

    // 释放资源
    avformat_close_input(&format_ctx2);
    av_freep(&avio_ctx->buffer);  // 释放缓冲区
    avio_context_free(&avio_ctx); // 释放 AVIOContext

    return 0;
}
```

在这个示例中，第一种方法直接使用 `avformat_open_input` 打开文件，而第二种方法通过创建自定义的 `AVIOContext` 并关联到 `AVFormatContext` 中，实现了自定义的文件读取逻辑。在实际使用中，你需要根据具体的需求来实现自定义的文件读取逻辑，并根据情况调整缓冲区大小、对齐等参数。

### 3.7.2 接口

`AVIOContext` 是 FFmpeg 中用于输入/输出（I/O）操作的上下文结构体。以下是与 `AVIOContext` 相关的一些主要接口和函数：

1. **`avio_alloc_context`**:
   ```c
   AVIOContext *avio_alloc_context(
       unsigned char *buffer,
       int buffer_size,
       int write_flag,
       void *opaque,
       int (*read_packet)(void *opaque, uint8_t *buf, int buf_size),
       int (*write_packet)(void *opaque, uint8_t *buf, int buf_size),
       int64_t (*seek)(void *opaque, int64_t offset, int whence)
   );
   ```
   用于分配并初始化一个 `AVIOContext` 结构体。可以指定读写标志、缓冲区等参数，以及一些回调函数，如读取、写入、定位等。

2. **`avio_open`**:

   将ftm_ctx 与 一个MP4文件链接起来，直接使用该函数，传入`fmt_ctx-> pb`, 可对文件进行读写

   为url【MP4文件】创建一个IO流，返回给fmt_ctx ->pb，可以通过io流进行读写

   ```c
   int avio_open(AVIOContext **s, const char *url, int flags);
   
   s: 一个指向 AVIOContext 结构体指针的指针，用于存储创建的输入/输出上下文。
   url: 表示要打开的文件的 URL 或路径。
   flags: 表示打开文件的模式，可以是 AVIO_FLAG_READ（读取模式）、AVIO_FLAG_WRITE（写入模式）等
   // 使用方法
   AVFormatContext *fmt_ctx = avformat_alloc_context();
   ret = avio_open(&fmt_ctx->pb, filename, AVIO_FLAG_WRITE);
   ```
   用于打开输入/输出上下文，通常用于文件或网络流。将创建的 `AVIOContext` 存储在 `s` 指针指向的位置。

3. **`avio_close`**:
   ```c
   int avio_close(AVIOContext *s);
   ```
   用于关闭输入/输出上下文，并释放相关资源。

4. **`avio_read`**:
   ```c
   int avio_read(AVIOContext *s, unsigned char *buf, int size);
   ```
   从输入上下文读取数据到缓冲区。

5. **`avio_write`**:
   ```c
   int avio_write(AVIOContext *s, const unsigned char *buf, int size);
   ```
   将数据写入输出上下文。

6. **`avio_seek`**:
   ```c
   int64_t avio_seek(AVIOContext *s, int64_t offset, int whence);
   ```
   用于定位输入/输出上下文中的位置。

7. **`avio_flush`**:
   ```c
   void avio_flush(AVIOContext *s);
   ```
   刷新缓冲区，确保所有缓冲的数据被写入。

8. **`avio_feof`**:
   ```c
   int avio_feof(const AVIOContext *s);
   ```
   检查是否已经到达输入流的末尾。

这些函数提供了对 `AVIOContext` 结构体的常用操作，使得 FFmpeg 可以与不同的数据源进行交互，包括文件、网络流等。在使用这些函数时，需要根据具体的需求和场景选择合适的参数和回调函数。

## 3.8 AVOutputFormat

### 3.8.1 属性

AVOutpufFormat表示输出⽂件容器格式，AVOutputFormat 结构主要包含的信息有：

封装名称描述，编 码格式信息(video/audio 默认编码格式，⽀持的编码格式列表)，⼀些对封装的操作函数 (write_header,write_packet,write_tailer等)。

| 属性                            | 作用             |
| ------------------------------- | ---------------- |
| `const char *name;`             | `复用器名称`     |
| `enum AVCodecID audio_codec`    | `默认音频编码器` |
| `enum AVCodecID video_codec`    | `默认视频编码器` |
| `enum AVCodecID subtitle_codec` | `默认字幕编码器` |
| `int flags`                     | ``               |
| ``                              | ``               |
|                                 |                  |

> flags 参数

- `AVFMT_NOFILE`：指示该格式不需要底层文件句柄，因此可以用于流式传输等场景，而不需要实际的文件。
- `AVFMT_NEEDNUMBER`：该格式需要数字（如图像序列中的帧号）来生成文件名。
- `AVFMT_GLOBALHEADER`：指示该格式需要全局标头（global header）。这意味着编码器产生的头部信息可以被放置在文件中的一个全局位置，而不是每个帧都包含一个头部。
- `AVFMT_NOTIMESTAMPS`：该格式不使用时间戳，通常用于音频格式，因为音频数据可以被视为一系列均匀分布的样本。

默认 video_codec = flv1

audio_codec =  mp3

### 3.8.2 代码

```cpp
/**
* @projectName   07-05-decode_audio
* @brief         解码音频，主要的测试格式aac和mp3
* @date          2023-11-26
*/
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#include <libavutil/frame.h>
#include <libavutil/mem.h>

#include <libavcodec/avcodec.h>
#include <libavformat/avformat.h>

#define BUF_SIZE 20480


static char* av_get_err(int errnum)
{
    static char err_buf[128] = {0};
    av_strerror(errnum, err_buf, 128);
    return err_buf;
}

static void print_sample_format(const AVFrame *frame)
{
    printf("ar-samplerate: %uHz\n", frame->sample_rate);
    printf("ac-channel: %u\n", frame->channels);
    printf("f-format: %u\n", frame->format);// 格式需要注意，实际存储到本地文件时已经改成交错模式
}

// ============= 回调函数 ===================
static int read_packet(void *opaque, uint8_t *buf, int buf_size)
{
    FILE *in_file = (FILE *)opaque;
    int read_size = fread(buf, 1, buf_size, in_file);
    printf("read_packet read_size:%d, buf_size:%d\n", read_size, buf_size);
    if(read_size <=0) {
        return AVERROR_EOF;     // 数据读取完毕
    }
    return read_size;
}

static void decode(AVCodecContext *dec_ctx, AVPacket *packet, AVFrame *frame,
                   FILE *outfile)
{
    int ret = 0;
    ret = avcodec_send_packet(dec_ctx, packet);
    if(ret == AVERROR(EAGAIN)) {
        printf("Receive_frame and send_packet both returned EAGAIN, which is an API violation.\n");
    } else if(ret < 0) {
        printf("Error submitting the packet to the decoder, err:%s\n",
               av_get_err(ret));
        return;
    }

    while (ret >= 0) {
        ret = avcodec_receive_frame(dec_ctx, frame);
        if (ret == AVERROR(EAGAIN) || ret == AVERROR_EOF) {
            return;
        } else if (ret < 0)  {
            printf("Error during decoding\n");
            exit(1);
        }
        if(!packet) {
            printf("get flush frame\n");
        }
        int data_size = av_get_bytes_per_sample(dec_ctx->sample_fmt);
        //        print_sample_format(frame);
        /**
           P表示Planar（平面），其数据格式排列方式为 :
           LLLLLLRRRRRRLLLLLLRRRRRRLLLLLLRRRRRRL...（每个LLLLLLRRRRRR为一个音频帧）
           而不带P的数据格式（即交错排列）排列方式为：
           LRLRLRLRLRLRLRLRLRLRLRLRLRLRLRLRLRLRL...（每个LR为一个音频样本）
        播放范例：   ffplay -ar 48000 -ac 2 -f f32le believe.pcm
            并不是每一种都是这样的格式
        */
        // 这里的写法不是通用，通用要调用重采样的函数去实现
        // 这里只是针对解码出来是planar格式的转换
        for(int i = 0; i < frame->nb_samples; i++) {
            for(int ch = 0; ch < dec_ctx->channels; ch++) {
                fwrite(frame->data[ch] + data_size *i, 1, data_size, outfile);
            }
        }
    }
}

int main(int argc, char **argv)
{
    if(argc != 3) {
        printf("usage: %s <intput file> <out file>\n", argv[0]);
        return -1;
    }
    const char *in_file_name = argv[1];
    const char *out_file_name = argv[2];
    FILE *in_file = NULL;
    FILE *out_file = NULL;

    // 1. 打开参数文件
    in_file = fopen(in_file_name, "rb");
    if(!in_file) {
        printf("open file %s failed\n", in_file_name);
        return  -1;
    }
    out_file = fopen(out_file_name, "wb");
    if(!out_file) {
        printf("open file %s failed\n", out_file_name);
        return  -1;
    }

    // 2自定义 io
    uint8_t *io_buffer = av_malloc(BUF_SIZE);
    AVIOContext *avio_ctx = avio_alloc_context(io_buffer, BUF_SIZE, 0, (void *)in_file,    \
                                               read_packet, NULL, NULL);

    // 3 读入数据
    AVFormatContext *format_ctx = avformat_alloc_context();
    //====
    // =======   与第一种打开方式的传参不同
    format_ctx->pb = avio_ctx;
    int ret = avformat_open_input(&format_ctx, NULL, NULL, NULL);
    if(ret < 0) {
        printf("avformat_open_input failed:%s\n", av_err2str(ret));
        return -1;
    }

    // 查找AAC编码器
    AVCodec *codec = avcodec_find_decoder(AV_CODEC_ID_AAC);
    if(!codec) {
        printf("avcodec_find_decoder failed\n");
        return -1;
    }

    AVCodecContext *codec_ctx = avcodec_alloc_context3(codec);
    if(!codec_ctx) {
        printf("avcodec_alloc_context3 failed\n");
        return -1;
    }
    ret = avcodec_open2(codec_ctx, codec, NULL);
    if(ret < 0) {
        printf("avcodec_open2 failed:%s\n", av_err2str(ret));
        return -1;
    }

    AVPacket *packet = av_packet_alloc();
    AVFrame *frame = av_frame_alloc();

    while (1) {
        ret = av_read_frame(format_ctx, packet);
        if(ret < 0) {
            printf("av_read_frame failed:%s\n", av_err2str(ret));
            break;
        }
        decode(codec_ctx, packet, frame, out_file);
    }

    printf("read file finish\n");
    decode(codec_ctx, NULL, frame, out_file);

    fclose(in_file);
    fclose(out_file);

    av_free(io_buffer);
    av_frame_free(frame);
    av_packet_free(packet);

    avformat_close_input(&format_ctx);
    avcodec_free_context(&codec_ctx);

    printf("main finish\n");

    return 0;
}

```

## 3.9 send、reveive 

### 解码API

* avcodec_receive_frame() ：





#### 编码API

关于 avcodec_send_frame() 与 avcodec_receive_packet() 的使用说明：

```cpp
@return 0 on success, otherwise negative error code:
 *      AVERROR(EAGAIN):   input is not accepted in the current state - user
 *                         must read output with avcodec_receive_packet() (once
 *                         all output is read, the packet should be resent, and
 *                         the call will not fail with EAGAIN).
 *      AVERROR_EOF:       the encoder has been flushed, and no new frames can
 *                         be sent to it
 *      AVERROR(EINVAL):   codec not opened, refcounted_frames not set, it is a
 *                         decoder, or requires flush
 *      AVERROR(ENOMEM):   failed to add packet to internal queue, or similar
 *      other errors: legitimate decoding errors
 */
int avcodec_send_frame(AVCodecContext *avctx, const AVFrame *frame);
```

```cpp
@return 0 on success, otherwise negative error code:
 *      AVERROR(EAGAIN):   output is not available in the current state - user
 *                         must try to send input
 *      AVERROR_EOF:       the encoder has been fully flushed, and there will be
 *                         no more output packets
 *      AVERROR(EINVAL):   codec not opened, or it is an encoder
 *      other errors: legitimate decoding errors
 */
int avcodec_receive_packet(AVCodecContext *avctx, AVPacket *avpkt);
```



> 按==pts 递增的顺序==向编码器送入原始帧 frame，编码器按 dts 递增的顺序输出编码帧 packet，实际上编码器关注输入 frame 的 pts 不关注其 dts，它只管依次处理收到的 frame，按需缓冲和编码

> avcodec_receive_packet() 输出 packet 时，会设置 packet.dts，从 0 开始，每次输出的 packet 的 dts 加 1，这是视频层的 dts，用户写输出前应将其转换为容器层的 dts

> avcodec_receive_packet() 输出 packet 时，==packet.pts 拷贝自对应的 frame.pts==，这是视频层的 pts，用户写输出前应将其转换为容器层的 pts

> avcodec_send_frame() 发送==NULL==frame 时，编码器进入 flush 模式

> avcodec_send_frame() 发送第一个 NULL 会返回成功，后续的 NULL 会返回 AVERROR_EOF

> avcodec_send_frame() 多次发送 NULL 并不会导致编码器中缓存的帧丢失，使用 avcodec_flush_buffers() 可以立即丢掉编码器中缓存帧。因此编码完毕时应使用 avcodec_send_frame(NULL) 来取完缓存的帧，而SEEK操作或切换流时应调用 avcodec_flush_buffers() 来直接丢弃缓存帧

> 编码器通常的冲洗方法：调用一次 avcodec_send_frame(NULL)(返回成功)，然后不停调用 avcodec_receive_packet() 直到其返回 AVERROR_EOF，取出所有缓存帧，avcodec_receive_packet() 返回 AVERROR_EOF 这一次是没有有效数据的，仅仅获取到一个结束标志

> 对音频来说，如果 AV_CODEC_CAP_VARIABLE_FRAME_SIZE(在 AVCodecContext.codec.capabilities 变量中，只读)标志有效，表示编码器支持可变尺寸音频帧，送入编码器的音频帧可以包含任意数量的采样点。如果此标志无效，则每一个音频帧的采样点数目(frame->nb_samples)必须等于编码器设定的音频帧尺寸(avctx->frame_size)，最后一帧除外，最后一帧音频帧采样点数可以小于 avctx->frame_size



`av_send_packet` 和 `av_receive_frame` 是FFmpeg库中用于发送数据包和接收解码后的帧的两个关键函数。下面分别说明它们的作用以及它们分别发送和接收的属性：

###  1 av_send_packet：

* avcodec_send_packet() 使用空包刷新解码器，即：==发送第一个 NULL 会返回成功，后续的 NULL 会返回 AVERROR_EOF==
* avcodec_send_packet() 多次发送 NULL 并不会导致解码器中缓存的帧丢失，使用 avcodec_flush_buffers() 可以立即丢掉解码器中缓存帧。
* ==播放完毕==时应 avcodec_send_packet(NULL) 来取完缓存的帧，然后不停调用 avcodec_receive_frame() 直到其返回 AVERROR_EOF，取出所有缓存帧，最后调用avcodec_flush_buffers()清空解码器用于下次解码
* ==SEEK 操作或切换流==时应调用 avcodec_flush_buffers() 来直接丢弃缓存帧

**发送的属性：**

**AVPacket 结构体：** 通过 `AVPacket` 结构体，你可以设置多个属性，其中包括：

- `data`: 存储数据的缓冲区指针。
- `size`: 数据的大小。
- `pts`（Presentation Timestamp）: 呈现时间戳，表示当前帧在时间轴上的显示时间。
- `dts`（Decoding Timestamp）: 解码时间戳，表示当前帧在时间轴上的解码时间。

### 2 av_receive_frame：

* 按 dts 递增的顺序向解码器送入编码帧 packet，解码器按 pts 递增的顺序输出原始帧 frame，实际上解码器不关注输入 packe t的 dts(错值都没关系)，它只管依次处理收到的 packet，按需缓冲和解码
* avcodec_receive_frame() 输出 frame 时，会根据各种因素设置好==frame->best_effort_timestamp==(文档明确说明)，实测 ==frame->pts== 也会被设置(通常直接拷贝自对应的 packet.pts，文档未明确说明)用户应确保 avcodec_send_packet() 发送的 packet 具有正确的 pts，编码帧 packet 与原始帧 frame 间的对应关系通过 pts 确定

```cpp
ret = avcodec_receive_frame(d->avctx, frame);
if (ret >= 0) {
    if (decoder_reorder_pts == -1) {
        frame->pts = frame->best_effort_timestamp;
    } else if (!decoder_reorder_pts) {
        frame->pts = frame->pkt_dts;
    }
}
```



* avcodec_receive_frame() 输出 frame 时，frame->pkt_dts 拷贝自当前avcodec_send_packet() 发送的 packet 中的 dts，如果当前 packet 为 ==NULL(flush packet)，解码器进入 flush 模式==，当前及剩余的 frame->pkt_dts 值总为 AV_NOPTS_VALUE。因为解码器中有缓存帧，当前输出的 frame 并不是由当前输入的 packet 解码得到的，所以这个 frame->pkt_dts 没什么实际意义，可以不必关注

 **接收的属性：**

   - **AVFrame 结构体：** 通过 `AVFrame` 结构体，你可以获取解码后帧的多个属性，其中包括：
      - `data`: 存储图像数据的指针数组。
      - `linesize`: 图像数据每行的字节数。
      - `width` 和 `height`: 帧的宽度和高度。
      - `pts`（Presentation Timestamp）: 呈现时间戳，表示当前帧在时间轴上的显示时间。
      - 其他一些帧的相关信息。

### 注意事项：
- 发送的数据包（`AVPacket`）中的 `pts` 和 `dts` 表示原始数据的时间戳信息，而接收的帧（`AVFrame`）中的 `pts` 表示解码后的帧的时间戳信息。这两个时间戳可能在一些情况下是相同的，但在其他情况下可能会有差异，这取决于媒体流的特性和编解码器的工作方式。
- 数据包和帧的属性不仅限于上述列举的内容，还可能包括其他一些与特定编解码器或媒体格式相关的信息。

总体而言，`av_send_packet` 主要用于将原始数据传递给编码器，而 `av_receive_frame` 用于从解码器中获取解码后的帧。这两个函数是进行音视频处理时非常常用的组合。

# 四 协议

## 4.1 AAC

### 4.1.1 aac 解析

* [学习资料](D:\课程资料\音视频\笔记\AAC ADTS格式分析.pdf)

```cpp
/**
* 传入 aac裸流，解析为数据流，装入packet，整个流程可以用 avformat_open_infile()函数替代
*/
#include <stdio.h>
#include <libavutil/log.h>
#include <libavformat/avio.h>
#include <libavformat/avformat.h>

#define ADTS_HEADER_LEN  7;

const int sampling_frequencies[] = {
    96000,  // 0x0
    88200,  // 0x1
    64000,  // 0x2
    48000,  // 0x3
    44100,  // 0x4
    32000,  // 0x5
    24000,  // 0x6
    22050,  // 0x7
    16000,  // 0x8
    12000,  // 0x9
    11025,  // 0xa
    8000   // 0xb
    // 0xc d e f是保留的
};

int adts_header(char * const p_adts_header, const int data_length,
                const int profile, const int samplerate,
                const int channels)
{

    // 设置音频采样率，若输入samp不符合，则默认为48000hz
    int sampling_frequency_index = 3; // 默认使用48000hz
    int adtsLen = data_length + 7;

    int frequencies_size = sizeof(sampling_frequencies) / sizeof(sampling_frequencies[0]);
    int i = 0;
    for(i = 0; i < frequencies_size; i++)
    {
        if(sampling_frequencies[i] == samplerate)
        {
            sampling_frequency_index = i;
            break;
        }
    }
    if(i >= frequencies_size)
    {
        printf("unsupport samplerate:%d\n", samplerate);
        return -1;
    }

    p_adts_header[0] = 0xff;         //syncword:0xfff                          高8bits
    p_adts_header[1] = 0xf0;         //syncword:0xfff                          低4bits
    p_adts_header[1] |= (0 << 3);    //MPEG Version:0 for MPEG-4,1 for MPEG-2  1bit
    p_adts_header[1] |= (0 << 1);    //Layer:0                                 2bits
    p_adts_header[1] |= 1;           //protection absent:1                     1bit

    p_adts_header[2] = (profile)<<6;            //profile:profile               2bits
    p_adts_header[2] |= (sampling_frequency_index & 0x0f)<<2; //sampling frequency index:sampling_frequency_index  4bits
    p_adts_header[2] |= (0 << 1);             //private bit:0                   1bit
    p_adts_header[2] |= (channels & 0x04)>>2; //channel configuration:channels  高1bit

    p_adts_header[3] = (channels & 0x03)<<6; //channel configuration:channels 低2bits
    p_adts_header[3] |= (0 << 5);               //original：0                1bit
    p_adts_header[3] |= (0 << 4);               //home：0                    1bit
    p_adts_header[3] |= (0 << 3);               //copyright id bit：0        1bit
    p_adts_header[3] |= (0 << 2);               //copyright id start：0      1bit
    p_adts_header[3] |= ((adtsLen & 0x1800) >> 11);           //frame length：value   高2bits

    p_adts_header[4] = (uint8_t)((adtsLen & 0x7f8) >> 3);     //frame length:value    中间8bits
    p_adts_header[5] = (uint8_t)((adtsLen & 0x7) << 5);       //frame length:value    低3bits
    p_adts_header[5] |= 0x1f;                                 //buffer fullness:0x7ff 高5bits
    p_adts_header[6] = 0xfc;      //‭11111100‬       //buffer fullness:0x7ff 低6bits
    // number_of_raw_data_blocks_in_frame：
    //    表示ADTS帧中有number_of_raw_data_blocks_in_frame + 1个AAC原始帧。

    return 0;
}

int main(int argc, char *argv[])
{
    int ret = -1;
    char errors[1024];

    char *in_filename = NULL;
    char *aac_filename = NULL;

    FILE *aac_fd = NULL;

    int audio_index = -1;
    int len = 0;


    AVFormatContext *ifmt_ctx = NULL;
    AVPacket pkt;

    // 设置打印级别
    av_log_set_level(AV_LOG_DEBUG);

    if(argc < 3)
    {
        av_log(NULL, AV_LOG_DEBUG, "the count of parameters should be more than three!\n");
        return -1;
    }

    in_filename = argv[1];      // 输入文件
    aac_filename = argv[2];     // 输出文件

    if(in_filename == NULL || aac_filename == NULL)
    {
        av_log(NULL, AV_LOG_DEBUG, "src or dts file is null, plz check them!\n");
        return -1;
    }

    aac_fd = fopen(aac_filename, "wb");
    if (!aac_fd)
    {
        av_log(NULL, AV_LOG_DEBUG, "Could not open destination file %s\n", aac_filename);
        return -1;
    }

    // 打开输入文件
    if((ret = avformat_open_input(&ifmt_ctx, in_filename, NULL, NULL)) < 0)
    {
        av_strerror(ret, errors, 1024);
        av_log(NULL, AV_LOG_DEBUG, "Could not open source file: %s, %d(%s)\n",
               in_filename,
               ret,
               errors);
        return -1;
    }

    // 获取解码器信息
    if((ret = avformat_find_stream_info(ifmt_ctx, NULL)) < 0)
    {
        av_strerror(ret, errors, 1024);
        av_log(NULL, AV_LOG_DEBUG, "failed to find stream information: %s, %d(%s)\n",
               in_filename,
               ret,
               errors);
        return -1;
    }

    // dump媒体信息
    av_dump_format(ifmt_ctx, 0, in_filename, 0);

    // 初始化packet
    av_init_packet(&pkt);

    // 查找audio对应的steam index
    audio_index = av_find_best_stream(ifmt_ctx, AVMEDIA_TYPE_AUDIO, -1, -1, NULL, 0);
    if(audio_index < 0)
    {
        av_log(NULL, AV_LOG_DEBUG, "Could not find %s stream in input file %s\n",
               av_get_media_type_string(AVMEDIA_TYPE_AUDIO),
               in_filename);
        return AVERROR(EINVAL);
    }

    // 打印AAC级别
    printf("audio profile:%d, FF_PROFILE_AAC_LOW:%d\n",
           ifmt_ctx->streams[audio_index]->codecpar->profile,
           FF_PROFILE_AAC_LOW);

    if(ifmt_ctx->streams[audio_index]->codecpar->codec_id != AV_CODEC_ID_AAC)
    {
        printf("the media file no contain AAC stream, it's codec_id is %d\n",
               ifmt_ctx->streams[audio_index]->codecpar->codec_id);
        goto failed;
    }
    // 读取媒体文件，并把aac数据帧写入到本地文件
    while(av_read_frame(ifmt_ctx, &pkt) >=0 )
    {
        if(pkt.stream_index == audio_index)
        {
            char adts_header_buf[7] = {0};
            adts_header(adts_header_buf, pkt.size,
                        ifmt_ctx->streams[audio_index]->codecpar->profile,
                        ifmt_ctx->streams[audio_index]->codecpar->sample_rate,
                        ifmt_ctx->streams[audio_index]->codecpar->channels);
            fwrite(adts_header_buf, 1, 7, aac_fd);  // 写adts header , ts流不适用，ts流分离出来的packet带了adts header
            len = fwrite( pkt.data, 1, pkt.size, aac_fd);   // 写adts data
            if(len != pkt.size)
            {
                av_log(NULL, AV_LOG_DEBUG, "warning, length of writed data isn't equal pkt.size(%d, %d)\n",
                       len,
                       pkt.size);
            }
        }
        av_packet_unref(&pkt);
    }

failed:
    // 关闭输入文件
    if(ifmt_ctx)
    {
        avformat_close_input(&ifmt_ctx);
    }
    if(aac_fd)
    {
        fclose(aac_fd);
    }

    return 0;
}

```

### 4.1.2 aac解码

[代码位置](D:\QT\project\Decode\audio_decode)

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231126130108884.png" alt="image-20231126130108884" style="zoom:67%;" />

```cpp
/**
* @projectName   07-05-decode_audio
* @brief         解码音频，传入aac或mp3，解码生成acm文件
* @author        张宇乔
* @date          2023-11-21
*/
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#include <libavutil/frame.h>
#include <libavutil/mem.h>

#include <libavcodec/avcodec.h>
#include <libavformat/avformat.h>

#define AUDIO_INBUF_SIZE 20480
#define AUDIO_REFILL_THRESH 4096

static char err_buf[128] = {0};
static char* av_get_err(int errnum)
{
    av_strerror(errnum, err_buf, 128);
    return err_buf;
}

static void print_sample_format(const AVFrame *frame)
{
    printf("ar-samplerate: %uHz\n", frame->sample_rate);
    printf("ac-channel: %u\n", frame->channels);
    printf("f-format: %u\n", frame->format);// 格式需要注意，实际存储到本地文件时已经改成交错模式
}

static void decode(AVCodecContext *dec_ctx, AVPacket *pkt, AVFrame *frame,
                   FILE *outfile)
{
    int i, ch;
    int ret, data_size;
    // 发送到解码器解码
    // pkt有348数据
    ret = avcodec_send_packet(dec_ctx, pkt);
    if(ret == AVERROR(EAGAIN))
    {
        fprintf(stderr, "Receive_frame and send_packet both returned EAGAIN, which is an API violation.\n");
    }
    else if (ret < 0)
    {
        fprintf(stderr, "Error submitting the packet to the decoder, err:%s, pkt_size:%d\n",
                av_get_err(ret), pkt->size);
//        exit(1);
        return;
    }

    // 对于1个send，需要循环receive
    while (ret >= 0)
    {
        // 对于frame, avcodec_receive_frame内部每次都先调用
        // frame读取了348
        ret = avcodec_receive_frame(dec_ctx, frame);

        if (ret == AVERROR(EAGAIN) || ret == AVERROR_EOF)
            return;
        else if (ret < 0)
        {
            fprintf(stderr, "Error during decoding\n");
            exit(1);
        }

        // receive成功后执行
        data_size = av_get_bytes_per_sample(dec_ctx->sample_fmt);
        if (data_size < 0)
        {
            /* This should not occur, checking just for paranoia */
            fprintf(stderr, "Failed to calculate data size\n");
            exit(1);
        }
        static int s_print_format = 0;
        if(s_print_format == 0)
        {
            s_print_format = 1;
            print_sample_format(frame);
        }
        /**
            P表示Planar（平面），其数据格式排列方式为 :
            LLLLLLRRRRRRLLLLLLRRRRRRLLLLLLRRRRRRL...（每个LLLLLLRRRRRR为一个音频帧）
            而不带P的数据格式（即交错排列）排列方式为：
            LRLRLRLRLRLRLRLRLRLRLRLRLRLRLRLRLRLRL...（每个LR为一个音频样本）
         播放范例：   ffplay -ar 48000 -ac 2 -f f32le believe.pcm
          */
        for (i = 0; i < frame->nb_samples; i++)
        {
            for (ch = 0; ch < dec_ctx->channels; ch++)  // 交错的方式写入, 大部分float的格式输出
                fwrite(frame->data[ch] + data_size*i, 1, data_size, outfile);
        }
    }
}
// 播放范例：   ffplay -ar 48000 -ac 2 -f f32le believe.pcm
int main(int argc, char **argv)
{
    const char *outfilename;
    const char *filename;

    AVCodecContext *codec_ctx= NULL;
    AVCodecParserContext *parser = NULL;
    int len = 0;
    int ret = 0;

    size_t   data_size = 0;



    if (argc <= 2)
    {
        fprintf(stderr, "Usage: %s <input file> <output file>\n", argv[0]);
        exit(0);
    }
    filename    = argv[1];
    outfilename = argv[2];

    enum AVCodecID audio_codec_id = AV_CODEC_ID_AAC;
    if(strstr(filename, "aac") != NULL)
    {
        audio_codec_id = AV_CODEC_ID_AAC;
    }
    else if(strstr(filename, "mp3") != NULL)
    {
        audio_codec_id = AV_CODEC_ID_MP3;
    }
    else
    {
        printf("default codec id:%d\n", audio_codec_id);
    }

    // 查找解码器
    const AVCodec *codec;
    codec = avcodec_find_decoder(audio_codec_id);  // AV_CODEC_ID_AAC
    if (!codec) {
        fprintf(stderr, "Codec not found\n");
        exit(1);
    }
    // 获取裸流的解析器 AVCodecParserContext(数据)  +  AVCodecParser(方法)
    parser = av_parser_init(codec->id);
    if (!parser) {
        fprintf(stderr, "Parser not found\n");
        exit(1);
    }
    // 分配codec上下文
    codec_ctx = avcodec_alloc_context3(codec);
    if (!codec_ctx) {
        fprintf(stderr, "Could not allocate audio codec context\n");
        exit(1);
    }

    // 将解码器和解码器上下文进行关联
    if (avcodec_open2(codec_ctx, codec, NULL) < 0) {
        fprintf(stderr, "Could not open codec\n");
        exit(1);
    }

    FILE *infile = NULL;
    FILE *outfile = NULL;

    // 打开输入文件（方式2）
//    infile = fopen(filename, "rb");
//    if (!infile) {
//        fprintf(stderr, "Could not open %s\n", filename);
//        exit(1);
//    }
    // 打开输出文件
    outfile = fopen(outfilename, "wb");
    if (!outfile) {
        av_free(codec_ctx);
        exit(1);
    }

    // 直接打开文件（方式1）
    AVPacket *pkt = NULL;
    pkt = av_packet_alloc();
    AVFrame *decoded_frame = NULL;
    decoded_frame = av_frame_alloc();

    AVFormatContext *format_ctx = avformat_alloc_context();
    ret = avformat_open_input(&format_ctx, filename, NULL, NULL);
    if(ret < 0) {
        printf("avformat_open_input failed:%s\n", av_err2str(ret));
        return -1;
    }

    // 读取数据到packet
    while (1) {
        ret = av_read_frame(format_ctx, pkt);
        if(ret < 0) {
            printf("av_read_frame failed:%s\n", av_err2str(ret));
            break;
        }
        decode(codec_ctx, pkt, decoded_frame, outfile);
    }




#if 0
    uint8_t inbuf[AUDIO_INBUF_SIZE + AV_INPUT_BUFFER_PADDING_SIZE];
    uint8_t *data = NULL;
    // 读取文件进行解码
    data      = inbuf;
    // 从infile中每次读取AUDIO_INBUF_SIZE(20480)字节数据到inbuf中
    data_size = fread(inbuf, 1, AUDIO_INBUF_SIZE, infile);

    AVPacket *pkt = NULL;
    pkt = av_packet_alloc();
    AVFrame *decoded_frame = NULL;


    // 读取1次，循环解析+解码
    while (data_size > 0)
    {
        // 一、解析原数据（Ox数据  -> packet）
        if (!decoded_frame)
        {
            if (!(decoded_frame = av_frame_alloc()))
            {
                fprintf(stderr, "Could not allocate audio frame\n");
                exit(1);
            }
        }

        // 解析读取的数据，每次解析1帧(348B)
        ret = av_parser_parse2(parser, codec_ctx, &pkt->data, &pkt->size,
                               data, data_size,
                               AV_NOPTS_VALUE, AV_NOPTS_VALUE, 0);
        if (ret < 0)
        {
            fprintf(stderr, "Error while parsing\n");
            exit(1);
        }
        data      += ret;   // 跳过已经解析的数据
        data_size -= ret;   // 对应的缓存大小也做相应减小

        // 二、解码 （packet -> frame -> outfile）
        if (pkt->size)
            decode(codec_ctx, pkt, decoded_frame, outfile);

        // 三、再次读取数据
        if (data_size < AUDIO_REFILL_THRESH)    // 如果数据少了则再次读取
        {
            memmove(inbuf, data, data_size);    // 把之前剩的数据拷贝到buffer的起始位置
            data = inbuf;
            // 读取数据 长度: AUDIO_INBUF_SIZE - data_size
            len = fread(data + data_size, 1, AUDIO_INBUF_SIZE - data_size, infile);
            if (len > 0)
                data_size += len;
        }
    }
#endif


    /* 冲刷解码器 */
    pkt->data = NULL;   // 让其进入drain mode
    pkt->size = 0;
    decode(codec_ctx, pkt, decoded_frame, outfile);

    fclose(outfile);
    fclose(infile);

    avcodec_free_context(&codec_ctx);
    av_parser_close(parser);
    av_frame_free(&decoded_frame);
    av_packet_free(&pkt);

    printf("main finish, please enter Enter and exit\n");
    return 0;
}

```



## 4.2 H264

* [h264Visa](E:\vedio_tools\H264visa)

* [h264编码原理](E:\typora索引文件\ffmpeg\08-02-图文并茂分析H264编码原理-1.0版本-待续 .pdf)

* [NALU学习资料](E:\typora索引文件\ffmpeg\H264 NALU分析.pdf)

### 4.2.1 理论

1. YUV格式

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231127161056442.png" alt="image-20231127161056442" style="zoom:67%;" />

* 一帧一张图片

* 一张图片无数个像素点，h264编码过程中的单位是宏块

* 对于一个 YUV 图像，可以把划分成一个个 16x16 的宏块(h264)

* 2 x 2范围的像素点共享一个u data （蓝色分量）和 一个 v data （红色分量），但是4个各自有自己的y分量

* 在YUV颜色空间中，每个分量的值范围如下：

  1. **Y（亮度分量）**：通常取值范围是0到255。较小的值表示较暗的颜色，而较大的值表示较亮的颜色。

  2. **U（色度-蓝色分量）**：取值范围也是0到255。较小的值表示较少的蓝色饱和度，而较大的值表示较多的蓝色饱和度。

  3. **V（色度-红色分量）**：同样取值范围是0到255。较小的值表示较少的红色饱和度，而较大的值表示较多的红色饱和度。

  这个范围是根据8位深度的颜色表示而来，其中0表示最暗的颜色，255表示最亮的颜色。不过，实际使用中，有时会使用不同的范围和位深度，例如，10位或12位深度的颜色表示。

----------

2. 帧内预测

帧内预测在视频编码中通常是指在一个宏块（宏块大小通常是16x16像素）内使用相邻块的信息来预测当前块的内容。这是因为视频编码标准（如H.264）通常以宏块为单位进行运动估计和帧内预测。

在H.264中，一个宏块可以包含多个8x8的块。对于帧内预测，通常采用三种类型的预测模式：垂直预测（从上方块复制像素值）、水平预测（从左侧块复制像素值）和DC预测（使用上方和左侧块的DC分量的平均值作为预测值）。这些预测模式之一会被选择来预测当前宏块或块的内容。

因此，你可以说在8x8的像素范围内，通过上方块、左侧块和左上方块的信息来预测当前块的内容。这种方式有助于减少冗余信息，从而提高视频编码的效率。

假设有一个16x16的宏块，被划分成四个8x8的块，它们分别是A、B、C、D。我们来看一下如何进行帧内预测：

```
+------+------+    +------+------+    +------+------+    +------+------+ 
|      |      |    |  A   |  B   |    |  A   |  B   |    |  A   |  B   | 
|      |      |    |      |      |    |      |      |    |      |      | 
+------+------+    +------+------+    +------+------+    +------+------+
|  C   |  D   |    |  C   |  D   |    |  C   |  D   |    |  C   |  D   | 
|      |      |    |      |      |    |      |      |    |      |      | 
+------+------+    +------+------+    +------+------+    +------+------+
```

在这个简化的例子中，每个8x8块可以选择一种帧内预测模式。例如，A块可能选择垂直预测，B块选择水平预测，C块选择DC预测，D块选择其他预测模式。每个预测模式都会根据相邻块的信息来进行压缩。

请注意，这只是一个概念性的示例，实际的帧内预测涉及更复杂的算法和多种预测模式。希望这个简化的描述能够帮助你理解宏块内每个8x8块都可以选择不同的压缩方式。



3. DCT变换和量化

H.264编码中的DCT变换和量化操作通常是在8x8的块上进行的。视频帧通常被划分成多个8x8的块，然后每个块都会经历DCT变换和量化阶段，以减小数据量并实现压缩。

具体步骤如下：

1. **DCT变换：** 将每个8x8的块进行二维离散余弦变换（2D DCT），将空间域中的像素转换为频域中的系数。这个过程产生了8x8的DCT系数块。

2. **量化：** 对DCT系数进行量化，通过使用一个特定的量化矩阵将系数映射到一组有限的值。这个量化过程导致一些高频分量的系数被设置为零，从而减小数据量。

这两个步骤对于H.264编码中的每个8x8块都是独立进行的。通过在8x8块上操作，H.264能够更好地利用视频中的局部相干性，并更有效地进行压缩。这种块级别的处理也使得H.264具备更高的灵活性，可以适应不同大小和分辨率的视频。



4. h264编码原理总结

为了能够在最后熵编码的时候压缩率更高，对于送到熵编码（以行程编码为例）的“像 素串”，包含的0越多，越能提高压缩率。为了达到这个目标： 

◼ 先通过帧内预测或者帧间预测去除空间冗余和时间冗余，从而得到一个像素值相 比编码块小很多的残差块。 

◼ 然后再通过 DCT 变换将低频和高频信息分离开来得到变换块，然后再对变换块的 系数做量化。 

◼ 由于高频系数通常比较小，很容易量化为 0，同时人眼对高频信息不太敏感，这样 就得到了一串含有很多个 0，大多数情况下是一串含有连续 0 的“像素串”，并且人 的观感还不会太明显。

这样，最后熵编码就能把图像压缩成比较小的数据，以此 达到视频压缩的目的。

























### 4.2.2 h264解析

```cpp
/**
* 传入h264裸流，解析为数据流，装入packet
*/
#include <stdio.h>
#include <libavutil/log.h>
#include <libavformat/avio.h>
#include <libavformat/avformat.h>



static char err_buf[128] = {0};
static char* av_get_err(int errnum)
{
    av_strerror(errnum, err_buf, 128);
    return err_buf;
}

/*
AvCodecContext->extradata[]中为nalu长度
*   codec_extradata:
*   1, 64, 0, 1f, ff, e1, [0, 18], 67, 64, 0, 1f, ac, c8, 60, 78, 1b, 7e,
*   78, 40, 0, 0, fa, 40, 0, 3a, 98, 3, c6, c, 66, 80,
*   1, [0, 5],68, e9, 78, bc, b0, 0,
*/

//ffmpeg -i 2018.mp4 -codec copy -bsf:h264_mp4toannexb -f h264 tmp.h264
//ffmpeg 从mp4上提取H264的nalu h
int main(int argc, char **argv)
{
    AVFormatContext *ifmt_ctx = NULL;
    int             videoindex = -1;
    AVPacket        *pkt = NULL;
    int             ret = -1;
    int             file_end = 0; // 文件是否读取结束

    if(argc < 3)
    {
        printf("usage inputfile outfile\n");
        return -1;
    }
    FILE *outfp=fopen(argv[2],"wb");
    printf("in:%s out:%s\n", argv[1], argv[2]);

    // 分配解复用器的内存，使用avformat_close_input释放
    ifmt_ctx = avformat_alloc_context();
    if (!ifmt_ctx)
    {
        printf("[error] Could not allocate context.\n");
        return -1;
    }

    // 根据url打开码流，并选择匹配的解复用器
    ret = avformat_open_input(&ifmt_ctx,argv[1], NULL, NULL);
    if(ret != 0)
    {
        printf("[error]avformat_open_input: %s\n", av_get_err(ret));
        return -1;
    }

    // 读取媒体文件的部分数据包以获取码流信息
    ret = avformat_find_stream_info(ifmt_ctx, NULL);
    if(ret < 0)
    {
        printf("[error]avformat_find_stream_info: %s\n", av_get_err(ret));
        avformat_close_input(&ifmt_ctx);
        return -1;
    }

    // 查找出哪个码流是video/audio/subtitles
    videoindex = -1;
    // 推荐的方式
    videoindex = av_find_best_stream(ifmt_ctx, AVMEDIA_TYPE_VIDEO, -1, -1, NULL, 0);
    if(videoindex == -1)
    {
        printf("Didn't find a video stream.\n");
        avformat_close_input(&ifmt_ctx);
        return -1;
    }

    // 分配数据包
    pkt = av_packet_alloc();
    av_init_packet(pkt);

    // 1 获取相应的比特流过滤器
    //FLV/MP4/MKV等结构中，h264需要h264_mp4toannexb处理。添加SPS/PPS等信息。
    // FLV封装时，可以把多个NALU放在一个VIDEO TAG中,结构为4B NALU长度+NALU1+4B NALU长度+NALU2+...,
    // 需要做的处理把4B长度换成00000001或者000001
    const AVBitStreamFilter *bsfilter = av_bsf_get_by_name("h264_mp4toannexb");
    AVBSFContext *bsf_ctx = NULL;
    // 2 初始化过滤器上下文
    av_bsf_alloc(bsfilter, &bsf_ctx); //AVBSFContext;
    // 3 添加解码器属性
    avcodec_parameters_copy(bsf_ctx->par_in, ifmt_ctx->streams[videoindex]->codecpar);
    av_bsf_init(bsf_ctx);

    file_end = 0;
    while (0 == file_end)
    {
        if((ret = av_read_frame(ifmt_ctx, pkt)) < 0)
        {
            // 没有更多包可读
            file_end = 1;
            printf("read file end: ret:%d\n", ret);
        }
        if(ret == 0 && pkt->stream_index == videoindex)
        {
#if 0
            int input_size = pkt->size;
            int out_pkt_count = 0;
            if (av_bsf_send_packet(bsf_ctx, pkt) != 0) // bitstreamfilter内部去维护内存空间
            {
                av_packet_unref(pkt);   // 你不用了就把资源释放掉
                continue;       // 继续送
            }
 //           av_packet_unref(pkt);   // 释放资源,可以不用释放，自动覆盖
            while(av_bsf_receive_packet(bsf_ctx, pkt) == 0)
            {
                out_pkt_count++;
                // printf("fwrite size:%d\n", pkt->size);
                size_t size = fwrite(pkt->data, 1, pkt->size, outfp);
                if(size != pkt->size)
                {
                    printf("fwrite failed-> write:%u, pkt_size:%u\n", size, pkt->size);
                }
                av_packet_unref(pkt);
            }
            if(out_pkt_count >= 2)
            {
                printf("cur pkt(size:%d) only get 1 out pkt, it get %d pkts\n",
                       input_size, out_pkt_count);
            }
#else       // TS流可以直接写入
            size_t size = fwrite(pkt->data, 1, pkt->size, outfp);
            if(size != pkt->size)
            {
                printf("fwrite failed-> write:%u, pkt_size:%u\n", size, pkt->size);
            }
            av_packet_unref(pkt);
#endif
        }
        else
        {
            if(ret == 0)
                av_packet_unref(pkt);        // 释放内存
        }
    }
    if(outfp)
        fclose(outfp);
    if(bsf_ctx)
        av_bsf_free(&bsf_ctx);
    if(pkt)
        av_packet_free(&pkt);
    if(ifmt_ctx)
        avformat_close_input(&ifmt_ctx);
    printf("finish\n");

    return 0;
```

## 4.3 FLV

[学习文档](D:\课程资料\音视频\笔记\FLV格式分析-FLV封装格式剖析.pdf)

查看封装flv封装信息：meida打开文件，调试改为Details 10，再次将文件拖入

### 4.3.1 AAC流封装格式

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231117170036936.png" alt="image-20231117170036936" style="zoom: 67%;" />

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231117161557650.png" alt="image-20231117161557650" style="zoom:67%;" />

### 4.3.2 AVC流封装格式

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231117170110904.png" alt="image-20231117170110904" style="zoom:67%;" />



<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231117165836337.png" alt="image-20231117165836337" style="zoom:67%;" />

### 4.3.3 flv解析

[flv_parser项目位置](D:\课程资料\音视频\笔记\07-04-flv_parser_cplus)

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231120234342427.png" alt="image-20231120234342427" style="zoom:67%;" />



## 4.4 MP4

* [MP4box](https://gpac.github.io/mp4box.js/test/filereader.html)

### 4.4.1 mdhd

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231122232118494.png" alt="image-20231122232118494" style="zoom:67%;" />

在音视频领域，`duration`参数通常与`timescale`结合使用，这是因为媒体文件可能采用时间刻度（timescale）来更精确地表示持续时间。这是为了支持更细粒度的时间控制。

1. **Duration（持续时间）：** 表示媒体文件的实际时长，以时间单位（通常是秒）表示。

2. **Timescale（时间刻度）：** 是一个比例因子，它定义了每秒包含多少个时间单元。将`duration`乘以`timescale`可以得到实际的时长。这允许表示更精确的时间，特别是当需要使用小数或分数来表示时长时。

举个例子，如果`duration`是10秒，而`timescale`是1000，那么实际时长就是：

\[ \text{实际时长} = \text{duration} \times \text{timescale} = 10 \, \text{秒} \times 1000 = 10000 \, \text{毫秒} \]

这种结合使用的方式允许更灵活地表示媒体文件的时长，特别是在需要高精度时间控制的应用中，比如音视频编辑或同步任务。

在处理音视频文件时，理解`duration`和`timescale`的关系有助于确保正确地解释和使用这些参数，以便在你的应用中实现准确的时间控制。

### 4.4.2 FLV/MP4  解封装 

[代码位置](D:\QT\project\audio_vedio_learn\FLV_demux_h264_aac)

```cpp
// 传入MP4文件 --demux-- h264 aac
// mp4文件解析stream，再将stream根据h264 aac协议封装保存，即得到单独的h264,aac文件

#include <stdio.h>
#include <libavformat/avformat.h>
#include <libavformat/avformat.h>


void deal_audio(AVFormatContext *ifmt_ctx, int audio_index, AVPacket *pkt, FILE *fd);
AVBSFContext *createAVBitStreamFilter(AVFormatContext *ifmt_ctx, int videoindex);
void deal_vedio(AVBSFContext *bsf_ctx, AVPacket *pkt, FILE *h264_fd);
int adts_header(char * const p_adts_header, const int data_length,
                const int profile, const int samplerate,
                const int channels);

int main(int argc, char **argv){
    if(argc != 4) {
        printf("format: [in.mp4] [out1.h264] [out2.aac]");
        return -1;
    }

    char *inFilePath = argv[1];
    char *h264FilePath = argv[2];
    char *aacFilePath = argv[3];
    FILE *aac_fd = fopen(aacFilePath, "wb");
    if (!aac_fd)
    {
        printf("%s open failed! \n",aacFilePath);
        return -1;
    }
    FILE *h264_fd = fopen(h264FilePath, "wb");
    if (!aac_fd)
    {
        printf("%s open failed! \n",h264FilePath);
        return -1;
    }

    char error[1024] = {0};

    // partI  传入MP4 -> demux -> stream
    AVFormatContext *fmt_ctx = avformat_alloc_context();

    int ret = avformat_open_input(&fmt_ctx, inFilePath, NULL, NULL);
    if(ret < 0) {
        av_strerror(ret, error, sizeof (error)-1);
        printf("open %s failed: %s\n", inFilePath, error);
        return -1;
    }

    ret = avformat_find_stream_info(fmt_ctx, NULL);
    if(ret < 0) {
        av_strerror(ret, error, sizeof (error) - 1);
        printf("find %s info failed: %s\n", inFilePath, error);
        return -1;
    }

    int audio_index = av_find_best_stream(fmt_ctx, AVMEDIA_TYPE_AUDIO, -1, -1, NULL, -1);
    if(audio_index < 0) {
        printf("Didn't find a audio stream.\n");
        avformat_close_input(&fmt_ctx);
        return -1;
    }

    int vedio_index = av_find_best_stream(fmt_ctx, AVMEDIA_TYPE_VIDEO, -1, -1, NULL, -1); // flag = 0
    if(vedio_index < 0) {
        printf("Didn't find a video stream.\n");
        avformat_close_input(&fmt_ctx);
        return -1;
    }

    // part II  stream -> packet
    // part III packet -> h264 aac, 放在两个函数内执行

    AVPacket *pkt = av_packet_alloc();
    av_init_packet(pkt);

    // 创建h264bit过滤器,在deal_vedio中使用
    // AVBSFContext *h264_filter_ctx = createAVBitStreamFilter(fmt_ctx, vedio_index);


    const AVBitStreamFilter *bsfilter = av_bsf_get_by_name("h264_mp4toannexb");
    AVBSFContext *bsf_ctx = NULL;
    // 2 初始化过滤器上下文
    av_bsf_alloc(bsfilter, &bsf_ctx); //AVBSFContext;
    // 3 添加解码器属性
    avcodec_parameters_copy(bsf_ctx->par_in, fmt_ctx->streams[vedio_index]->codecpar);
    av_bsf_init(bsf_ctx);


    while (av_read_frame(fmt_ctx, pkt) >= 0 ) {
        // audio
        if(pkt->stream_index == audio_index) {
           deal_audio(fmt_ctx, audio_index, pkt, aac_fd);
        }
        // vedio
        else if(pkt->stream_index == vedio_index) {
           //deal_vedio(h264_filter_ctx, pkt, h264_fd);
           deal_vedio(bsf_ctx, pkt, h264_fd);
        }

        else {
            av_packet_unref(pkt);
        }
    }

    // 重置
    av_packet_unref(pkt);
    // 执行完毕，释放资源
    avformat_close_input(&fmt_ctx);

    printf(" success !\n");
    return 0;

}

// =================================================== 处理audio =======================================================
const int sampling_frequencies[] = {
    96000,  // 0x0
    88200,  // 0x1
    64000,  // 0x2
    48000,  // 0x3
    44100,  // 0x4
    32000,  // 0x5
    24000,  // 0x6
    22050,  // 0x7
    16000,  // 0x8
    12000,  // 0x9
    11025,  // 0xa
    8000   // 0xb
    // 0xc d e f是保留的
};
int adts_header(char * const p_adts_header, const int data_length,
                const int profile, const int samplerate,
                const int channels)
{

    // 设置音频采样率，若输入samp不符合，则默认为48000hz
    int sampling_frequency_index = 3; // 默认使用48000hz
    int adtsLen = data_length + 7;

    int frequencies_size = sizeof(sampling_frequencies) / sizeof(sampling_frequencies[0]);
    int i = 0;
    for(i = 0; i < frequencies_size; i++)
    {
        if(sampling_frequencies[i] == samplerate)
        {
            sampling_frequency_index = i;
            break;
        }
    }
    if(i >= frequencies_size)
    {
        printf("unsupport samplerate:%d\n", samplerate);
        return -1;
    }

    p_adts_header[0] = 0xff;         //syncword:0xfff                          高8bits
    p_adts_header[1] = 0xf0;         //syncword:0xfff                          低4bits
    p_adts_header[1] |= (0 << 3);    //MPEG Version:0 for MPEG-4,1 for MPEG-2  1bit
    p_adts_header[1] |= (0 << 1);    //Layer:0                                 2bits
    p_adts_header[1] |= 1;           //protection absent:1                     1bit

    p_adts_header[2] = (profile)<<6;            //profile:profile               2bits
    p_adts_header[2] |= (sampling_frequency_index & 0x0f)<<2; //sampling frequency index:sampling_frequency_index  4bits
    p_adts_header[2] |= (0 << 1);             //private bit:0                   1bit
    p_adts_header[2] |= (channels & 0x04)>>2; //channel configuration:channels  高1bit

    p_adts_header[3] = (channels & 0x03)<<6; //channel configuration:channels 低2bits
    p_adts_header[3] |= (0 << 5);               //original：0                1bit
    p_adts_header[3] |= (0 << 4);               //home：0                    1bit
    p_adts_header[3] |= (0 << 3);               //copyright id bit：0        1bit
    p_adts_header[3] |= (0 << 2);               //copyright id start：0      1bit
    p_adts_header[3] |= ((adtsLen & 0x1800) >> 11);           //frame length：value   高2bits

    p_adts_header[4] = (uint8_t)((adtsLen & 0x7f8) >> 3);     //frame length:value    中间8bits
    p_adts_header[5] = (uint8_t)((adtsLen & 0x7) << 5);       //frame length:value    低3bits
    p_adts_header[5] |= 0x1f;                                 //buffer fullness:0x7ff 高5bits
    p_adts_header[6] = 0xfc;      //‭11111100‬       //buffer fullness:0x7ff 低6bits
    // number_of_raw_data_blocks_in_frame：
    //    表示ADTS帧中有number_of_raw_data_blocks_in_frame + 1个AAC原始帧。

    return 0;
}

void deal_audio(AVFormatContext *ifmt_ctx, int audio_index, AVPacket *pkt, FILE *fd){
    char adts_header_buf[7] = {0};
    adts_header(adts_header_buf, pkt->size,
                ifmt_ctx->streams[audio_index]->codecpar->profile,
                ifmt_ctx->streams[audio_index]->codecpar->sample_rate,
                ifmt_ctx->streams[audio_index]->codecpar->channels);


    fwrite(adts_header_buf, 1, 7, fd);  // 写adts header , ts流不适用，ts流分离出来的packet带了adts header
    int len = fwrite( pkt->data, 1, pkt->size, fd);   // 写adts data
    if(len != pkt->size)
    {
        av_log(NULL, AV_LOG_DEBUG, "warning, length of writed data isn't equal pkt.size(%d, %d)\n",
               len,
               pkt->size);
    }
    return;
}

// =================================================== 处理vedio =======================================================
AVBSFContext *createAVBitStreamFilter(AVFormatContext *ifmt_ctx, int videoindex){
    char errors[1024] = {0};
    const AVBitStreamFilter *bsfilter = av_bsf_get_by_name("h264_mp4toannexb");      // 对应面向对象的方法
    if(!bsfilter) {
        avformat_close_input(&ifmt_ctx);
        printf("av_bsf_get_by_name h264_mp4toannexb failed\n");
        return NULL;
    }
    AVBSFContext *bsf_ctx = NULL;        // 对应面向对象的变量
    int ret = av_bsf_alloc(bsfilter, &bsf_ctx);
    if(ret < 0) {
        av_strerror(ret, errors, 1024);
        printf("av_bsf_alloc failed:%s\n", errors);
        avformat_close_input(&ifmt_ctx);
        return NULL;
    }
    ret = avcodec_parameters_copy(bsf_ctx->par_in, ifmt_ctx->streams[videoindex]->codecpar);
    if(ret < 0) {
        av_strerror(ret, errors, 1024);
        printf("avcodec_parameters_copy failed:%s\n", errors);
        avformat_close_input(&ifmt_ctx);
        av_bsf_free(&bsf_ctx);
        return NULL;
    }
    ret = av_bsf_init(bsf_ctx);
    if(ret < 0) {
        av_strerror(ret, errors, 1024);
        printf("av_bsf_init failed:%s\n", errors);
        avformat_close_input(&ifmt_ctx);
        av_bsf_free(&bsf_ctx);
        return NULL;
    }
}

void deal_vedio(AVBSFContext *bsf_ctx, AVPacket *pkt, FILE *h264_fd){
    int ret = av_bsf_send_packet(bsf_ctx, pkt); // 内部把我们传入的buf转移到自己bsf内部
    printf("hello!");
//    if(ret < 0) {       // 基本不会进入该逻辑
//        av_strerror(ret, errors, ERROR_STRING_SIZE);
//        printf("av_bsf_send_packet failed:%s\n", errors);
//        av_packet_unref(pkt);
//        continue;
//    }
//            av_packet_unref(pkt); // 这里不需要去释放内存
    while (1) {
        ret = av_bsf_receive_packet(bsf_ctx, pkt);
        if(ret != 0) {
            break;
        }
        size_t size = fwrite(pkt->data, 1, pkt->size, h264_fd);
        if(size != pkt->size)
        {
            av_log(NULL, AV_LOG_DEBUG, "h264 warning, length of writed data isn't equal pkt->size(%d, %d)\n",
                   size,
                   pkt->size);
        }
        av_packet_unref(pkt);
    }
}

```

## 4.5 PCM

[参考文件](E:\typora索引文件\ffmpeg\08-01-音频编码实战.pdf)

### 4.5.1 理论

### 4.5.2 pcm编码

```cpp
/**
* @projectName   08-01-encode_audio
* @brief         音频编码
*               从本地读取PCM数据进行AAC编码
*           1. 输入PCM格式问题，通过AVCodec的sample_fmts参数获取具体的格式支持
*           （1）默认的aac编码器输入的PCM格式为:AV_SAMPLE_FMT_FLTP
*           （2）libfdk_aac编码器输入的PCM格式为AV_SAMPLE_FMT_S16.
*           2. 支持的采样率，通过AVCodec的supported_samplerates可以获取
* @date          2022-11-28
*/

#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>

#include <libavcodec/avcodec.h>

#include <libavutil/channel_layout.h>
#include <libavutil/common.h>
#include <libavutil/frame.h>
#include <libavutil/samplefmt.h>
#include <libavutil/opt.h>

/* 检测该编码器是否支持该采样格式 */
static int check_sample_fmt(const AVCodec *codec, enum AVSampleFormat sample_fmt)
{
    const enum AVSampleFormat *p = codec->sample_fmts;

    while (*p != AV_SAMPLE_FMT_NONE) { // 通过AV_SAMPLE_FMT_NONE作为结束符
        if (*p == sample_fmt)
            return 1;
        p++;
    }
    return 0;
}

/* 检测该编码器是否支持该采样率 */
static int check_sample_rate(const AVCodec *codec, const int sample_rate)
{
    const int *p = codec->supported_samplerates;
    while (*p != 0)  {// 0作为退出条件，比如libfdk-aacenc.c的aac_sample_rates
        printf("%s support %dhz\n", codec->name, *p);
        if (*p == sample_rate)
            return 1;
        p++;
    }
    return 0;
}

/* 检测该编码器是否支持该采样率, 该函数只是作参考 */
static int check_channel_layout(const AVCodec *codec, const uint64_t channel_layout)
{
    // 不是每个codec都给出支持的channel_layout
    const uint64_t *p = codec->channel_layouts;
    if(!p) {
        printf("the codec %s no set channel_layouts\n", codec->name);
        return 1;
    }
    while (*p != 0) { // 0作为退出条件，比如libfdk-aacenc.c的aac_channel_layout
        printf("%s support channel_layout %d\n", codec->name, *p);
        if (*p == channel_layout)
            return 1;
        p++;
    }
    return 0;
}


static void get_adts_header(AVCodecContext *ctx, uint8_t *adts_header, int aac_length)
{
    uint8_t freq_idx = 0;    //0: 96000 Hz  3: 48000 Hz 4: 44100 Hz
    switch (ctx->sample_rate) {
        case 96000: freq_idx = 0; break;
        case 88200: freq_idx = 1; break;
        case 64000: freq_idx = 2; break;
        case 48000: freq_idx = 3; break;
        case 44100: freq_idx = 4; break;
        case 32000: freq_idx = 5; break;
        case 24000: freq_idx = 6; break;
        case 22050: freq_idx = 7; break;
        case 16000: freq_idx = 8; break;
        case 12000: freq_idx = 9; break;
        case 11025: freq_idx = 10; break;
        case 8000: freq_idx = 11; break;
        case 7350: freq_idx = 12; break;
        default: freq_idx = 4; break;
    }
    uint8_t chanCfg = ctx->channels;
    uint32_t frame_length = aac_length + 7;
    adts_header[0] = 0xFF;
    adts_header[1] = 0xF1;
    adts_header[2] = ((ctx->profile) << 6) + (freq_idx << 2) + (chanCfg >> 2);
    adts_header[3] = (((chanCfg & 3) << 6) + (frame_length  >> 11));
    adts_header[4] = ((frame_length & 0x7FF) >> 3);
    adts_header[5] = (((frame_length & 7) << 5) + 0x1F);
    adts_header[6] = 0xFC;
}
/*
*
*/
static int encode(AVCodecContext *ctx, AVFrame *frame, AVPacket *pkt, FILE *output)
{
    int ret;

    /* send the frame for encoding */
    ret = avcodec_send_frame(ctx, frame);
    if (ret < 0) {
        fprintf(stderr, "Error sending the frame to the encoder\n");
        return -1;
    }

    /* read all the available output packets (in general there may be any number of them */
    // 编码和解码都是一样的，都是send 1次，然后receive多次, 直到AVERROR(EAGAIN)或者AVERROR_EOF
    while (ret >= 0) {
        ret = avcodec_receive_packet(ctx, pkt);
        if (ret == AVERROR(EAGAIN) || ret == AVERROR_EOF) {
            return 0;
        } else if (ret < 0) {
            fprintf(stderr, "Error encoding audio frame\n");
            return -1;
        }

        size_t len = 0;
        printf("ctx->flags:0x%x & AV_CODEC_FLAG_GLOBAL_HEADER:0x%x, name:%s\n",ctx->flags, ctx->flags & AV_CODEC_FLAG_GLOBAL_HEADER, ctx->codec->name);
        if((ctx->flags & AV_CODEC_FLAG_GLOBAL_HEADER)) {
            // 需要额外的adts header写入
            uint8_t aac_header[7];
            get_adts_header(ctx, aac_header, pkt->size);
            len = fwrite(aac_header, 1, 7, output);
            if(len != 7) {
                fprintf(stderr, "fwrite aac_header failed\n");
                return -1;
            }
        }
        len = fwrite(pkt->data, 1, pkt->size, output);
        if(len != pkt->size) {
            fprintf(stderr, "fwrite aac data failed\n");
            return -1;
        }
        /* 是否需要释放数据? avcodec_receive_packet第一个调用的就是 av_packet_unref
        * 所以我们不用手动去释放，这里有个问题，不能将pkt直接插入到队列，因为编码器会释放数据
        * 可以新分配一个pkt, 然后使用av_packet_move_ref转移pkt对应的buffer
        */
        // av_packet_unref(pkt);
    }
    return -1;
}

/*
 * 这里只支持2通道的转换
*/
void f32le_convert_to_fltp(float *f32le, float *fltp, int nb_samples) {
    float *fltp_l = fltp;   // 左通道
    float *fltp_r = fltp + nb_samples;   // 右通道
    for(int i = 0; i < nb_samples; i++) {
        fltp_l[i] = f32le[i*2];     // 0 1   - 2 3
        fltp_r[i] = f32le[i*2+1];   //
    }
}
/*
 * 提取测试文件：
 * （1）s16格式：ffmpeg -i buweishui.aac -ar 48000 -ac 2 -f s16le 48000_2_s16le.pcm
 * （2）flt格式：ffmpeg -i buweishui.aac -ar 48000 -ac 2 -f f32le 48000_2_f32le.pcm
 *      ffmpeg只能提取packed格式的PCM数据，在编码时候如果输入要为fltp则需要进行转换
 * 测试范例:
 * （1）48000_2_s16le.pcm libfdk_aac.aac libfdk_aac  // 如果编译的时候没有支持fdk aac则提示找不到编码器
 * （2）48000_2_f32le.pcm aac.aac aac // 我们这里只测试aac编码器，不测试fdkaac
*/


int main(int argc, char **argv)
{
    char *in_pcm_file = NULL;
    char *out_aac_file = NULL;
    FILE *infile = NULL;
    FILE *outfile = NULL;

    const AVCodec *codec = NULL;
    AVCodecContext *codec_ctx= NULL;

    AVFrame *frame = NULL;
    AVPacket *pkt = NULL;

    int ret = 0;
    int force_codec = 0;     // 强制使用指定的编码
    char *codec_name = NULL;

    if (argc < 3) {
        fprintf(stderr, "Usage: %s <input_file out_file [codec_name]>, argc:%d\n",
                argv[0], argc);
        return 0;
    }
    in_pcm_file = argv[1];      // 输入PCM文件
    out_aac_file = argv[2];     // 输出的AAC文件

    enum AVCodecID codec_id = AV_CODEC_ID_AAC;

    if(4 == argc) {
        if(strcmp(argv[3], "libfdk_aac") == 0) {
            force_codec = 1;     // 强制使用 libfdk_aac
            codec_name = "libfdk_aac";
        } else if(strcmp(argv[3], "aac") == 0) {
            force_codec = 1;
            codec_name = "aac";
        }
    }
    if(force_codec)
        printf("force codec name: %s\n", codec_name);
    else
        printf("default codec name: %s\n", "aac");

    if(force_codec == 0) { // 没有强制设置编码器
        codec = avcodec_find_encoder(codec_id); // 按ID查找则缺省的aac encode为aacenc.c
    } else {
        // 按名字查找指定的encode,对应AVCodec的name字段
        codec = avcodec_find_encoder_by_name(codec_name);
    }
    if (!codec) {
        fprintf(stderr, "Codec not found\n");
        exit(1);
    }

    // 创建编码器上下文
    codec_ctx = avcodec_alloc_context3(codec);
    if (!codec_ctx) {
        fprintf(stderr, "Could not allocate audio codec context\n");
        exit(1);
    }

    // 配置编码器上下文
    codec_ctx->codec_id = codec_id;
    codec_ctx->codec_type = AVMEDIA_TYPE_AUDIO;
    codec_ctx->channel_layout = AV_CH_LAYOUT_STEREO;
    codec_ctx->sample_rate    = 48000; //48000;
    codec_ctx->channels       = av_get_channel_layout_nb_channels(codec_ctx->channel_layout);
    codec_ctx->bit_rate = 128*1024;
    codec_ctx->profile = FF_PROFILE_AAC_LOW;    //

    if(strcmp(codec->name, "aac") == 0) {
        codec_ctx->sample_fmt = AV_SAMPLE_FMT_FLTP;
    } else if(strcmp(codec->name, "libfdk_aac") == 0) {
        codec_ctx->sample_fmt = AV_SAMPLE_FMT_S16;
    } else {
        codec_ctx->sample_fmt = AV_SAMPLE_FMT_FLTP;
    }

    /* 检测支持采样格式支持情况 */
    if (!check_sample_fmt(codec, codec_ctx->sample_fmt)) {
        fprintf(stderr, "Encoder does not support sample format %s",
                av_get_sample_fmt_name(codec_ctx->sample_fmt));
        exit(1);
    }
    if (!check_sample_rate(codec, codec_ctx->sample_rate)) {
        fprintf(stderr, "Encoder does not support sample rate %d", codec_ctx->sample_rate);
        exit(1);
    }
    if (!check_channel_layout(codec, codec_ctx->channel_layout)) {
        fprintf(stderr, "Encoder does not support channel layout %lu", codec_ctx->channel_layout);
        exit(1);
    }

    printf("\n\nAudio encode config\n");
    printf("bit_rate:%ldkbps\n", codec_ctx->bit_rate/1024);
    printf("sample_rate:%d\n", codec_ctx->sample_rate);
    printf("sample_fmt:%s\n", av_get_sample_fmt_name(codec_ctx->sample_fmt));


    printf("channels:%d\n", codec_ctx->channels);
    // frame_size是在avcodec_open2后进行关联
    printf("1 frame_size:%d\n", codec_ctx->frame_size);
    codec_ctx->flags = AV_CODEC_FLAG_GLOBAL_HEADER;  //ffmpeg默认的aac是不带adts，而fdk_aac默认带adts，这里我们强制不带


    /* 将编码器上下文和编码器进行关联 */
    if (avcodec_open2(codec_ctx, codec, NULL) < 0) {
        fprintf(stderr, "Could not open codec\n");
        exit(1);
    }
    printf("2 frame_size:%d\n\n", codec_ctx->frame_size); // 决定每次到底送多少个采样点
    // 打开输入和输出文件
    infile = fopen(in_pcm_file, "rb");
    if (!infile) {
        fprintf(stderr, "Could not open %s\n", in_pcm_file);
        exit(1);
    }
    outfile = fopen(out_aac_file, "wb");
    if (!outfile) {
        fprintf(stderr, "Could not open %s\n", out_aac_file);
        exit(1);
    }

    /* packet for holding encoded output */
    pkt = av_packet_alloc();
    if (!pkt)
    {
        fprintf(stderr, "could not allocate the packet\n");
        exit(1);
    }

    /* frame containing input raw audio */
    frame = av_frame_alloc();
    if (!frame)
    {
        fprintf(stderr, "Could not allocate audio frame\n");
        exit(1);
    }
    /* 每次送多少数据给编码器由：
     *  (1)frame_size(每帧单个通道的采样点数);
     *  (2)sample_fmt(采样点格式);
     *  (3)channel_layout(通道布局情况);
     * 3要素决定
     */

    // 设置输入音频帧参数
    frame->nb_samples     = codec_ctx->frame_size; // 帧大小
    frame->format         = codec_ctx->sample_fmt;
    frame->channel_layout = codec_ctx->channel_layout;
    frame->channels = av_get_channel_layout_nb_channels(frame->channel_layout);
    printf("frame nb_samples:%d\n", frame->nb_samples);
    printf("frame sample_fmt:%d\n", frame->format);
    printf("frame channel_layout:%lu\n\n", frame->channel_layout);
    /* 为frame分配buffer */

    // 分配输出缓冲区
    ret = av_frame_get_buffer(frame, 0);
    if (ret < 0)
    {
        fprintf(stderr, "Could not allocate audio data buffers\n");
        exit(1);
    }
    // 计算出每一帧的数据 单个采样点的字节 * 通道数目 * 每帧采样点数量
    int frame_bytes = av_get_bytes_per_sample(frame->format) \
            * frame->channels \
            * frame->nb_samples;
    printf("frame_bytes %d\n", frame_bytes);


    uint8_t *pcm_buf = (uint8_t *)malloc(frame_bytes);
    if(!pcm_buf) {
        printf("pcm_buf malloc failed\n");
        return 1;
    }
    uint8_t *pcm_temp_buf = (uint8_t *)malloc(frame_bytes);
    if(!pcm_temp_buf) {
        printf("pcm_temp_buf malloc failed\n");
        return 1;
    }
    int64_t pts = 0;
    printf("start enode\n");
    for (;;) {
        memset(pcm_buf, 0, frame_bytes);

        // 从infile文件中向buff读入数据
        size_t read_bytes = fread(pcm_buf, 1, frame_bytes, infile);
        if(read_bytes <= 0) {
            printf("read file finish\n");
            break;
//            fseek(infile,0,SEEK_SET);
//            fflush(outfile);
//            continue;
        }

        /* 确保该frame可写, 如果编码器内部保持了内存参考计数，则需要重新拷贝一个备份
            目的是新写入的数据和编码器保存的数据不能产生冲突
        */
        ret = av_frame_make_writable(frame);
        if(ret != 0)
            printf("av_frame_make_writable failed, ret = %d\n", ret);

        if(AV_SAMPLE_FMT_S16 == frame->format) {
            // 将读取到的PCM数据填充到frame去，但要注意格式的匹配, 是planar还是packed都要区分清楚
            ret = av_samples_fill_arrays(frame->data, frame->linesize,
                                   pcm_buf, frame->channels,
                                   frame->nb_samples, frame->format, 0);
        } else {
            // 将读取到的PCM数据填充到frame去，但要注意格式的匹配, 是planar还是packed都要区分清楚
            // 将本地的f32le packed模式的数据转为float palanar
            memset(pcm_temp_buf, 0, frame_bytes);
            f32le_convert_to_fltp((float *)pcm_buf, (float *)pcm_temp_buf, frame->nb_samples);
            ret = av_samples_fill_arrays(frame->data, frame->linesize,
                                   pcm_temp_buf, frame->channels,
                                   frame->nb_samples, frame->format, 0);
        }

        // 设置pts
        pts += frame->nb_samples;
        frame->pts = pts;

        // 使用采样率作为pts的单位，具体换算成秒 pts*1/采样率
        // 解码
        ret = encode(codec_ctx, frame, pkt, outfile);
        if(ret < 0) {
            printf("encode failed\n");
            break;
        }
    }

    /* 冲刷编码器 */
    encode(codec_ctx, NULL, pkt, outfile);

    // 关闭文件
    fclose(infile);
    fclose(outfile);

    // 释放内存
    if(pcm_buf) {
        free(pcm_buf);
    }
    if (pcm_temp_buf) {
        free(pcm_temp_buf);
    }
    av_frame_free(&frame);
    av_packet_free(&pkt);
    avcodec_free_context(&codec_ctx);
    printf("main finish, please enter Enter and exit\n");
    getchar();
    return 0;
}
```



## 4.6 YUV

[资料](E:\typora索引文件\ffmpeg\08-04-音视频FLV合成实战.pdf)

### 4.6.1 yuv编码

```cpp
/**
* @projectName   08-02-encode_video
* @brief         视频编码，从本地读取YUV数据进行H264编码
* @author        Liao Qingfu
* @date          2020-04-16
*/
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#include <libavcodec/avcodec.h>
#include <libavutil/time.h>
#include <libavutil/opt.h>
#include <libavutil/imgutils.h>

int64_t get_time()
{
    return av_gettime_relative() / 1000;  // 换算成毫秒
}
static int encode(AVCodecContext *enc_ctx, AVFrame *frame, AVPacket *pkt,
                  FILE *outfile)
{
    int ret;

    /* send the frame to the encoder */
    if (frame)
        printf("Send frame %3"PRId64"\n", frame->pts);
    /* 通过查阅代码，使用x264进行编码时，具体缓存帧是在x264源码进行，
     * 不会增加avframe对应buffer的reference*/
    ret = avcodec_send_frame(enc_ctx, frame);
    if (ret < 0)
    {
        fprintf(stderr, "Error sending a frame for encoding\n");
        return -1;
    }

    while (ret >= 0)
    {
        ret = avcodec_receive_packet(enc_ctx, pkt);
        if (ret == AVERROR(EAGAIN) || ret == AVERROR_EOF) {
            return 0;
        } else if (ret < 0) {
            fprintf(stderr, "Error encoding audio frame\n");
            return -1;
        }

        if(pkt->flags & AV_PKT_FLAG_KEY)
            printf("Write packet flags:%d pts:%3"PRId64" dts:%3"PRId64" (size:%5d)\n",
               pkt->flags, pkt->pts, pkt->dts, pkt->size);
        if(!pkt->flags)
            printf("Write packet flags:%d pts:%3"PRId64" dts:%3"PRId64" (size:%5d)\n",
               pkt->flags, pkt->pts, pkt->dts, pkt->size);
        fwrite(pkt->data, 1, pkt->size, outfile);
    }
    return 0;
}
/**
 * @brief 提取测试文件：ffmpeg -i test_1280x720.flv -t 5 -r 25 -pix_fmt yuv420p yuv420p_1280x720.yuv
 *           参数输入: yuv420p_1280x720.yuv yuv420p_1280x720.h264 libx264
 * @param argc
 * @param argv
 * @return
 */
int main(int argc, char **argv)
{
    char *in_yuv_file = NULL;
    char *out_h264_file = NULL;
    FILE *infile = NULL;
    FILE *outfile = NULL;

    const char *codec_name = NULL;
    const AVCodec *codec = NULL;
    AVCodecContext *codec_ctx= NULL;
    AVFrame *frame = NULL;
    AVPacket *pkt = NULL;
    int ret = 0;

    if (argc < 4) {
        fprintf(stderr, "Usage: %s <input_file out_file codec_name >, argc:%d\n",
                argv[0], argc);
        return 0;
    }
    in_yuv_file = argv[1];      // 输入YUV文件
    out_h264_file = argv[2];
    codec_name = argv[3];

    /* 查找指定的编码器 */
    codec = avcodec_find_encoder_by_name(codec_name);
    if (!codec) {
        fprintf(stderr, "Codec '%s' not found\n", codec_name);
        exit(1);
    }


    // 申请编码器上下文
    codec_ctx = avcodec_alloc_context3(codec);
    if (!codec_ctx) {
        fprintf(stderr, "Could not allocate video codec context\n");
        exit(1);
    }


    // 设置编码器上下文
    /* 设置分辨率*/
    codec_ctx->width = 1280;
    codec_ctx->height = 720;
    /* 设置time base */
    codec_ctx->time_base = (AVRational){1, 25};
    codec_ctx->framerate = (AVRational){25, 1};
    /* 设置I帧间隔
     * 如果frame->pict_type设置为AV_PICTURE_TYPE_I, 则忽略gop_size的设置，一直当做I帧进行编码
     */
    codec_ctx->gop_size = 25;   // I帧间隔
    codec_ctx->max_b_frames = 2; // 如果不想包含B帧则设置为0
    codec_ctx->pix_fmt = AV_PIX_FMT_YUV420P;
    //
    if (codec->id == AV_CODEC_ID_H264) {
        // 相关的参数可以参考libx264.c的 AVOption options
        // ultrafast all encode time:2270ms
        // medium all encode time:5815ms
        // veryslow all encode time:19836ms
        ret = av_opt_set(codec_ctx->priv_data, "preset", "medium", 0);
        if(ret != 0) {
            printf("av_opt_set preset failed\n");
        }
        ret = av_opt_set(codec_ctx->priv_data, "profile", "main", 0); // 默认是high
        if(ret != 0) {
            printf("av_opt_set profile failed\n");
        }
        ret = av_opt_set(codec_ctx->priv_data, "tune","zerolatency",0); // 直播是才使用该设置
//        ret = av_opt_set(codec_ctx->priv_data, "tune","film",0); //  画质film
        if(ret != 0) {
            printf("av_opt_set tune failed\n");
        }
    }
    /* 设置bitrate */
    codec_ctx->bit_rate = 3000000;
//    codec_ctx->rc_max_rate = 3000000;
//    codec_ctx->rc_min_rate = 3000000;
//    codec_ctx->rc_buffer_size = 2000000;
//    codec_ctx->thread_count = 4;  // 开了多线程后也会导致帧输出延迟, 需要缓存thread_count帧后再编程。
//    codec_ctx->thread_type = FF_THREAD_FRAME; // 并 设置为FF_THREAD_FRAME
    /* 对于H264 AV_CODEC_FLAG_GLOBAL_HEADER  设置则只包含I帧，此时sps pps需要从codec_ctx->extradata读取
     *  不设置则每个I帧都带 sps pps sei
     */


//    codec_ctx->flags |= AV_CODEC_FLAG_GLOBAL_HEADER; // 存本地文件时不要去设置




    /* 将codec_ctx和codec进行绑定 */
    ret = avcodec_open2(codec_ctx, codec, NULL);
    if (ret < 0) {
        fprintf(stderr, "Could not open codec: %s\n", av_err2str(ret));
        exit(1);
    }
    printf("thread_count: %d, thread_type:%d\n", codec_ctx->thread_count, codec_ctx->thread_type);
    // 打开输入和输出文件
    infile = fopen(in_yuv_file, "rb");
    if (!infile) {
        fprintf(stderr, "Could not open %s\n", in_yuv_file);
        exit(1);
    }
    outfile = fopen(out_h264_file, "wb");
    if (!outfile) {
        fprintf(stderr, "Could not open %s\n", out_h264_file);
        exit(1);
    }

    // 分配pkt和frame
    pkt = av_packet_alloc();
    if (!pkt) {
        fprintf(stderr, "Could not allocate video frame\n");
        exit(1);
    }
    frame = av_frame_alloc();
    if (!frame) {
        fprintf(stderr, "Could not allocate video frame\n");
        exit(1);
    }

    // 为frame分配buffer
    frame->format = codec_ctx->pix_fmt;
    frame->width  = codec_ctx->width;
    frame->height = codec_ctx->height;
    ret = av_frame_get_buffer(frame, 0);
    if (ret < 0) {
        fprintf(stderr, "Could not allocate the video frame data\n");
        exit(1);
    }


    // 计算出每一帧的数据 像素格式 * 宽 * 高
    // 1382400
    int frame_bytes = av_image_get_buffer_size(frame->format, frame->width,
                                               frame->height, 1);
    printf("frame_bytes %d\n", frame_bytes);
    uint8_t *yuv_buf = (uint8_t *)malloc(frame_bytes);
    if(!yuv_buf) {
        printf("yuv_buf malloc failed\n");
        return 1;
    }
    int64_t begin_time = get_time();
    int64_t end_time = begin_time;
    int64_t all_begin_time = get_time();
    int64_t all_end_time = all_begin_time;
    int64_t pts = 0;
    printf("start enode\n");
    for (;;) {
        memset(yuv_buf, 0, frame_bytes);
        size_t read_bytes = fread(yuv_buf, 1, frame_bytes, infile);
        if(read_bytes <= 0) {
            printf("read file finish\n");
            break;
        }
        /* 确保该frame可写, 如果编码器内部保持了内存参考计数，则需要重新拷贝一个备份
            目的是新写入的数据和编码器保存的数据不能产生冲突
        */
        int frame_is_writable = 1;
        if(av_frame_is_writable(frame) == 0) { // 这里只是用来测试
            printf("the frame can't write, buf:%p\n", frame->buf[0]);
            if(frame->buf && frame->buf[0])        // 打印referenc-counted，必须保证传入的是有效指针
                printf("ref_count1(frame) = %d\n", av_buffer_get_ref_count(frame->buf[0]));
            frame_is_writable = 0;
        }
        ret = av_frame_make_writable(frame);
        if(frame_is_writable == 0) {  // 这里只是用来测试
            printf("av_frame_make_writable, buf:%p\n", frame->buf[0]);
            if(frame->buf && frame->buf[0])        // 打印referenc-counted，必须保证传入的是有效指针
                printf("ref_count2(frame) = %d\n", av_buffer_get_ref_count(frame->buf[0]));
        }
        if(ret != 0) {
            printf("av_frame_make_writable failed, ret = %d\n", ret);
            break;
        }
        int need_size = av_image_fill_arrays(frame->data, frame->linesize, yuv_buf,
                                             frame->format,
                                             frame->width, frame->height, 1);
        if(need_size != frame_bytes) {
            printf("av_image_fill_arrays failed, need_size:%d, frame_bytes:%d\n",
                   need_size, frame_bytes);
            break;
        }
         pts += 40;
        // 设置pts
        frame->pts = pts;       // 使用采样率作为pts的单位，具体换算成秒 pts*1/采样率
        begin_time = get_time();
        ret = encode(codec_ctx, frame, pkt, outfile);
        end_time = get_time();
        printf("encode time:%lldms\n", end_time - begin_time);
        if(ret < 0) {
            printf("encode failed\n");
            break;
        }
    }

    /* 冲刷编码器 */
    encode(codec_ctx, NULL, pkt, outfile);
    all_end_time = get_time();
    printf("all encode time:%lldms\n", all_end_time - all_begin_time);
    // 关闭文件
    fclose(infile);
    fclose(outfile);

    // 释放内存
    if(yuv_buf) {
        free(yuv_buf);
    }

    av_frame_free(&frame);
    av_packet_free(&pkt);
    avcodec_free_context(&codec_ctx);

    printf("main finish, please enter Enter and exit\n");
    getchar();
    return 0;
}

```



# 五 时间戳详解

## 1. I 帧/P 帧/B 帧

**I 帧**：I 帧(Intra-coded picture, 帧内编码帧，常称为关键帧)包含一幅完整的图像信息，属于帧内编码图像，不含运动矢量，在解码时不需要参考其他帧图像。因此在 I 帧图像处可以切换频道，而不会导致图像丢失或无法解码。I 帧图像用于阻止误差的累积和扩散。在闭合式 GOP 中，每个 GOP 的第一个帧一定是 I 帧，且当前 GOP 的数据不会参考前后 GOP 的数据。

**P 帧**：P 帧(Predictive-coded picture, 预测编码图像帧)是帧间编码帧，利用之前的 I 帧或 P 帧进行预测编码。

**B 帧**：B 帧(Bi-directionally predicted picture, 双向预测编码图像帧)是帧间编码帧，利用之前和(或)之后的 I 帧或 P 帧进行双向预测编码。B 帧不可以作为参考帧。
B 帧具有更高的压缩率，但需要更多的缓冲时间以及更高的 CPU 占用率，因此 B 帧适合本地存储以及视频点播，而不适用对实时性要求较高的直播系统。

## 2. DTS 和 PTS

DTS(Decoding Time Stamp, 解码时间戳)，表示压缩帧的解码时间。
PTS(Presentation Time Stamp, 显示时间戳)，表示将压缩帧解码后得到的原始帧的显示时间。

音频中 DTS 和 PTS 是相同的。

视频中由于 B 帧需要双向预测，B 帧依赖于其前和其后的帧，因此含 B 帧的视频解码顺序与显示顺序不同，即 DTS 与 PTS 不同。当然，不含 B 帧的视频，其 DTS 和 PTS 是相同的。下图以一个开放式 GOP 示意图为例，说明视频流的解码顺序和显示顺序
![image-20231130180339240](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231130180339240.png)
**采集顺序**指图像传感器采集原始信号得到图像帧的顺序。
**编码顺序**指编码器编码后图像帧的顺序。存储到磁盘的本地视频文件中图像帧的顺序与编码顺序相同。
**传输顺序**指编码后的流在网络中传输过程中图像帧的顺序。
**解码顺序**指解码器解码图像帧的顺序。
**显示顺序**指图像帧在显示器上显示的顺序。
**采集顺序与显示顺序相同。**

**编码顺序、传输顺序、解码顺序、磁盘存储顺序相同。**
以图中“B[1]”帧为例进行说明，“B[1]”帧解码时需要参考“I[0]”帧和“P[3]”帧，因此“P[3]”帧必须比“B[1]”帧先解码。这就导致了解码顺序和显示顺序的不一致，后显示的帧需要先解码。

## 3. FFmpeg 中的时间基与时间戳

### 3.1 时间基与时间戳的概念

在 FFmpeg 中，时间基(time_base)是时间戳(timestamp)的单位，时间戳值乘以时间基，可以得到实际的时刻值(以秒等为单位)。例如，如果一个视频帧的 dts 是 40，pts 是 160，其 time_base 是 1/1000 秒，那么可以计算出此视频帧的解码时刻是 40 毫秒(40/1000)，显示时刻是 160 毫秒(160/1000)。FFmpeg 中时间戳(pts/dts)的类型是 int64_t 类型，把一个 time_base 看作一个时钟脉冲，则可把 dts/pts 看作时钟脉冲的计数。

### 3.2 四种时间基 tbr、tbn 和 tbc

* 处理视频时候==不涉及时间基==转换
* 处理音频过程中（pkt -> frame）需要==转换时间基==

==不同的封装格式具有不同的时间基==（FLV {1 1000 }    ts {1 90k}  mp4 {1 90k}）

在 FFmpeg 处理音视频过程中的==不同阶段==，也会采用不同的时间基。（编解码阶段不同，但是对packet内数据进行时间基转换）
FFmepg 中有三种时间基，命令行中 tbr、tbn 和 tbc 的打印值就是这三种时间基的倒数：
tbn：对应容器中的时间基。值是 AVStream.time_base 的倒数
tbc：对应编解码器中的时间基。值是 AVCodecContext.time_base 的倒数
tbr：从视频流中猜算得到，可能是帧率或场率(帧率的 2 倍

除以上三种时间基外，FFmpeg 还有一个内部时间基 AV_TIME_BASE(以及分数形式的 AV_TIME_BASE_Q)

```c
// Internal time base represented as integer
#define AV_TIME_BASE            1000000

// Internal time base represented as fractional value
#define AV_TIME_BASE_Q          (AVRational){1, AV_TIME_BASE}
```

AV_TIME_BASE 及 AV_TIME_BASE_Q 用于 FFmpeg 内部函数处理，使用此时间基计算得到时间值表示的是微秒。

解码过程中：

* `AVStream -> time_base`赋值

| type      | 容器固定的time_base          |
| --------- | ---------------------------- |
| MP4-audio | `AVRationlal(1,sample_rate)` |
| MP4-vedio | `AVRationlal(1,90k)`         |
| FLV-audio | `AVRationlal(1,1k)`          |
| FLV-vedio | `AVRationlal(1,1k)`          |
| TS-audio  | `AVRationlal(1,90k)`         |
| TS-vedio  | `AVRationlal(1,90k)`         |

* `codec_ctx->time_base`赋值

| type  | avcodec_alloc_context3() | avcodec_open2                            |
| ----- | ------------------------ | ---------------------------------------- |
| vedio | `AVRationlal(1,0)`       | 赋值为 : 初始化编解码器时指定的time_base |
| audio | `AVRationlal(1,0)`       | `AVRationlal(1,sample_rate)`             |

### 3.3 时间值形式转换

<a name = "av_q2d"> [跳转接口] </a>

在 FFmpeg 中，`AVRational` 是表示有理数的结构体，用于表示时间基准、帧率等与时间相关的参数。具体定义如下：

```c
typedef struct AVRational {
    int num; ///< Numerator 分子
    int den; ///< Denominator 分母
} AVRational;
```

这结构体有两个成员变量：

- `num`（Numerator）：分子，表示有理数的分子部分。
- `den`（Denominator）：分母，表示有理数的分母部分。

`AVRational` 主要用于表示时间戳（时间戳 = 分子/分母），帧率等涉及时间和速率的参数。在音视频处理中，时间戳通常使用这个结构体表示，例如在 `AVPacket` 和 `AVFrame` 结构体中的 `pts`（Presentation Timestamp）和 `dts`（Decoding Timestamp）字段。

示例用法：

```c
AVRational time_base = {1, 1000}; // 1 millisecond
```

这个示例表示一个时间基准为每秒 1000 次的情况，常见的例子是在毫秒级的时间戳中使用。如果 `num` 是 1，`den` 是 25，那么时间基准就是 1/25 秒，表示每秒有 25 个时间单位。



在 FFmpeg 中，`AVRational` 主要用于时间相关的计算，而且 FFmpeg 提供了一些用于处理 `AVRational` 的函数，如 `av_cmp_q`、`av_d2q`、`av_q2d` 等。

==av_q2d()将时间从 AVRational 形式转换为 double 形式==。AVRational 是分数类型，double 是双精度浮点数类型，转换的结果单位是秒。转换前后的值基于同一时间基，仅仅是数值的表现形式不同而已。

av_q2d()实现如下：

```c
/**
 * Convert an AVRational to a `double`.
 * @param a AVRational to convert
 * @return `a` in floating-point form
 * @see av_d2q()
 */
static inline double av_q2d(AVRational a){
    return a.num / (double) a.den;
}
```

av_q2d()使用方法如下：

```java
AVStream stream;
AVPacket packet;
packet 播放时刻值：timestamp(单位秒) = packet.pts × av_q2d(stream.time_base);
packet 播放时长值：duration(单位秒) = packet.duration × av_q2d(stream.time_base);
// 形式2： 注意分母在前，分子在后
av_q2d((AVRational){frame_rate.den, frame_rate.num})
```

### 3.5 时间基转换函数

#### 3.5.1 av_rescale_q()

av_rescale_q()用于一个时间戳在不同时间基的下的转换，用于将时间值从一种时间基转换为另一种时间基。

```c
/**
 * Rescale a 64-bit integer by 2 rational numbers.
 *
 * The operation is mathematically equivalent to `a × bq / cq`.
 *
 * This function is equivalent to av_rescale_q_rnd() with #AV_ROUND_NEAR_INF.
 *
 * @see av_rescale(), av_rescale_rnd(), av_rescale_q_rnd()
 */
int64_t av_rescale_q(int64_t a, AVRational bq, AVRational cq) av_const;
```

```cpp
AVRational src_tb = {1, 1000};   // 时间基1，表示每个时刻的时间单位是1/1000 秒
AVRational dst_tb = {1, 25};     // 时间基2，表示每个时刻的时间单位是 1/25 秒

// 定义输入时间戳
int64_t timestamp = 500;

// 使用 av_rescale_q 进行时间戳转换,(500 * 25) / 1000 = 12
// tb1 x ts1 = tb2  x ts2
int64_t timestamp2= av_rescale_q(timestamps, src_tb, dst_tb);

```

#### 3.5.2 av_rescale_q_rnd()

`av_rescale_q_rnd` 是 FFmpeg 中用于进行时间戳和持续时间的转换的函数，类似于 `av_rescale_q`。不同之处在于，`av_rescale_q_rnd` 提供了对舍入模式的选择，以更灵活地进行精确的取整操作。

函数原型如下：

```c
int64_t av_rescale_q_rnd(int64_t a, AVRational bq, AVRational cq, enum AVRounding rnd);
```

参数说明：

- `a`: 要转换的时间值。
- `bq`: 输入时间基。
- `cq`: 输出时间基。
- `rnd`: 舍入模式，是一个表示取整方式的枚举值。

返回值：

- 返回经过转换的时间值。

舍入模式 `enum AVRounding` 包括以下几种取整方式：

- `AV_ROUND_ZERO`: 向零舍入。
- `AV_ROUND_INF`: 向正无穷舍入。
- `AV_ROUND_DOWN`: 向负无穷舍入。
- `AV_ROUND_UP`: 向正无穷舍入。
- `AV_ROUND_NEAR_INF`: 四舍五入到最接近的整数。

以下是一个使用 `av_rescale_q_rnd` 函数的示例：

```c
#include <stdio.h>
#include <libavutil/rational.h>
#include <libavutil/mathematics.h>

int main() {
    // 定义输入时间基和输出时间基
    AVRational src_tb = {1, 1000};   // 输入时间基，表示每个时刻的时间单位是毫秒
    AVRational dst_tb = {1, 25};     // 输出时间基，表示每个时刻的时间单位是 1/25 秒

    // 定义输入时间戳（毫秒）
    int64_t timestamp_in_milliseconds = 500;

    // 使用 av_rescale_q_rnd 进行时间戳转换，选择向正无穷舍入模式
    int64_t timestamp_in_output_units = av_rescale_q_rnd(
        timestamp_in_milliseconds, src_tb, dst_tb, AV_ROUND_UP
    );

    // 输出结果
    printf("Timestamp in output units: %lld\n", timestamp_in_output_units);

    return 0;
}
```

在这个示例中，我们将 500 毫秒的时间戳从输入时间基（毫秒）转换为输出时间基（1/25 秒），选择向正无穷舍入模式。计算的结果是 `(500 * 25 + 999) / 1000 = 13`，这表示在新的时间基中，500 毫秒对应 13 个单位。

#### 3.5.3 av_packet_rescale_ts()

`av_packet_rescale_ts` 函数是用于调整 `AVPacket` 结构体中的时间戳（PTS、DTS）和持续时间（duration）的函数。这个函数通常在进行时间基的转换时使用，用于将一个 `AVPacket` 中的时间戳和持续时间从一个时间基（tb_src）转换为另一个时间基（tb_dst）。

```c
/**
 * Convert valid timing fields (timestamps / durations) in a packet from one
 * timebase to another. Timestamps with unknown values (AV_NOPTS_VALUE) will be
 * ignored.
 *
 * @param pkt packet on which the conversion will be performed
 * @param tb_src source timebase, in which the timing fields in pkt are
 *               expressed
 * @param tb_dst destination timebase, to which the timing fields will be
 *               converted
 */
void av_packet_rescale_ts(AVPacket *pkt, AVRational tb_src, AVRational tb_dst);
```

### 3.6 转封装过程中的时间基转换

![image-20231130185951337](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231130185951337.png)

容器中的时间基(AVStream.time_base，3.2 节中的 tbn)定义如下：

```c
typedef struct AVStream {
    ......
    /**
     * This is the fundamental unit of time (in seconds) in terms
     * of which frame timestamps are represented.
     *
     * decoding: set by libavformat
     * encoding: May be set by the caller before avformat_write_header() to
     *           provide a hint to the muxer about the desired timebase. In
     *           avformat_write_header(), the muxer will overwrite this field
     *           with the timebase that will actually be used for the timestamps
     *           written into the file (which may or may not be related to the
     *           user-provided one, depending on the format).
     */
    AVRational time_base;
    ......
}
```

AVStream.time_base 是 AVPacket 中 pts 和 dts 的时间单位，输入流与输出流中 time_base 按如下方式确定：
对于输入流：打开输入文件后，调用 avformat_find_stream_info()可获取到每个流中的 time_base
对于输出流：打开输出文件后，调用 avformat_write_header()可根据输出文件封装格式确定每个流的 time_base 并写入输出文件中

不同封装格式具有不同的时间基，在转封装(将一种封装格式转换为另一种封装格式)过程中，时间基转换相关代码如下：

```rust
av_read_frame(ifmt_ctx, &pkt);
pkt.pts = av_rescale_q_rnd(pkt.pts, in_stream->time_base, out_stream->time_base, AV_ROUND_NEAR_INF|AV_ROUND_PASS_MINMAX);
pkt.dts = av_rescale_q_rnd(pkt.dts, in_stream->time_base, out_stream->time_base, AV_ROUND_NEAR_INF|AV_ROUND_PASS_MINMAX);
pkt.duration = av_rescale_q(pkt.duration, in_stream->time_base, out_stream->time_base);
```

下面的代码具有和上面代码相同的效果：

```scss
// 从输入文件中读取 packet
av_read_frame(ifmt_ctx, &pkt);
// 将 packet 中的各时间值从输入流封装格式时间基转换到输出流封装格式时间基
av_packet_rescale_ts(&pkt, in_stream->time_base, out_stream->time_base);
```

这里流里的时间基`in_stream->time_base`和`out_stream->time_base`，是容器中的时间基，就是 3.2 节中的 tbn。

例如，flv 封装格式的 time_base 为{1,1000}，ts 封装格式的 time_base 为{1,90000}
我们编写程序将 flv 封装格式转换为 ts 封装格式，抓取原文件(flv)的前四帧显示时间戳：

```makefile
think@opensuse> ffprobe -show_frames -select_streams v tnmil3.flv | grep pkt_pts  
ffprobe version 4.1 Copyright (c) 2007-2018 the FFmpeg developers
Input #0, flv, from 'tnmil3.flv':
  Metadata:
    encoder         : Lavf58.20.100
  Duration: 00:00:03.60, start: 0.017000, bitrate: 513 kb/s
    Stream #0:0: Video: h264 (High), yuv420p(progressive), 784x480, 25 fps, 25 tbr, 1k tbn, 50 tbc
    Stream #0:1: Audio: aac (LC), 44100 Hz, stereo, fltp, 128 kb/s
pkt_pts=80
pkt_pts_time=0.080000
pkt_pts=120
pkt_pts_time=0.120000
pkt_pts=160
pkt_pts_time=0.160000
pkt_pts=200
pkt_pts_time=0.200000
```

再抓取转换的文件(ts)的前四帧显示时间戳：

```makefile
think@opensuse> ffprobe -show_frames -select_streams v tnmil3.ts | grep pkt_pts  
ffprobe version 4.1 Copyright (c) 2007-2018 the FFmpeg developers
Input #0, mpegts, from 'tnmil3.ts':
  Duration: 00:00:03.58, start: 0.017000, bitrate: 619 kb/s
  Program 1 
    Metadata:
      service_name    : Service01
      service_provider: FFmpeg
    Stream #0:0[0x100]: Video: h264 (High) ([27][0][0][0] / 0x001B), yuv420p(progressive), 784x480, 25 fps, 25 tbr, 90k tbn, 50 tbc
    Stream #0:1[0x101]: Audio: aac (LC) ([15][0][0][0] / 0x000F), 44100 Hz, stereo, fltp, 127 kb/s
pkt_pts=7200
pkt_pts_time=0.080000
pkt_pts=10800
pkt_pts_time=0.120000
pkt_pts=14400
pkt_pts_time=0.160000
pkt_pts=18000
pkt_pts_time=0.200000
```

可以发现，对于同一个视频帧，它们时间基(tbn)不同因此时间戳(pkt_pts)也不同，但是计算出来的时刻值(pkt_pts_time)是相同的。
看第一帧的时间戳，计算关系：80×{1,1000} == 7200×{1,90000} == 0.080000

### 3.7 转码过程中的时间基转换

编解码器中的时间基(AVCodecContext.time_base，3.2 节中的 tbc)定义如下：

```c
typedef struct AVCodecContext {
    ......
    
    /**
     * This is the fundamental unit of time (in seconds) in terms
     * of which frame timestamps are represented. For fixed-fps content,
     * timebase should be 1/framerate and timestamp increments should be
     * identically 1.
     * This often, but not always is the inverse of the frame rate or field rate
     * for video. 1/time_base is not the average frame rate if the frame rate is not
     * constant.
     *
     * Like containers, elementary streams also can store timestamps, 1/time_base
     * is the unit in which these timestamps are specified.
     * As example of such codec time base see ISO/IEC 14496-2:2001(E)
     * vop_time_increment_resolution and fixed_vop_rate
     * (fixed_vop_rate == 0 implies that it is different from the framerate)
     *
     * - encoding: MUST be set by user.
     * - decoding: the use of this field for decoding is deprecated.
     *             Use framerate instead.
     */
    AVRational time_base;
    
    ......
}
```

上述注释指出，AVCodecContext.time_base 是帧率(视频帧)的倒数，每帧时间戳递增 1，那么 tbc 就等于帧率。编码过程中，应由用户设置好此参数。解码过程中，此参数已过时，建议直接使用帧率倒数用作时间基。

这里有一个问题：按照此处注释说明，帧率为 25 的视频流，tbc 理应为 25，但实际值却为 50，不知作何解释？是否 tbc 已经过时，不具参考意义？

根据注释中的建议，实际使用时，在视频解码过程中，我们不使用 AVCodecContext.time_base，而用帧率倒数作时间基，在视频编码过程中，我们将 AVCodecContext.time_base 设置为帧率的倒数。

`AVCodecContext.time_base = av_inv_q(dec_ctx->framerate);`

`dec_ctx->framerate` 表示解码器的帧率，是一个 `AVRational` 结构体，包含了帧率的分子和分母。`av_inv_q` 用于获取这个帧率的倒数。

#### 3.7.1 视频流

视频按帧播放，所以解码后的原始视频帧时间基为 1/framerate。

视频解码过程中的时间基转换处理：

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231130190631203.png" alt="image-20231130190631203" style="zoom:67%;" />

```c
AVFormatContext *ifmt_ctx;
AVStream *in_stream;
AVCodecContext *dec_ctx;
AVPacket packet;
AVFrame *frame;

// 从输入文件中读取编码帧
av_read_frame(ifmt_ctx, &packet);

// 时间基转换, stn -> stc (即计算 时间戳在AVstream.time_base 到 AVcodecContex.time_base下的转换)
int raw_video_time_base = av_inv_q(dec_ctx->framerate);
av_packet_rescale_ts(packet, in_stream->time_base, raw_video_time_base);

// 解码
avcodec_send_packet(dec_ctx, packet)
avcodec_receive_frame(dec_ctx, frame);
```

视频编码过程中的时间基转换处理：

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231130190953310.png" alt="image-20231130190953310" style="zoom:67%;" />

```c
AVFormatContext *ofmt_ctx;
AVStream *out_stream;
AVCodecContext *dec_ctx;
AVCodecContext *enc_ctx;
AVPacket packet;
AVFrame *frame;

// 编码
avcodec_send_frame(enc_ctx, frame);
avcodec_receive_packet(enc_ctx, packet);

// 时间基转换
packet.stream_index = out_stream_idx;
enc_ctx->time_base = av_inv_q(dec_ctx->framerate);
av_packet_rescale_ts(&opacket, enc_ctx->time_base, out_stream->time_base);

// 将编码帧写入输出媒体文件
av_interleaved_write_frame(o_fmt_ctx, &packet);
```

#### 3.7.2 音频流

音频按采样点播放，所以解码后的原始音频帧时间基为 1/sample_rate

音频解码过程中的时间基转换处理：

```c
AVFormatContext *ifmt_ctx;
AVStream *in_stream;
AVCodecContext *dec_ctx;
AVPacket packet;
AVFrame *frame;

// 从输入文件中读取编码帧
av_read_frame(ifmt_ctx, &packet);

// 时间基转换
int raw_audio_time_base = av_inv_q(dec_ctx->sample_rate);
av_packet_rescale_ts(packet, in_stream->time_base, raw_audio_time_base);

// 解码
avcodec_send_packet(dec_ctx, packet)
avcodec_receive_frame(dec_ctx, frame);
```

音频编码过程中的时间基转换处理：

```c
AVFormatContext *ofmt_ctx;
AVStream *out_stream;
AVCodecContext *dec_ctx;
AVCodecContext *enc_ctx;
AVPacket packet;
AVFrame *frame;

// 编码
avcodec_send_frame(enc_ctx, frame);
avcodec_receive_packet(enc_ctx, packet);

// 时间基转换
packet.stream_index = out_stream_idx;
enc_ctx->time_base = av_inv_q(dec_ctx->sample_rate);
av_packet_rescale_ts(&opacket, enc_ctx->time_base, out_stream->time_base);

// 将编码帧写入输出媒体文件
av_interleaved_write_frame(o_fmt_ctx, &packet);
```

# 六 FLV封装

[学习资料](E:\typora索引文件\ffmpeg\08-04-音视频FLV合成实战.pdf)

## 6.1 AVOutputFormat

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231130094639036.png" alt="image-20231130094639036"  />

`avformat_write_header` 函数负责将媒体容器的头信息写入文件。这些头信息包含了媒体文件的元数据以及关于各个流的信息。具体来说，写入的头文件通常包括以下信息：

1. **文件格式信息：** 包含文件格式的标识符，表示使用的是哪种容器格式，比如 AVI、MP4、FLV 等。

2. **流的元数据：** 对于每个流，包括音频、视频、字幕等，会写入该流的元数据。这些元数据可能包括编码器信息、时基（time base）、流的索引、时长等。

3. **全局参数：** 一些全局的参数，比如媒体文件的整体时长，整体的时基等。

写入头文件的过程并不需要读取具体的流内容。头信息主要是根据 `AVFormatContext` 中设置的各个参数，以及每个流（`AVStream`）中设置的参数来构建的。这些参数通常是在打开文件、添加流、设置编码参数等步骤中配置的。

举例来说，如果你有一个视频流和一个音频流，那么在调用 `avformat_write_header` 之前，你需要设置：

- 对于视频流 (`AVStream`)，你需要设置视频的编码参数，如分辨率、帧率、编码器等。
- 对于音频流 (`AVStream`)，你需要设置音频的编码参数，如采样率、声道数、编码器等。
- 你还需要设置媒体容器的格式，即 `AVFormatContext` 中的 `oformat` 字段，表明你要使用哪种格式进行封装。

这些设置决定了写入头文件时需要包含的信息。函数会根据这些信息构建合适的头部数据，无需读取实际的流内容。

实际的数据读取和写入通常发生在 `av_write_frame` 的循环中，该循环用于逐帧地将编码后的数据写入文件。头文件写入后，你可以在之后的循环中通过编码器将音频和视频数据逐帧写入文件。





# 七 MP4封装

[h264编码参数设置文档](F:/学习视频/音视频/资料/09-FFmpeg编码+封装实战/08-03-视频编码实战.pdf)

[完整项目](D:\QT\project\audio_vedio_learn\pcm_yuv_mux_mp4)

```cpp
#include <iostream>

#include "audioencoder.h"
#include "audioresampler.h"
#include "videoencoder.h"
#include "muxer.h"
using namespace std;

#define YUV_WIDTH 720
#define YUV_HEIGHT 576
#define YUV_FPS  25

#define VIDEO_BIT_RATE 500*1024

#define PCM_SAMPLE_FORMAT AV_SAMPLE_FMT_S16
#define PCM_SAMPLE_RATE 44100
#define PCM_CHANNELS 2

#define AUDIO_BIT_RATE 128*1024

#define AUDIO_TIME_BASE 1000000
#define VIDEO_TIME_BASE 1000000
//ffmpeg -i sound_in_sync_test.mp4 -pix_fmt yuv420p 720x576_yuv420p.yuv
//ffmpeg -i sound_in_sync_test.mp4 -vn -ar 44100 -ac 2 -f s16le 44100_2_s16le.pcm
// 执行文件  yuv文件 pcm文件 输出mp4文件
int main(int argc, char **argv)
{
    if(argc != 4) {
        printf("usage -> exe in.yuv in.pcm out.mp4");
        return -1;
    }
    // 1. 打开yuv pcm文件
    char *in_yuv_name = argv[1];
    char *in_pcm_name = argv[2];
    char *out_mp4_name = argv[3];
    FILE *in_yuv_fd = NULL;
    FILE *in_pcm_fd = NULL;
    //1. 打开测试文件
    // 打开YUV文件
    in_yuv_fd = fopen(in_yuv_name, "rb");
    if( !in_yuv_fd )
    {
        printf("Failed to open %s file\n", in_yuv_name);
        return -1;
    }

    // 打开PCM文件
    in_pcm_fd = fopen(in_pcm_name, "rb");
    if( !in_pcm_fd )
    {
        printf("Failed to open %s file\n", in_pcm_name);
        return -1;
    }

    int ret = 0;
    // 2. 初始化编码器，包括视频、音频编码器, 分配yuv、pcm的帧buffer
    // 2.1 初始化video
    // 初始化编码器
    int yuv_width = YUV_WIDTH;
    int yuv_height = YUV_HEIGHT;
    int yuv_fps = YUV_FPS;
    int video_bit_rate = VIDEO_BIT_RATE;
    VideoEncoder video_encoder;
    ret = video_encoder.InitH264(yuv_width, yuv_height, yuv_fps, video_bit_rate);
    if(ret < 0)
    {
        printf("video_encoder.InitH264 failed\n");
        return -1;
    }
    // 分配yuv buf
    int y_frame_size = yuv_width * yuv_height;
    int u_frame_size = yuv_width * yuv_height /4;
    int v_frame_size = yuv_width * yuv_height /4;
    int yuv_frame_size = y_frame_size + u_frame_size + v_frame_size;
    uint8_t *yuv_frame_buf = (uint8_t *)malloc(yuv_frame_size);
    if(!yuv_frame_buf)
    {
        printf("malloc(yuv_frame_size)\n");
        return -1;
    }

    // 2.2 初始化audio
    // 初始化音频编码器
    int pcm_channels= PCM_CHANNELS;
    int pcm_sample_rate = PCM_SAMPLE_RATE;
    int pcm_sample_format = PCM_SAMPLE_FORMAT;
    int audio_bit_rate = AUDIO_BIT_RATE;
    AudioEncoder audio_encoder;
    ret = audio_encoder.InitAAC(pcm_channels, pcm_sample_rate, audio_bit_rate);
    if(ret < 0)
    {
        printf("audio_encoder.InitAAC failed\n");
        return -1;
    }
    // 分配pcm buf
    // pcm_frame_size  = 单个采样点占用的字节 * 通道数量 * 每个通道有多少给采用点
    int pcm_frame_size = av_get_bytes_per_sample((AVSampleFormat)pcm_sample_format)
            *pcm_channels * audio_encoder.GetFrameSize();
    if(pcm_frame_size <= 0) {
        printf("pcm_frame_size <= 0\n");
        return -1;
    }
    uint8_t *pcm_frame_buf = (uint8_t *)malloc(pcm_frame_size);
    if(!pcm_frame_buf)
    {
        printf("malloc(pcm_frame_size)\n");
        return -1;
    }

    // 初始化重采样
    AudioResampler audio_resampler;
    ret = audio_resampler.InitFromS16ToFLTP(pcm_channels, pcm_sample_rate,
                                      audio_encoder.GetChannels(), audio_encoder.GetSampleRate());
    if(ret < 0)
    {
        printf("audio_resampler.InitFromS16ToFLTP failed\n");
        return -1;
    }

    // 3. mp4初始化 包括新建流，open io, send header
    Muxer mp4_muxer;
    ret = mp4_muxer.Init(out_mp4_name);
    if(ret < 0)
    {
        printf("mp4_muxer.Init failed\n");
        return -1;
    }

    ret = mp4_muxer.AddStream(video_encoder.GetCodecContext());
    if(ret < 0)
    {
        printf("mp4_muxer.AddStream video failed\n");
        return -1;
    }

    ret = mp4_muxer.AddStream(audio_encoder.GetCodecContext());
    if(ret < 0)
    {
        printf("mp4_muxer.AddStream audio failed\n");
        return -1;
    }

    ret = mp4_muxer.Open();
    if(ret < 0)
    {
        printf("mp4_muxer.Open failed\n");
        return -1;
    }

    ret = mp4_muxer.SendHeader();
    if(ret < 0)
    {
        printf("mp4_muxer.SendHeader failed\n");
        return -1;
    }
    // 4. 在while循环读取yuv、pcm进行编码然后发送给MP4 muxer
    // 4.1 时间戳相关
    int64_t audio_time_base = AUDIO_TIME_BASE;
    int64_t video_time_base = VIDEO_TIME_BASE;
    double audio_pts = 0;
    double video_pts = 0;
    double audio_frame_duration = 1.0 * audio_encoder.GetFrameSize()/pcm_sample_rate*audio_time_base;
    double video_frame_duration = 1.0/yuv_fps * video_time_base;

    int audio_finish = 0;   // 两者都为0的时候才结束while循环
    int video_finish = 0;

    size_t read_len = 0;
    AVPacket *packet =  NULL;
    int audio_index = mp4_muxer.GetAudioStreamIndex();
    int video_index = mp4_muxer.GetVideoStreamIndex();
    while (1) {
        if(audio_finish && video_finish) {
            break;
        }
        printf("apts:%0.0lf vpts:%0.0lf\n", audio_pts/1000, video_pts/1000);
        if((video_finish != 1 && audio_pts > video_pts)   // audio和vidoe都还有数据，优先audio（audio_pts > video_pts）
              ||  (video_finish != 1 && audio_finish == 1)) {
            read_len = fread(yuv_frame_buf, 1, yuv_frame_size, in_yuv_fd);
            if(read_len < yuv_frame_size) {
                video_finish = 1;
                printf("fread yuv_frame_buf finish\n");
            }

            if(video_finish != 1) {
                packet = video_encoder.Encode(yuv_frame_buf, yuv_frame_size, video_index,
                                              video_pts, video_time_base);
            }else {
                packet = video_encoder.Encode(NULL, 0, video_index,
                                              video_pts, video_time_base);
            }
            video_pts += video_frame_duration;  // 叠加pts
            if(packet) {
                mp4_muxer.SendPacket(packet);
            }
        } else if(audio_finish != 1) {
            read_len = fread(pcm_frame_buf, 1, pcm_frame_size, in_pcm_fd);
            if(read_len < pcm_frame_size) {
                audio_finish = 1;
                printf("fread pcm_frame_buf finish\n");
            }

            if(audio_finish != 1) {
                AVFrame *fltp_frame = AllocFltpPcmFrame(pcm_channels, audio_encoder.GetFrameSize());
                ret = audio_resampler.ResampleFromS16ToFLTP(pcm_frame_buf, fltp_frame);
                if(ret < 0)
                    printf("ResampleFromS16ToFLTP error\n");
                packet = audio_encoder.Encode(fltp_frame, audio_index,
                                              audio_pts, audio_time_base);
                FreePcmFrame(fltp_frame);
            }else {
                packet = audio_encoder.Encode(NULL,video_index,
                                              audio_pts, audio_time_base);
            }
            audio_pts += audio_frame_duration;  // 叠加pts
            if(packet) {
                mp4_muxer.SendPacket(packet);
            }
        }
    }
    ret = mp4_muxer.SendTrailer();
    if(ret < 0)
    {
        printf("mp4_muxer.SendTrailer failed\n");
    }

    printf("write mp4 finish\n");

    if(yuv_frame_buf)
        free(yuv_frame_buf);
    if(pcm_frame_buf)
        free(pcm_frame_buf);
    if(in_yuv_fd)
        fclose(in_yuv_fd);
    if(in_pcm_fd)
        fclose(in_pcm_fd);

    return 0;
}






```





# 八 ffplay

```
F:\学习视频\音视频\资料\12-13- ffplay播放器剖析\ffplay-pro-1-3\ffmpeg-4.2.1-win32-dev\include\libavresample\avresample.h

F:\学习视频\音视频\资料\12-13- ffplay播放器剖析\ffplay-pro-1-3\ffmpeg-4.2.1-win32-dev\include\libavutil\libm.h

F:\学习视频\音视频\资料\12-13- ffplay播放器剖析\ffplay-pro-1-3\ffmpeg-4.2.1-win32-dev\include\libavformat\network.h

"F:\学习视频\音视频\资料\12-13- ffplay播放器剖析\ffplay-pro-1-3\ffmpeg-4.2.1-win32-dev\include\libavutil\wchar_filename.h"
```

[文档](F:\学习视频\音视频\资料\12-13- ffplay播放器剖析)

[博客](https://ffmpeg.xianwaizhiyin.net/ffplay/main.html)

[数据结构和接口](E:\typora索引文件\ffmpeg\ffplay\ffplay播放器-1-3.pdf)

[读取线程 ](E:\typora索引文件\ffmpeg\ffplay\ffplay播放器-4数据读取线程.pdf)

[项目文件路径](D:\QT\project\audio_vedio_learn\ffplay_2)

* ffplay 自定义封装，包括播放器的所有接口和变量

## 环境配置

FFmpeg 工程用了大量的 linux 系统的函数，所以 在Window10 需要用 MSYS2 来编译。简单来说，MSYS2 就是一个软件套件，他可以把linux的软件转成 window的软件，编译打包成 exe文件在 window 运行。

MSVC 的编译器跟 MinGW 编译器的区别：如果你Qt项目里的 C 文件，里面用了linux 的api，那只能用MinGW来编译，用MSVC编译会报错。如果没用到 linux的api函数，用MSVC 还是 MinGW 都能编译成功。

### 1 选择编译器和debugger

> ffplay源码中调用linuxAPI，因此本项目kits选择MinGW32bit，调试器选择gdb，具体如下：

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231213155501962.png" alt="image-20231213155501962" style="zoom:67%;" />

> eg：本项目中所使用的ffmpeg库和SDL库均为32bit，具体配置需要结合自己所编译的库类型

### 2 导入库

> 导入ffplay源码和编译好的ffmpg、SDL库

具体参考：https://juejin.cn/post/7052208992109461541

![image-20240109200208445](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20240109200208445.png)

> 将两个库目录下的ddl动态库复制到QT文件的执行目录下，将Ole32.Lib 放入项目目录下 【全部存在dll文件夹内】

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231213160201173.png" alt="image-20231213160201173" style="zoom:67%;" />



### 3 配置pro文件

```cpp
TEMPLATE = app
CONFIG += console
CONFIG -= app_bundle
CONFIG -= qt

SOURCES += \
    cmdutils.c \
    ffplay.c

win32 {
INCLUDEPATH += $$PWD/ffmpeg-4.2.1-win32-dev/include
INCLUDEPATH += $$PWD/SDL2/include

LIBS += $$PWD/ffmpeg-4.2.1-win32-dev/lib/avformat.lib   \
        $$PWD/ffmpeg-4.2.1-win32-dev/lib/avcodec.lib    \
        $$PWD/ffmpeg-4.2.1-win32-dev/lib/avdevice.lib   \
        $$PWD/ffmpeg-4.2.1-win32-dev/lib/avfilter.lib   \
        $$PWD/ffmpeg-4.2.1-win32-dev/lib/avutil.lib     \
        $$PWD/ffmpeg-4.2.1-win32-dev/lib/postproc.lib   \
        $$PWD/ffmpeg-4.2.1-win32-dev/lib/swresample.lib \
        $$PWD/ffmpeg-4.2.1-win32-dev/lib/swscale.lib    \
        $$PWD/SDL2/lib/x86/SDL2.lib \
        $$PWD/Ole32.Lib 
}

HEADERS += \
    cmdutils.h \
    config.h

```

### 4 检查环境

> 分别检查 ffmpeg 和 SDL库是否配置成功

```cpp
#if 0  // ffmpeg
#include <stdio.h>

int main()
{

    printf("Hello World!\n");
    return 0;
}
#endif
#if 0  // SDL
#include <stdio.h>

#include "libavutil/avutil.h"

int main()
{
    printf("Hello FFMPEG, version is %s\n", av_version_info());
    return 0;
 }
#endif
#if 1
#include <stdio.h>
#include <SDL.h>

#undef main
int main()
{
    printf("Hello World!\n");

    SDL_Window *window = NULL;

    SDL_Init(SDL_INIT_VIDEO);

    window = SDL_CreateWindow("Basic Window",
                              SDL_WINDOWPOS_UNDEFINED, // 默认生成窗口位置：中心
                              SDL_WINDOWPOS_UNDEFINED,
                              640,						// 窗口大小
                              480,
                              SDL_WINDOW_OPENGL | SDL_WINDOW_RESIZABLE);
    if(!window){
        printf("can't create window:%s\n", SDL_GetError());
        return 1;

    }

    SDL_Delay(10000); // 延迟10s

    // 释放资源
    SDL_DestroyWindow(window);
    SDL_Quit();


    return 0;
}
#endif

```

### 5 加载源码文件

> 将ffplay.c及其依赖文件导入项目，媒体文件放入执行目录下
>
> 输入参数，执行

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231213160643236.png" alt="image-20231213160643236" style="zoom:67%;" />

### 6 执行成功

![image-20231213160746758](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20231213160746758.png)

## 数据结构

###  1 VideoState

#### 1.1 定义

```cpp
typedef struct VideoState {
    // ============================ stream_open()内初始化 ============
    char *filename;             // 文件名
    AVInputFormat *iformat;     // 指向demuxer    一般为空
    int width, height, xleft, ytop; // 宽、高，x起始坐标，y起始坐标
    
    PacketQueue	audioq;         // 音频packet队列
    PacketQueue videoq;         // 视频packet队列
    PacketQueue subtitleq;      // 字幕packet队列
    
    FrameQueue	pictq;          // 视频Frame队列
    FrameQueue	subpq;          // 字幕Frame队列
    FrameQueue	sampq;          // 采样Frame队列
    
    SDL_cond *continue_read_thread; // 当读取数据队列满了后进入休眠时，可以通过该condition唤醒读线程
    
    Clock	audclk;             // 音频时钟
    Clock	vidclk;             // 视频时钟
    Clock	extclk;             // 外部时钟
    int audio_clock_serial;     // 播放序列，seek可改变此值，初始化-1
    
    int	audio_volume;           // 音量
    int muted;                  // =1静音，=0则正常
    int av_sync_type;           // 音视频同步类型, 默认AV_SYNC_AUDIO_MASTER;
    
    SDL_Thread	*read_tid;      // 读线程句柄
   
    // =============================== read_thread()内初始化========
    // 保留最近的相应audio、video、subtitle流的steam index，初始化-1
    int last_video_stream, last_audio_stream, last_subtitle_stream;
    int audio_stream ;          // 音频流索引 -1
    int video_stream;           // 视频流索引 -1
    int subtitle_stream;        // 字幕流索引 -1
    int eof;            		// 是否读取结束，初始化0，-1表示数据读取完毕
    AVFormatContext *ic;        // iformat的上下文
    
    enum ShowMode {
        SHOW_MODE_NONE = -1, SHOW_MODE_VIDEO = 0, SHOW_MODE_WAVES, SHOW_MODE_RDFT, SHOW_MODE_NB
    } show_mode; // 初始化为NONE，之后选择显示视频or显示频谱图
    
    // ===================================== stream_component_open()内初始化 ==================
    
    AVStream *audio_st;  		// 音频流
    AVStream *subtitle_st;      // 字幕流 
    AVStream *video_st;         // 视频流
    
    int audio_stream ;          // 音频流索引 
    int video_stream;           // 视频流索引 
    int subtitle_stream;        // 字幕流索引 
    
    
    int		abort_request;      // =1时请求退出播放
    int		force_refresh;      // =1时需要刷新画面，请求立即刷新画面的意思
    int		paused;             // =1时暂停，=0时播放
    int		last_paused;        // 暂存“暂停”/“播放”状态
    int		queue_attachments_req;  //  mp3专辑封面
    
    // seek操作相关
    int		seek_req;           // 标识一次seek请求
    int		seek_flags;         // seek标志，诸如AVSEEK_FLAG_BYTE等
    int64_t		seek_pos;       // 请求seek的目标位置(当前位置+增量)
    int64_t		seek_rel;       // 本次seek的位置增量
    int		read_pause_return;
    
    int		realtime;           // =1为实时流

	// ========================音视频解码 =======================
    Decoder auddec;             // 音频解码器
    Decoder viddec;             // 视频解码器
    Decoder subdec;             // 字幕解码器



    double			audio_clock;            // 当前音频帧的PTS+当前帧Duration
    
    // 以下4个参数 非audio master同步方式使用
    double			audio_diff_cum;         // used for AV difference average computation
    double			audio_diff_avg_coef;
    double			audio_diff_threshold;
    int			audio_diff_avg_count;
    // end

    // ======================= 音视频播放===================================
    int			audio_hw_buf_size;          // SDL音频缓冲区的大小(字节为单位)
    // 指向待播放的一帧音频数据，指向的数据区将被拷入SDL音频缓冲区。若经过重采样则指向audio_buf1，
    // 否则指向frame中的音频
    uint8_t			*audio_buf;             // 从AVFrame中取出的⾳频数据（PCM），如果有必要，则对该数据重采样
    uint8_t			*audio_buf1;            // 指向重采样后的数据，buffer
    unsigned int		audio_buf_size;     // 待播放的一帧音频数据(audio_buf指向)的大小
    unsigned int		audio_buf1_size;    // 申请到的音频缓冲区audio_buf1的实际尺寸
    int			audio_buf_index;            // 下⼀次可读的audio_buf的index位置的位置索引(指向第一个待拷贝字节)
    int			audio_write_buf_size; // audio_buf剩余的长度 = audio_buf_size - audio_buf_index
    
    struct AudioParams audio_src;           // 音频frame的参数，初始值=audio_tgt
    struct AudioParams audio_tgt;       // 用来接收SDL设备实际的音频输出参数，重采样转换：audio_src->audio_tgt
    
    struct SwrContext *swr_ctx;         // 音频重采样context
    int frame_drops_early;              // 丢弃视频packet计数
    int frame_drops_late;               // 丢弃视频frame计数

#if CONFIG_AVFILTER
    struct AudioParams audio_filter_src;
#endif
    // 音频波形显示使用
    int16_t sample_array[SAMPLE_ARRAY_SIZE];
    int sample_array_index;
    int last_i_start;
    RDFTContext *rdft;
    int rdft_bits;
    FFTSample *rdft_data;

    int xpos;
    double last_vis_time;
    SDL_Texture *vis_texture;

    SDL_Texture *sub_texture;       // 字幕显示
    SDL_Texture *vid_texture;       // 视频显示

    

    double frame_timer;             // 记录最后一帧播放的时刻
    double frame_last_returned_time;
    double frame_last_filter_delay;


    double max_frame_duration;      // 一帧最大间隔. above this, we consider the jump a timestamp discontinuity
    struct SwsContext *img_convert_ctx; // 视频尺寸格式变换
    struct SwsContext *sub_convert_ctx; // 字幕尺寸格式变换
    

    
    
    int step;           // =1 步进播放模式, =0 其他模式

#if CONFIG_AVFILTER
    int vfilter_idx;
    AVFilterContext *in_video_filter;   // the first filter in the video chain
    AVFilterContext *out_video_filter;  // the last filter in the video chain
    AVFilterContext *in_audio_filter;   // the first filter in the audio chain
    AVFilterContext *out_audio_filter;  // the last filter in the audio chain
    AVFilterGraph *agraph;              // audio filter graph
#endif
    

    
} VideoState;
```

#### 1.2 接口

| 函数名                      | 作用 |
| --------------------------- | ---- |
| [stream_open](#stream_open) |      |

---------

<a name="stream_open">【stream_open】</a>

```cpp
static VideoState *stream_open(const char *filename, AVInputFormat *iformat)
{
    VideoState *is;

    is = av_mallocz(sizeof(VideoState));	/* 分配结构体并初始化 */
    if (!is)
        return NULL;
    is->filename = av_strdup(filename);
    if (!is->filename)
        goto fail;
    is->iformat = iformat;
    is->ytop    = 0;
    is->xleft   = 0;

    /* 初始化帧队列 */
    if (frame_queue_init(&is->pictq, &is->videoq, VIDEO_PICTURE_QUEUE_SIZE, 1) < 0)
        goto fail;
    if (frame_queue_init(&is->subpq, &is->subtitleq, SUBPICTURE_QUEUE_SIZE, 0) < 0)
        goto fail;
    if (frame_queue_init(&is->sampq, &is->audioq, SAMPLE_QUEUE_SIZE, 1) < 0)
        goto fail;

    if (packet_queue_init(&is->videoq) < 0 ||
            packet_queue_init(&is->audioq) < 0 ||
            packet_queue_init(&is->subtitleq) < 0)
        goto fail;

    /* 创建 */
    if (!(is->continue_read_thread = SDL_CreateCond())) {
        av_log(NULL, AV_LOG_FATAL, "SDL_CreateCond(): %s\n", SDL_GetError());
        goto fail;
    }

    /*
     * 初始化时钟
     * 时钟序列->queue_serial，实际上指向的是is->videoq.serial
     */
    init_clock(&is->vidclk, &is->videoq.serial);
    init_clock(&is->audclk, &is->audioq.serial);
    init_clock(&is->extclk, &is->extclk.serial);
    is->audio_clock_serial = -1;
    /* 初始化音量 */
    if (startup_volume < 0)
        av_log(NULL, AV_LOG_WARNING, "-volume=%d < 0, setting to 0\n", startup_volume);
    if (startup_volume > 100)
        av_log(NULL, AV_LOG_WARNING, "-volume=%d > 100, setting to 100\n", startup_volume);
    startup_volume = av_clip(startup_volume, 0, 100);
    startup_volume = av_clip(SDL_MIX_MAXVOLUME * startup_volume / 100, 0, SDL_MIX_MAXVOLUME);
    is->audio_volume = startup_volume;
    is->muted = 0;
    is->av_sync_type = av_sync_type;
    /* 创建读线程 */
    is->read_tid     = SDL_CreateThread(read_thread, "read_thread", is);
    if (!is->read_tid) {
        av_log(NULL, AV_LOG_FATAL, "SDL_CreateThread(): %s\n", SDL_GetError());
fail:
        stream_close(is);
        return NULL;
    }
    return is;
}

```



### 2 PacketQueue

#### 2.1 内存模型和serial

![image-20240106155116868](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20240106155116868.png)

1. MyAVPacketList的内存是完全由PacketQueue维护的，在put的时候`av_malloc`，在get的时候`av_free`。

2.  AVPacket分两块： 

   * ⼀部分是AVPacket结构体的内存（黄色），这部分从MyAVPacketList的定义可以看出是和MyAVPacketList 共存亡的，通过`av_free`释放

   * 另⼀部分是AVPacket字段指向的`data`内存，存放音视频压缩数据，这部分⼀般通过 ·av_packet_unref ·函数释放。

     ⼀般情况下，是在get后由调⽤者负责⽤ av_packet_unref 函数释放。特殊的情况是当碰到 packet_queue_flush 或put失败时，这时需要队列⾃⼰处理。



![image-20240106155344082](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20240106155344082.png)



如上图所示，左边是队头，右边是队尾，从左往右标注了4个节点的serial，以及放⼊对应节点时queue的 serial。 

可以看到放⼊flush_pkt的时候后，serial增加了

* 假设，现在要从队头取出⼀个节点，那么取出的节点是serial 1，⽽PacketQueue⾃身的queue已经增⻓到 了2。



PacketQueue设计思路：

1. 设计⼀个多线程安全的队列，保存AVPacket，同时统计队列内已缓存的数据⼤⼩。（这个统计数据会 ⽤来后续设置要缓存的数据量） 
2. 引⼊serial的概念，区别前后数据包是否连续，主要应⽤于seek（快进快退后，serial增加，即和原来的不是一个视频队列）操作。
3. 设计了两类特殊的packet——flush_pkt和nullpkt（类似⽤于多线程编程的事件模型——往队列中放⼊ flush事件、放⼊null事件），我们在⾳频输出、视频输出、播放控制等模块时也会继续对flush_pkt和 nullpkt的作⽤展开分析。
4. queue的大小不是无限的，一般通过外部定义一个maxsize确定，常设`maxsize = 15*1024*1024` ，即15M

#### 2.2 结构体定义

```cpp
// 定义
typedef struct MyAVPacketList {
    AVPacket		pkt;    //解封装后的数据
    struct MyAVPacketList	*next;  //下一个节点
    int			serial;     //播放序列
} MyAVPacketList;

typedef struct PacketQueue {
    MyAVPacketList	*first_pkt, *last_pkt;  // 队首，队尾指针指向最后一个元素
    int		  nb_packets;   // 包数量，也就是队列元素数量
    int		  size;         // 队列所有元素的数据大小总和
    int64_t   duration; // 队列所有元素的数据播放持续时间
    int		  abort_request; // 用户退出请求标志
    int		  serial;         // 播放序列号，和MyAVPacketList的serial作用相同，但改变的时序稍微有点不同
    SDL_mutex *mutex;     // 用于维持PacketQueue的多线程安全(SDL_mutex可以按pthread_mutex_t理解）
    SDL_cond  *cond;      // 用于读、写线程相互通知(SDL_cond可以按pthread_cond_t理解)
} PacketQueue;
```

> 【serial】
>
> serial字段主要⽤于标记当前节点的播放序列号，ffplay中多处⽤到serial的概念，主要⽤来区分是否连续 数据，每做⼀次seek，该serial都会做+1的递增，以区分不同的播放序列。
>
> serial字段在我们ffplay的分析 中应⽤⾮常⼴泛，谨记他是⽤来区分数据否连续先。
>
> queue中的serial的值，是整个queue中流的最大数目，因为加入一个不同的流的pkt时候，queue.serial总是先自增
>
> 在多线程的视频解码和播放中，可能会有多个线程同时处理和解码音频和视频包。这些包需要按照它们的时间戳（timestamp）顺序进行播放，以确保正确的时间同步和连续性。
>
> `serial` 字段的目的是为了确保这些包在发送到解码器进行解码之前按照正确的顺序进行排序。这样可以确保即使在多线程环境下，也能保持音视频同步和正确的播放顺序。
>
> 简而言之，`serial` 字段有助于确保在播放过程中音视频包的正确排序和同步，特别是在处理多线程解码时。
>
> MyAVPacketList的serial字段的赋值来⾃PacketQueue的serial，

----------------------

#### 2.3 接口

| 提供的接口                              | 描述                 |
| --------------------------------------- | -------------------- |
| [packet_queue_init](#packet_queue_init) | 初始化PacketQueue 【锁、条件变量、是否使用标志】 |
| [packet_queue_start](#packet_queue_start) | 启用PacketQueue  【加锁、更改使用标志、插入flush_pkt】 |
| [packet_queue_destroy](#packet_queue_destroy) | 删除整个PacketQueue 【节点、锁、条件变量】 |
| [packet_queue_flush](#packet_queue_flush) | 完全删除所有队列节点，除了锁和条件变量 |
| [packet_queue_abort](#packet_queue_abort)                    | 中止、强制退出【加锁、更改使用标志】 |
| [packet_queue_get](#packet_queue_get)                      | 获取节点的pkt、serial【加锁、有数据读取av_free，无数据非阻塞退出、无数据阻塞等待唤醒】 |
| [packet_queue_put_private](#packet_queue_put_private) | 向PacketQueue 【av_malloc插入packet、发送唤醒取数据信号】 |
| [packet_queue_put](#packet_queue_put)                      | 插入packet，【加锁、调用packet_queue_put_private】 |
| [packet_queue_put_nullpacket](#packet_queue_put_nullpacket)           | 插入空节点pkt |
| `av_malloc` | `pkt1 = av_malloc(sizeof(MyAVPacketList));` |
| `av_free` | `av_free(pkt1);` |

<a name="packet_queue_init">【packet_queue_init】</a>

```cpp
static int packet_queue_init(PacketQueue *q)
{
    memset(q, 0, sizeof(PacketQueue));
    // 1 初始化锁
    q->mutex = SDL_CreateMutex();
    if (!q->mutex) {
        av_log(NULL, AV_LOG_FATAL, "SDL_CreateMutex(): %s\n", SDL_GetError());
        return AVERROR(ENOMEM);
    }
    
    // 2 初始化条件遍历
    q->cond = SDL_CreateCond();
    if (!q->cond) {
        av_log(NULL, AV_LOG_FATAL, "SDL_CreateCond(): %s\n", SDL_GetError());
        return AVERROR(ENOMEM);
    }
    
    // 3 是否使用标志
    q->abort_request = 1;
    return 0;
}
```

<a name="packet_queue_destroy">【packet_queue_destroy】</a>

```cpp
static void packet_queue_destroy(PacketQueue *q)
{
    packet_queue_flush(q); //先清除所有的节点
    SDL_DestroyMutex(q->mutex);
    SDL_DestroyCond(q->cond);
}
```
<a name="packet_queue_start">【packet_queue_start】</a>

```cpp
static void packet_queue_start(PacketQueue *q)
{
    SDL_LockMutex(q->mutex);
    q->abort_request = 0;
    packet_queue_put_private(q, &flush_pkt); //这里放入了一个flush_pkt
    SDL_UnlockMutex(q->mutex);
}

```
<a name="packet_queue_abort">【packet_queue_abort】</a>

```cpp
static void packet_queue_abort(PacketQueue *q)
{
    SDL_LockMutex(q->mutex);

    q->abort_request = 1;       // 请求退出

    SDL_CondSignal(q->cond);    //释放一个条件信号

    SDL_UnlockMutex(q->mutex);
}
```
<a name="packet_queue_get">【packet_queue_get】</a>

```cpp
/* return < 0 if aborted, 0 if no packet and > 0 if packet.  */
/**
 * @brief packet_queue_get
 * @param q 队列
 * @param pkt 输出参数，即MyAVPacketList.pkt
 * @param block 调用者是否需要在没节点可取的情况下阻塞等待
 * @param serial 输出参数，即MyAVPacketList.serial
 * @return <0: aborted; =0: no packet; >0: has packet
 */
static int packet_queue_get(PacketQueue *q, AVPacket *pkt, int block, int *serial)
{
    MyAVPacketList *pkt1;
    int ret;

    SDL_LockMutex(q->mutex);    // 加锁

    for (;;) {
        if (q->abort_request) {
            ret = -1;
            break;
        }

        pkt1 = q->first_pkt;    //MyAVPacketList *pkt1; 从队头拿数据
        if (pkt1) {     //队列中有数据
            q->first_pkt = pkt1->next;  //队头移到第二个节点
            if (!q->first_pkt)
                q->last_pkt = NULL;
            q->nb_packets--;    //节点数减1
            q->size -= pkt1->pkt.size + sizeof(*pkt1);  //cache大小扣除一个节点
            q->duration -= pkt1->pkt.duration;  //总时长扣除一个节点
            //返回AVPacket，这里发生一次AVPacket结构体拷贝，AVPacket的data只拷贝了指针
            *pkt = pkt1->pkt;
            if (serial) //如果需要输出serial，把serial输出
                *serial = pkt1->serial;
            av_free(pkt1);      //释放节点内存,只是释放节点，而不是释放AVPacket
            ret = 1;
            break;
        } else if (!block) {    //队列中没有数据，且非阻塞调用
            ret = 0;
            break;
        } else {    //队列中没有数据，且阻塞调用
            //这里没有break。for循环的另一个作用是在条件变量满足后重复上述代码取出节点
            SDL_CondWait(q->cond, q->mutex);
        }
    }
    SDL_UnlockMutex(q->mutex);  // 释放锁
    return ret;
}
```
<a name="packet_queue_put">【packet_queue_put】</a>

```cpp
static int packet_queue_put(PacketQueue *q, AVPacket *pkt)
{
    int ret;

    SDL_LockMutex(q->mutex);
    ret = packet_queue_put_private(q, pkt);//主要实现
    SDL_UnlockMutex(q->mutex);

    if (pkt != &flush_pkt && ret < 0)
        av_packet_unref(pkt);       //放入失败，释放AVPacket

    return ret;
}
```
<a name="packet_queue_put_nullpacket">【packet_queue_put_nullpacket】</a>

```cpp
static int packet_queue_put_nullpacket(PacketQueue *q, int stream_index)
{
    // 创建空节点
    AVPacket pkt1, *pkt = &pkt1;
    av_init_packet(pkt);
    pkt->data = NULL;
    pkt->size = 0;
    pkt->stream_index = stream_index;
    // 插入
    return packet_queue_put(q, pkt);
}
```
<a name="packet_queue_flush">【packet_queue_flush】</a>

```cpp
// 完全删除整个队列
static void packet_queue_flush(PacketQueue *q)
{
    MyAVPacketList *pkt, *pkt1;

    // 上锁
    SDL_LockMutex(q->mutex);
    for (pkt = q->first_pkt; pkt; pkt = pkt1) {
        pkt1 = pkt->next;
        av_packet_unref(&pkt->pkt); // 清除pkt -> data
        av_freep(&pkt); // 完全释放节点
    }
    q->last_pkt = NULL;
    q->first_pkt = NULL;
    q->nb_packets = 0;
    q->size = 0;
    q->duration = 0;
    SDL_UnlockMutex(q->mutex);
}
```

<a name="packet_queue_put_private">【packet_queue_put_private】</a>

```cpp
static int packet_queue_put_private(PacketQueue *q, AVPacket *pkt)
{
    MyAVPacketList *pkt1;

    if (q->abort_request)   //如果已中止，则放入失败
        return -1;

    pkt1 = av_malloc(sizeof(MyAVPacketList));   //分配节点内存
    if (!pkt1)  //内存不足，则放入失败
        return -1;
    // 没有做引用计数，那这里也说明av_read_frame不会释放替用户释放buffer。
    pkt1->pkt = *pkt; //(解引用，pkt1存放的是对象，不是指针)拷贝AVPacket(浅拷贝，AVPacket.data等内存并没有拷贝)
    pkt1->next = NULL;
    if (pkt == &flush_pkt)//如果放入的是flush_pkt，需要增加队列的播放序列号，以区分不连续的两段数据
    {
        q->serial++;
        printf("q->serial = %d\n", q->serial);
    }
    pkt1->serial = q->serial;   //用队列序列号标记节点
    /* 队列操作：如果last_pkt为空，说明队列是空的，新增节点为队头；
     * 否则，队列有数据，则让原队尾的next为新增节点。 最后将队尾指向新增节点
     */
    if (!q->last_pkt)
        q->first_pkt = pkt1;
    else
        q->last_pkt->next = pkt1;
    q->last_pkt = pkt1;

    //队列属性操作：增加节点数、cache大小、cache总时长, 用来控制队列的大小
    q->nb_packets++;
    q->size += pkt1->pkt.size + sizeof(*pkt1);  // 加上节点的字节大小（pkt大小+next指针大小）？？？为什么不加serial大小
    q->duration += pkt1->pkt.duration;

    /* XXX: should duplicate packet data in DV case */
    //发出信号，表明当前队列中有数据了，通知等待中的读线程可以取数据了
    SDL_CondSignal(q->cond);
    return 0;
}
```



### 3 FrameQueue

#### 3.1 内存模型

* 由数组构成的循环队列

#### 3.2 结构体定义

```cpp
// 用来存储 音视频帧、字幕帧
typedef struct Frame {
    AVFrame		*frame;         // 指向数据帧
    AVSubtitle	sub;            // 用于字幕
    int		serial;             // 帧序列，在seek的操作时serial会变化
    
    double		pts;            // 时间戳，单位为秒（需要将新加的frame的pts时间戳进行转化）
    double		duration;       // 该帧持续时间，单位为秒 （计算帧率， 1/帧率）
    
    int64_t		pos;            // 该帧在输入文件中的字节位置
    int		width;              // 图像宽度
    int		height;             // 图像高读
    int		format;             // 对于图像为(enum AVPixelFormat)，
    // 对于声音则为(enum AVSampleFormat)
    AVRational	sar;            // 图像的宽高比（16:9，4:3...），如果未知或未指定则为0/1，该值来⾃AVFrame结构体的sample_aspect_ratio变量
    int		uploaded;           // 用来记录该帧是否已经显示过？
    int		flip_v;             // =1则垂直翻转， = 0则正常播放
} Frame;

typedef struct FrameQueue {
    Frame	queue[FRAME_QUEUE_SIZE];        // FRAME_QUEUE_SIZE  最大size, 数字太大时会占用大量的内存，需要注意该值的设置
    int		rindex;                         // 读索引。待播放时读取此帧进行播放，播放后此帧成为上一帧
    int		windex;                         // 写索引
    int		size;                           // 当前总帧数
    int		max_size;                       // 可存储最大帧数
    int		keep_last;                      // = 1，说明要在队列里面保持最后一帧的数据不释放，只在销毁队列的时候才将其真正释放
    int		rindex_shown;                   // 初始化为0，开启keep_last=1时候，peek_next将其设置为1，跳过节点，不删除
    SDL_mutex	*mutex;                     // 互斥量
    SDL_cond	*cond;                      // 条件变量
    PacketQueue	*pktq;                      // 指向解压数据的PacketQueue
} FrameQueue;
```

* keep_last模式：开启状态下
  1. 当队列有一个frame时候，调用peek_next释放frame，第一次进来时候，如果开启keep_last，就直接跳过，不会释放
  2. 这个时候，调用peek_last，才能获取到上一帧数据
  3. put节点时候，判断size == max， 读取节点readable时候，需要判断 size - rindex_shown <=0

#### 3.3 接口

| 函数名                                                | 描述                                                         |
| ----------------------------------------------------- | ------------------------------------------------------------ |
| [frame_queue_unref_item](#frame_queue_unref_item)     | 释放单个Frame里面的 `AVFrame` 和 `AVSubtitle`中的引用(释放未压缩的data数据)。 |
| [frame_queue_init](#frame_queue_init)                 | 初始化队列【开辟AVFrame空间】                                |
| [frame_queue_destory](#frame_queue_destory)           | 销毁队列【整个队列完全销毁】                                 |
| [frame_queue_signal](#frame_queue_signal)             | 发送唤醒信号。                                               |
| [frame_queue_nb_remaining](#frame_queue_nb_remaining) | 获取队列Frame节点个数。                                      |
| [frame_queue_last_pos](#frame_queue_last_pos)         | 获取最近播放Frame对应数据在媒体文件的位置，主要在seek时使用。 |
|                                                       |                                                              |
|                                                       |                                                              |

-------

【插入节点】

| 函数                                                    | 描述                                                         |
| ------------------------------------------------------- | ------------------------------------------------------------ |
| [frame_queue_peek_writable](#frame_queue_peek_writable) | 在queue中获取一个可写Frame的位置指针，可以以阻塞或非阻塞方式进行。 |
| [frame_queue_push](#frame_queue_push)                   | 更新写索引，此时Frame才真正入队列，队列节点Frame个数加1。    |
| [queue_picture](#queue_picture)                         | 在一个VideoState中，向FrameQueue插入一个AVPacket             |
|                                                         ||

-------

【读取节点、删除节点】

| 函数                                                    | 描述                                                         |
| ------------------------------------------------------- | ------------------------------------------------------------ |
| [frame_queue_peek_readable](#frame_queue_peek_readable) | 从队列头部读取⼀帧(vp)，只读取不删除，若⽆帧可读则等待。     |
| [frame_queue_peek](#frame_queue_peek)                   | 读取frame，少了阻塞等待的方式                                |
| [frame_queue_peek_next](#frame_queue_peek_next)         | 获取当前Frame的下一个Frame，调用之前先调用 `frame_queue_nb_remaining` 确保至少有2 Frame在队列。 |
| [frame_queue_peek_last](#frame_queue_peek_last)         | 获取上一个Frame。                                            |
| [frame_queue_next](#frame_queue_next)                   | 更新读索引，表面删除一个节点，队列节点Frame个数减1，内部调用 `frame_queue_unref_item` ，只是释放对应的 `AVFrame` 和 `AVSubtitle`，结构体还在 |
|                                                         |                                                              |
|                                                         |                                                              |

-------------------



<a name="frame_queue_unref_item">【frame_queue_unref_item】</a>

```cpp
// 释放单个元素所指向的压缩内容
static void frame_queue_unref_item(Frame *vp)
{
    av_frame_unref(vp->frame);	/* 释放指针所指数据，但是没有释放数组元素即没有释放结构体 */
    avsubtitle_free(&vp->sub);
}
```

<a name="frame_queue_init">【frame_queue_init】</a>

```cpp
/* 初始化FrameQueue，视频和音频keep_last设置为1，字幕设置为0 */
static int frame_queue_init(FrameQueue *f, PacketQueue *pktq, int max_size, int keep_last)
{
    int i;
    memset(f, 0, sizeof(FrameQueue));
    // 创建mutex
    if (!(f->mutex = SDL_CreateMutex())) {
        av_log(NULL, AV_LOG_FATAL, "SDL_CreateMutex(): %s\n", SDL_GetError());
        return AVERROR(ENOMEM);
    }
    // 创建cond
    if (!(f->cond = SDL_CreateCond())) {
        av_log(NULL, AV_LOG_FATAL, "SDL_CreateCond(): %s\n", SDL_GetError());
        return AVERROR(ENOMEM);
    }
    // 赋值
    f->pktq = pktq;
    f->max_size = FFMIN(max_size, FRAME_QUEUE_SIZE);
    f->keep_last = !!keep_last;
    // 开辟内存空间
    for (i = 0; i < f->max_size; i++)
        if (!(f->queue[i].frame = av_frame_alloc())) // 分配AVFrame结构体
            return AVERROR(ENOMEM);
    return 0;
}
```

* 队列初始化函数确定了队列⼤⼩，将为队列中每⼀个节点的frame( f->queue[i].frame )分配内存，注意只是分配Frame对象本身，⽽不关注Frame中的数据缓冲区。
* Frame中的数据缓冲区是 AVBuffer，使⽤引⽤计数机制。 f->max_size 是队列的⼤⼩，此处值为16（由FRAME_QUEUE_SIZE定义），实际分配的时候视 频为3，⾳频为9，字幕为16，因为这⾥存储的是解码后的数据，不宜设置过⼤，⽐如视频当为 1080p时，如果为YUV420p格式，⼀帧就有3110400字节。

```cpp
#define VIDEO_PICTURE_QUEUE_SIZE 3 
// 图像帧缓存数量 
#define SUBPICTURE_QUEUE_SIZE 16 
// 字幕帧缓存数量 
#define SAMPLE_QUEUE_SIZE 9 
// 采样帧缓存数量 
#define FRAME_QUEUE_SIZE FFMAX(SAMPLE_QUEUE_SIZE, FFMAX(VIDEO_PICTURE_QUEUE_SIZE, SUBPICTURE_QUEUE_SIZE))
```



<a name="frame_queue_destory">【frame_queue_destory】</a>

```cpp
static void frame_queue_destory(FrameQueue *f)
{
    int i;
    for (i = 0; i < f->max_size; i++) {
        Frame *vp = &f->queue[i];
        // 释放对vp->frame中的数据缓冲区的引用
        frame_queue_unref_item(vp);
        // 释放vp->frame对象
        av_frame_free(&vp->frame);
    }
    SDL_DestroyMutex(f->mutex);
    SDL_DestroyCond(f->cond);
}
```

<a name="frame_queue_signal">【frame_queue_signal】</a>

```cpp
static void frame_queue_signal(FrameQueue *f)
{
    SDL_LockMutex(f->mutex);
    SDL_CondSignal(f->cond);
    SDL_UnlockMutex(f->mutex);
}
```



<a name="frame_queue_peek_writable">【frame_queue_peek_writable】</a>

<a name="frame_queue_push">【frame_queue_push】</a>

```cpp
static Frame *frame_queue_peek_writable(FrameQueue *f)
{
    /* wait until we have space to put a new frame */
    SDL_LockMutex(f->mutex);
    while (f->size >= f->max_size &&      // 可写的时候不用减去rindex_show,因为未被删除的一个节点也占用1个空间
           !f->pktq->abort_request) {	   
        SDL_CondWait(f->cond, f->mutex);  // 队列满，阻塞，等待唤醒
    }
    SDL_UnlockMutex(f->mutex);

    if (f->pktq->abort_request)			 /* 检查是不是要退出 */
        return NULL;

    return &f->queue[f->windex];
}
// .....
// 设置frame变量，在创建队列时候，已经new，此时只需要修改结构体的各项值
// .....
static void frame_queue_push(FrameQueue *f)
{
    if (++f->windex == f->max_size)
        f->windex = 0;
    SDL_LockMutex(f->mutex);
    f->size++;
    SDL_CondSignal(f->cond);    //唤醒读取进程，frame_queue_peek_readable
    SDL_UnlockMutex(f->mutex);
}
```

* FrameQueue写队列的步骤和PacketQueue不同，分了3步进⾏：
  1. 调⽤frame_queue_peek_writable获取可写的Frame，如果队列已满则等待
  2. 获取到Frame后，设置Frame的成员变量 ，实际写入frame，但是没有更新索引
  3. 调⽤frame_queue_push更新队列的写索引，真正将Frame⼊队列

<a name="queue_picture">【queue_picture】</a>

```cpp
// 具体插入AVFrame的流程操作
// 先将src_frame的pts时间戳转化为秒单位

// 计算druation
// duration = 1/frame_rate 单位秒, 没有帧率时则设置为0, 有帧率帧计算出帧间隔
// av_q2d将rational形式化为浮点数
// ========================================================
duration = (frame_rate.num && frame_rate.den ? av_q2d((AVRational){frame_rate.den, frame_rate.num}) : 0);
// 根据AVStream timebase计算出pts值, 单位为秒
pts = (frame->pts == AV_NOPTS_VALUE) ? NAN : frame->pts * av_q2d(tb);
// 5 将解码后的视频帧插入队列
ret = queue_picture(is, frame, pts, duration, frame->pkt_pos, is->viddec.pkt_serial);

// =============================================================
static int queue_picture(VideoState *is, AVFrame *src_frame, double pts,
                         double duration, int64_t pos, int serial)
{
    Frame *vp;

    if (!(vp = frame_queue_peek_writable(&is->pictq))) // 检测队列是否有可写空间
        return -1;      // 请求退出则返回-1
    // 执行到这步说已经获取到了可写入的Frame
    vp->sar = src_frame->sample_aspect_ratio;
    vp->uploaded = 0;

    vp->width = src_frame->width;
    vp->height = src_frame->height;
    vp->format = src_frame->format;

    vp->pts = pts;
    vp->duration = duration;
    vp->pos = pos;
    vp->serial = serial;

    set_default_window_size(vp->width, vp->height, vp->sar);

    av_frame_move_ref(vp->frame, src_frame); // 将src中所有数据转移到dst中，并复位src。
    frame_queue_push(&is->pictq);   // 更新写索引位置
    return 0;
}

```

1. `frame_queue_peek_writable(&is->pictq) `向队列尾部申请⼀个可写的帧空间，若队列已满 ⽆空间可写，则等待(由`SDL_cond *cond`控制，由`frame_queue_next`或f`rame_queue_signal`触发唤 醒) 
2. `av_frame_move_ref(vp->frame, src_frame) `将`src_frame`中所有数据拷⻉到`vp-> frame`并复位`src_frame，vp-> frame`中`AVBuffer`使⽤引⽤计数机制，不会执⾏AVBuffer的拷⻉动作，仅是修改指针指向值。为避免内存泄漏，在 `av_frame_move_ref(dst, src) `之前应先调⽤ `av_frame_unref(dst)` ，这⾥ \没有调⽤，是因为`frame_queue`在删除⼀个节点时，已经释放了`frame`及`frame`中的`AVBuffer`。 
3. `frame_queue_push(&is->pictq) `此步仅将`frame_queue`中的写索引加1，实际的数据写⼊在此 步之前已经完成。

<a name="frame_queue_peek_readable">【frame_queue_peek_readable】</a>

```cpp
static Frame *frame_queue_peek_readable(FrameQueue *f)
{
    /* wait until we have a readable a new frame */
    SDL_LockMutex(f->mutex);
    while (f->size - f->rindex_shown <= 0 &&   // 读取时候，需要减去rindex_shown，即没有删除的frame所占用的位置
           									   //  比如： size = 4， 其实占用了1个，真正只有3个frame可读
           !f->pktq->abort_request) {
        SDL_CondWait(f->cond, f->mutex);   // 队伍空，没有数据可读，阻塞等待唤醒
    }
    SDL_UnlockMutex(f->mutex);

    if (f->pktq->abort_request)
        return NULL;

    return &f->queue[(f->rindex + f->rindex_shown) % f->max_size];
}
```

<a name="frame_queue_peek">【frame_queue_peek】</a>

```cpp
/* 获取队列当前Frame, 在调用该函数前先调用frame_queue_nb_remaining确保有frame可读 */
static Frame *frame_queue_peek(FrameQueue *f)
{
    return &f->queue[(f->rindex + f->rindex_shown) % f->max_size];
}

```

<a name="frame_queue_peek_next">【frame_queue_peek_next】</a>

```cpp
/* 获取当前Frame的下一Frame, 此时要确保queue里面至少有2个Frame */
// 不管你什么时候调用，返回来肯定不是 NULL
static Frame *frame_queue_peek_next(FrameQueue *f)
{
    return &f->queue[(f->rindex + f->rindex_shown + 1) % f->max_size];
}
```

<a name="frame_queue_peek_last">【frame_queue_peek_last】</a>

```cpp
/* 获取last Frame：
 * 当rindex_shown=0时，和frame_queue_peek效果一样
 * 当rindex_shown=1时，说明第一个frame没有删除，依次下来，读取的是已经显示过的上一个frame
 */
static Frame *frame_queue_peek_last(FrameQueue *f)
{
    return &f->queue[f->rindex];    // 这时候才有意义
}
```

<a name="frame_queue_next">【frame_queue_next】</a>

```cpp
/* 释放当前frame所指数据，并更新读索引rindex，
 * 当keep_last为1, rindex_show为0时不去更新rindex,也不释放当前frame */
static void frame_queue_next(FrameQueue *f)
{
    if (f->keep_last && !f->rindex_shown) {
        f->rindex_shown = 1; // 第一次进来没有更新，对应的frame就没有释放
        return;
    }
    
    // 除了第一次后，其余调用该函数，全部释放
    frame_queue_unref_item(&f->queue[f->rindex]); // 释放所指数据即可，并不删除该结构体本身，插入该位置时候，也只需要修改其对应的值
    if (++f->rindex == f->max_size) // 读索引++
        f->rindex = 0;
    SDL_LockMutex(f->mutex);
    f->size--;  // size--
    SDL_CondSignal(f->cond);    // 唤醒写入进程，frame_queue_peek_writable
    SDL_UnlockMutex(f->mutex);
}
```

### 4 Decoder

#### 4.1 结构体定义

```cpp
typedef struct Decoder {
    SDL_Thread	*decoder_tid;       // 线程句柄
    AVCodecContext	*avctx;     // 解码器上下文
    PacketQueue	*queue;         // 数据包队列
    
    SDL_cond *empty_queue_cond;  // 检查到packet队列空时发送 signal，唤醒read_thread读取数据
    int64_t	start_pts;          // 初始化时是stream的start time
    int	 pkt_serial;         // 当前解码器解码的序列，用于判断是否是同一播放序列的数据
    
    int		finished;           // =0，解码器处于工作状态；=非0，解码器处于空闲状态
    
    int		packet_pending;     // =0，解码器处于异常状态，需要考虑重置解码器；=1，解码器处于正常状态
    AVPacket pkt;              // 临时存放send失败的pkt
    
    AVRational	start_pts_tb;       // 初始化时是stream的time_base
    int64_t		next_pts;           // 当解码后的frame.pts无效时，用next_pts代替
    								// 有效时候，预测next_pts = frame.pts + frame.nb_samples								
    
    AVRational	next_pts_tb;        // next_pts的时间基， 用户设置值
    
} Decoder;
```

1. `avcodec_send_packet`后出现`AVERROR(EAGAIN)`，则说明我们要继续调⽤ `avcodec_receive_frame()`将`frame`读取，再调⽤`avcodec_send_packet`发`packet`。

2. 由于出现 `AVERROR(EAGAIN)`返回值解码器内部没有接收传⼊的packet，但⼜没法放回`PacketQueue`，所以我们 就缓存到了⾃封装的Decoder的pkt（即是`d->pkt`），并将 d->packet_pending = 1，以备下次继续使⽤ 该packet

```cpp
// 要将从queue中取出的pkt发送到解码器中
if (avcodec_send_packet(d->avctx, &pkt) == AVERROR(EAGAIN)) {
    d->packet_pending = 1;
    av_packet_move_ref(&d->pkt, &pkt);
}
```



#### 4.2 接口

解码器接口

| `函数`                                          | `作用`                               |
| ----------------------------------------------- | ------------------------------------ |
| [`decoder_init`](#decoder_init)                 | 给decoder绑定参数                    |
| [`decoder_start`](#decoder_start)               | 启动packet队列，创建解码线程         |
| [`decoder_decode_frame`](#decoder_decode_frame) | 获取解码后的==1帧frame帧==           |
| [`decoder_abort`](#decoder_abort)               | 关闭解码器                           |
| [`decoder_destroy`](#decoder_destroy)           | 释放解码器指针所指向内容，结构体还在 |

| 函数              | 作用                |
| ----------------- | ------------------- |
| `get_video_frame` | 获取解码后的frame帧 |



<a name="decoder_init">【decoder_init】</a>

```cpp
static void decoder_init(Decoder *d, AVCodecContext *avctx, PacketQueue *queue, SDL_cond *empty_queue_cond) {
    memset(d, 0, sizeof(Decoder));
    d->avctx = avctx;   // 解码器上下文
    d->queue = queue;   // 绑定对应的packet queue
    d->empty_queue_cond = empty_queue_cond; // 绑定read_thread线程的continue_read_thread
    d->start_pts = AV_NOPTS_VALUE;      // 起始设置为无效
    d->pkt_serial = -1;                 // 起始设置为-1
}

decoder_init(&is->auddec, avctx, &is->audioq, is->continue_read_thread);
```

<a name=""></a>

```cpp
static int decoder_start(Decoder *d, int (*fn)(void *), const char *thread_name, void* arg)
{
    packet_queue_start(d->queue);   // 启用对应的packet 队列
    d->decoder_tid = SDL_CreateThread(fn, thread_name, arg);    // 创建解码线程
    if (!d->decoder_tid) {
        av_log(NULL, AV_LOG_ERROR, "SDL_CreateThread(): %s\n", SDL_GetError());
        return AVERROR(ENOMEM);
    }
    return 0;
}

ret = decoder_start(&is->auddec, audio_thread, "audio_decoder", is)
```



<a name=""></a>

<a name="decoder_abort">【decoder_abort】</a>

```cpp
static void decoder_abort(Decoder *d, FrameQueue *fq)
{
    packet_queue_abort(d->queue);   // 终止packet队列，packetQueue的abort_request被置为1
    frame_queue_signal(fq);         // 唤醒Frame队列, 以便退出
    SDL_WaitThread(d->decoder_tid, NULL);   // 等待解码线程退出
    d->decoder_tid = NULL;          // 线程ID重置
    packet_queue_flush(d->queue);   // 情况packet队列，并释放数据
}
```



<a name="decoder_destroy">【decoder_destroy】</a>

```cpp
static void decoder_destroy(Decoder *d) {
    av_packet_unref(&d->pkt);
    avcodec_free_context(&d->avctx);
}
```



## 解码音视频过程

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20240116221345541.png" alt="image-20240116221345541"  />

1 获取解码后的frame帧

2 选取解码过程中的time_base

* vedio: 不变，为stream->time_base
* audio: 修改，为采样率的倒数

2 设置frame的pts

* vedio：`frame->pts = frame->pkt_pts = pkt->pts`
* audio: `frame->pts = 转换时间基`

3 将pts化为单位秒

4 插入frame队列

## 音频播放流程

![image-20240117174242363](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20240117174242363.png)

###  1 OpenAudioDevice

`SDL_OpenAudioDevice` 函数是SDL库中用于打开音频设备的函数。以下是该函数的参数及其解释：

```c
SDL_AudioDeviceID SDL_OpenAudioDevice(const char *dev, int iscapture,
                                      const SDL_AudioSpec *desired,
                                      SDL_AudioSpec *obtained,
                                      int allowed_changes);
```

1. `dev`: 字符串，指定要打开的音频设备的名称，或者为 `NULL` 表示使用默认音频设备。

2. `iscapture`: 整数，指示是否为音频捕获设备。如果设置为非零值，表示打开音频捕获设备；如果设置为零，表示打开音频播放设备。

3. `desired`: 指向 `SDL_AudioSpec` 结构的指针，包含期望的音频规格（例如，期望的通道数、采样率等）。

4. `obtained`: 指向 `SDL_AudioSpec` 结构的指针，用于存储实际获得的音频规格。这是一个输出参数，函数调用后包含实际配置的音频规格。

5. `allowed_changes`: 整数，表示允许的改变选项。可以使用按位或运算符组合以下常量：
   - `SDL_AUDIO_ALLOW_FREQUENCY_CHANGE`: 允许改变音频频率。
   - `SDL_AUDIO_ALLOW_FORMAT_CHANGE`: 允许改变音频格式。
   - `SDL_AUDIO_ALLOW_CHANNELS_CHANGE`: 允许改变通道数。

返回值是 `SDL_AudioDeviceID`，表示成功打开的音频设备的ID。如果打开失败，将返回 `0` 或负数，表示错误代码。

这个函数的作用是打开指定的音频设备，并根据期望的音频规格配置该设备。

```cpp
// 项目使用
// 打开设备，打开失败时候，循环组合采样率和通道数，直到打开
while (!(audio_dev = SDL_OpenAudioDevice(NULL, 0, &wanted_spec, &spec, SDL_AUDIO_ALLOW_FREQUENCY_CHANGE | SDL_AUDIO_ALLOW_CHANNELS_CHANGE))) {
    av_log(NULL, AV_LOG_WARNING, "SDL_OpenAudio (%d channels, %d Hz): %s\n",
           wanted_spec.channels, wanted_spec.freq, SDL_GetError());
    wanted_spec.channels = next_nb_channels[FFMIN(7, wanted_spec.channels)]; // 通道数逐渐缩小
    if (!wanted_spec.channels) {
        wanted_spec.freq = next_sample_rates[next_sample_rate_idx--];
        wanted_spec.channels = wanted_nb_channels;
        if (!wanted_spec.freq) {
            av_log(NULL, AV_LOG_ERROR,
                   "No more combinations to try, audio open failed\n");
            return -1;
        }
    }
    wanted_channel_layout = av_get_default_channel_layout(wanted_spec.channels);
}
```

### 2 数据结构

#### 2.1 AudioParams

* 是vediostate中的属性所使用的结构体

```cpp
struct AudioParams audio_src;           // 音频frame的参数，初始值=audio_tgt
struct AudioParams audio_tgt;       // 用来接收SDL设备实际的音频输出参数，重采样转换：audio_src->audio_tgt
```

* 用于存储解码frame即pcm的相关参数

```cpp
typedef struct AudioParams {
    int			freq;                   // 采样率
    int			channels;               // 通道数
    int64_t		channel_layout;         // 通道布局，比如2.1声道，5.1声道等
    enum AVSampleFormat	fmt;            // 音频采样格式，比如AV_SAMPLE_FMT_S16表示为有符号16bit深度，交错排列模式。
    int			frame_size;             // 一个采样单元占用的字节数（比如2通道时，则左右通道各采样一次合成一个采样单元）
    int			bytes_per_sec;          // 一秒时间的字节数，比如采样率48Khz，2 channel，16bit，则一秒48000*2*16/8=192000
} AudioParams;
```

#### 2.2 SDL_AudioSpec

* 用于存储SDL调用音频播放设备使用的参数信息

```cpp
typedef struct SDL_AudioSpec
{
    // 采样率
    int freq;                   /**< DSP frequency -- samples per second */
    // 采样格式
    SDL_AudioFormat format;     /**< Audio data format */
    // 通道数
    Uint8 channels;             /**< Number of channels: 1 mono, 2 stereo */
    // 是否静音
    Uint8 silence;              /**< Audio buffer silence value (calculated) */
    // 缓冲区相关
    // buffer存放的样本数
    Uint16 samples;             /**< Audio  buffer size in samples (power of 2) */
    Uint16 padding;             /**< Necessary for some compile environments */
    // buffer字节大小
    Uint32 size;                /**< Audio buffer size in bytes (calculated) */
    //回调函数
    SDL_AudioCallback callback;
    void *userdata;
} SDL_AudioSpec;
```

## 回调函数

```cpp
typedef struct AVFormatContext {
    ...
    AVIOInterruptCB interrupt_callback;
    ...
}

typedef struct AVIOInterruptCB {
    int (*callback)(void*); // 函数指针的声明,指向自定义回调函数
    void *opaque; // 指向 该结构体 所属的 播放器VideoState
} AVIOInterruptCB;
```

| 回调函数              | 功能                                   |
| --------------------- | -------------------------------------- |
| `decode_interrupt_cb` | 当read出现阻塞的时候就会调用该回调函数 |
| ``                    |                                        |
| ``                    |                                        |
| ``                    |                                        |
| ``                    |                                        |

### seek操作

#### avformat_seek_file

`avformat_seek_file` 函数是 FFmpeg 库中用于在媒体文件中进行定位（seeking）的函数。它主要用于设置媒体文件的读取位置，以便在文件中直接跳转到指定的时间或帧位置。这对于在媒体文件中进行快速导航、跳转或查找特定时间的帧非常有用。

函数原型如下：

```c
int avformat_seek_file(AVFormatContext *s, int stream_index, int64_t min_ts, int64_t ts, int64_t max_ts, int flags);
```

参数说明：
- `s`：`AVFormatContext` 结构体，表示媒体文件的上下文信息。
- `stream_index`：表示要定位的媒体流的索引。
- `min_ts`：表示允许的最小时间戳。
- `ts`：表示目标时间戳，即要跳转到的时间位置。
- `max_ts`：表示允许的最大时间戳。
- `flags`：附加的标志，用于指定定位的方式和行为。

这个函数允许你通过设置不同的参数，以不同的方式在媒体文件中进行定位。例如，你可以通过设置 `AVSEEK_FLAG_BACKWARD` 标志来进行反向定位，或者通过设置 `AVSEEK_FLAG_FRAME` 标志来在帧层面上进行定位。

需要注意的是，该函数可能不受所有封装格式的支持，而且定位的准确性也取决于封装格式和媒体文件本身的特性。

### 用户指定参数

```cpp
// 解码器
ffplay -acodec aac .flv
```



`av_dump_format`

## bug修改记录

![image-20240109191024884](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20240109191024884.png)

#### 错误1

根据`No more combinations to try, audio open failed`错误，定位到ffplay.c如下函数

```cpp
while (!(audio_dev = SDL_OpenAudioDevice(NULL, 0, &wanted_spec, &spec, SDL_AUDIO_ALLOW_FREQUENCY_CHANGE | SDL_AUDIO_ALLOW_CHANNELS_CHANGE))) {
    av_log(NULL, AV_LOG_WARNING, "SDL_OpenAudio (%d channels, %d Hz): %s\n",
           wanted_spec.channels, wanted_spec.freq, SDL_GetError());
    wanted_spec.channels = next_nb_channels[FFMIN(7, wanted_spec.channels)];
    if (!wanted_spec.channels) {
        wanted_spec.freq = next_sample_rates[next_sample_rate_idx--];
        wanted_spec.channels = wanted_nb_channels;
        if (!wanted_spec.freq) {
            av_log(NULL, AV_LOG_ERROR,
                   "No more combinations to try, audio open failed\n");
            return -1;
        }
    }
    wanted_channel_layout = av_get_default_channel_layout(wanted_spec.channels);
}
```

1. 本函数作用是打开音频设备播放音频数据，经过调试得到`!wanted_spec.freq)`值为0，因此报错：`No more combinations to try, audio open failed\n`

2. 向上找：`wanted_spec.freq = next_sample_rates[next_sample_rate_idx--];`，判断是`next_sample_rate_idx`值出现问题

3. 再次定位如下代码：

   ```cpp
   while (next_sample_rate_idx && next_sample_rates[next_sample_rate_idx] >= wanted_spec.freq)
           next_sample_rate_idx--;  
   ```

   这个函数作用是从**采样率数组中找到第一个不大于传入参数wanted_sample_rate的值**

   此时，` wanted_spec.freq=44100`，`next_sample_rate_idx = 4`,`next_sample_rates = {0, 44100, 48000, 96000, 192000}`

   函数返回时，`next_sample_rate_idx = 0`，从而导致`wanted_spec.freq = 0 `，最终报错

   > **修改**：大于等于号变为大于号
   >
   > ```cpp
   > while (next_sample_rate_idx && next_sample_rates[next_sample_rate_idx] > wanted_spec.freq)
   > ```
   >
   > 此时，`next_sample_rate_idx = 1`，`wanted_spec.freq = 44100 `

#### 错误2

![image-20240109192949312](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20240109192949312.png)

这条错误消息表明当你尝试使用 SDL 打开音频设备时，发生了一个错误，特别是在使用 XAudio2 API 时。

XAudio2 是 Microsoft DirectX SDK 中的一部分，它是用于在 Windows 平台上进行高性能音频处理和音频渲染的音频库。

经过chatgpt和查阅资料，解决方案是：添加`CoInitialize(NULL)` 在 Windows 环境中初始化 COM 。

> 步骤：
>
> 1 在`read_phtread()`函数内添加代码
>
> ```cpp
> CoInitialize(NULL);
> ```
>
> 2 `ffplay.c`包含头文件
>
> ```cpp
> #include <objbase.h>
> ```
>
> 3 `.pro`文件中添加lib库
>
> ```cpp
> $$PWD/Ole32.Lib
> ```
>
> * 使用everything查找`Ole32.Lib`
> * 项目是32下，因此选取的是x86的lib文件
> * 将lib放入项目目录下（不是运行目录，运行目录下存放的是dll动态库）

<img src="C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20240109193913008.png" alt="image-20240109193913008"  />

修改完以上两个错误，声音正常播放！

# webRtc

## 环境配置

ubuntun22下

### 1 nodejs

1. 下载源码

   ```
   https://nodejs.org/dist/   下载为21.5.0版本
   ```

   ![image-20240304220604212](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20240304220604212.png)

```cpp
路径：/home/yuqiao/webRTC/nodejs/node-v21.5.0-linux-x64
# 解压
tar ‐xvf node‐v21.5.0-‐linux‐x64.tar.xz
# 进入目录
cd node‐v21.5.0-‐linux‐x64/

```

2. 链接执行文件

```cpp
sudo ln -s ~/webRTC/nodejs//node-v21.5.0-linux-x64/bin/npm /usr/local/bin
udo ln -s ~/webRTC/nodejs//node-v21.5.0-linux-x64/bin/node /usr/local/bin
# 查看是否安装，安装正常则打印版本号
node ‐v
npm ‐v
```

3. 创建第一个server.js文件

```cpp
// 命令行
gedit server.js
    
// 内容如下
var http = require('http');
http.createServer(function (request, response) {
    // 发送 HTTP 头部
    // HTTP 状态值: 200 : OK
    // 内容类型: text/plain
    response.writeHead(200, {'Content‐Type': 'text/plain'});  // 注意-复制错误
    // 发送响应数据 "Hello World"
    response.end('Hello World\n');
}).listen(8888);
// 终端打印如下信息
console.log('Server running at http://127.0.0.1:8888/');
    
```

```cpp
// 运行
node server.js
// 显示：
Server running at http://127.0.0.1:8888/

// 浏览器上查看
```

### 2 sturn

1. 安装依赖

   ```
   sudo apt‐get install libssl‐dev
   sudo apt‐get install libevent‐dev
   ```

2. 编译安装

```cpp
// cd 安装目录
git clone https://github.com/coturn/coturn
cd coturn
./configure
make
sudo make install

```

3. 检测安装成功

   ```cpp
   # nohup是重定向命令，输出都将附加到当前目录的 nohup.out 文件中； 命令后加 & ,后台执行起来后按
   ctr+c,不会停止
   sudo nohup turnserver ‐L 0.0.0.0 ‐a ‐u lqf:123456 ‐v ‐f ‐r nort.gov &
   #然后查看相应的端口号3478是否存在进程
   sudo lsof ‐i:3478
   
   ```

   

4. 进入网址测

[测试网址](https://webrtc.github.io/samples/src/content/peerconnection/trickle-ice/)

```cpp
stun:192.168.88.130:3478[yuqiao:123456]
turn:192.168.88.130:3478[yuqiao:123456]
```

* 显示done，则安装成功



# 流媒体

[流媒体服务器搭建](E:\typora索引文件\ffmpeg\流媒体\1 RTMP流媒体服务器搭建.pdf)

![image-20240305152158564](C:\Users\zhang\AppData\Roaming\Typora\typora-user-images\image-20240305152158564.png)

```
git clone https://gitee.com/ossrs/srs
cd srs
// 显示分支
git branch -a
// 切换5.0版本
git checkout -b 5.0 remotes/origin/5.0release
// 查看当前分支为5.0
git branch -a

cd srs/trunk
./configure
make

// 启动服务器
./objs/srs -c conf/srs.conf

// 是否运行
./etc/init.d/srs status
// SRS(pid 24303) is running.   

// 推流
ffmpeg -re -i ./doc/source.flv -c copy -f flv -y rtmp://localhost/live/livestream

// vlc查看
rtmp://localhost/live/livestream
```

```cpp
// 关闭进程， 端口为1935
sudo lsof : 1935
kill-9 pid
```



```
packetqueue.h
packetqueue.cpp
avsync.h
avsync.cpp
audiooutsdl.cpp
audiooutsdl.
```

