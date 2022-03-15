# 在VS2019上编译`dlfcn-win32`

[dlfcn-win32](https://github.com/dlfcn-win32/dlfcn-win32/blob/master/README.md)是一个在Windows上的符合POSIX标准的dlfcn实现：

- http://www.opengroup.org/onlinepubs/009695399/basedefs/dlfcn.h.html
- http://www.opengroup.org/onlinepubs/009695399/functions/dlerror.html
- http://www.opengroup.org/onlinepubs/009695399/functions/dlsym.html
- http://www.opengroup.org/onlinepubs/009695399/functions/dlclose.html
- http://www.opengroup.org/onlinepubs/009695399/functions/dlopen.html

## Change History

- <time datetime="2022-03-14">2022.03.14</time>
   - Initial version

## 编译环境

以下步骤假定使用的是Microsoft Visual Studio 2019社区版，并安装有以下功能：

1. C++编程支持
2. CMake支持

另外，源码库需要使用Git下载／同步。

以下步骤包括了x86（32位）和x86\_64（64位）上静态库（LIB）和动态库（DLL）的支持，并可选择生成Debug或Release库。

## 编译库

1. 先启动开发环境：
   - 开始菜单 >> `Developer Command Prompt for VS 2019`
   - ▼ 以下所有指令都假定在此开发环境中执行

2. 从GitHub下载源代码：
   ```powershell
   git clone https://github.com/FlyingOE/dlfcn-win32.git dlfcn-win32
   cd dlfcn-win32
   # 增加源代码的upstream源
   git remote add upstream https://github.com/dlfcn-win32/dlfcn-win32.git
   git fetch upstream
   ```

3. 编译、安装程序库：

   | 链接方式 | 32/64位 | `--config`/`CMAKE_BUILD_TYPE` | `CMAKE_GENERATOR_PLATFORM` | `BUILD_SHARED_LIBS` | 编译目录 | 安装目录 |
   |:---:|:--:|:-------:|:-------:|:-----:|:-----------:|:-----------:|
   | LIB | 32 | `Debug` | `Win32` | `OFF` | `build.x86` | `install.x86-static-d`    |
   | LIB | 32 | `Release` | `Win32` | `OFF` | `build.x86` | `install.x86-static`    |
   | LIB | 64 | `Debug` | `x64` | `OFF` | `build.x64` | `install.x64-static-d` |
   | LIB | 64 | `Release` | `x64` | `OFF` | `build.x64` | `install.x64-static` |
   | DLL | 32 | `Debug` | `Win32` | `ON` | `build.x86` | `install.x86-d` |
   | DLL | 32 | `Release` | `Win32` | `ON` | `build.x86` | `install.x86` |
   | DLL | 64 | `Debug` | `x64` | `ON` | `build.x64` | `install.x64-d` |
   | DLL | 64 | `Release` | `x64` | `ON` | `build.x64` | `install.x64` |

   1. 生成编译环境
      ```powershell
      cd dlfcn-win32
      # BUILD_SHARED_LIBS 缺省为 ON
      # BUILD_TESTS 缺省为 OFF
      # CMAKE_GENERATOR_PLATFORM 缺省为 x64
      # 如果使用 MS Build 等支持多配置的编译系统：
      cmake -S . -B {编译目录} -D CMAKE_GENERATOR_PLATFORM={见上表} -D BUILD_SHARED_LIBS={见上表} -D BUILD_TESTS=ON -D CMAKE_DEBUG_POSTFIX=d
      # 如果使用 Ninja 等不支持多配置的编译系统：
      cmake -S . -B {编译目录} -D CMAKE_GENERATOR_PLATFORM={见上表} -D BUILD_SHARED_LIBS={见上表} -D BUILD_TESTS=ON -D CMAKE_BUILD_TYPE={见上表} -D CMAKE_DEBUG_POSTFIX=d
      ```

   2. 编译程序库
      ```powershell
      # 编译时，config 缺省为 Debug
      cmake --build {编译目录} --config {见上表}
      ```

   3. 测试程序库
      ```powershell
      # 只有在 BUILD_TESTS 为 ON 时才能测试
      cd dlfcn-win32\{编译目录}\tests
      ctest -N  -C {见上表CMAKE_BUILD_TYPE}    # 查看测试列表
      ctest -VV -C {见上表CMAKE_BUILD_TYPE}    # 运行自动测试（显示详情）
      ```

   4. 安装程序库
      ```powershell
      # 安装时，--config 缺省为 Release
      cmake --install {编译目录} --prefix {安装目录} --config {见上表}
      ```