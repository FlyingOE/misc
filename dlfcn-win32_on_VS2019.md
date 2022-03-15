# 在VS2019上编译`dlfcn-win32`

[dlfcn-win32](https://github.com/dlfcn-win32/dlfcn-win32/blob/master/README.md)是一个在Windows上的符合POSIX标准的dlfcn实现：

- http://www.opengroup.org/onlinepubs/009695399/basedefs/dlfcn.h.html
- http://www.opengroup.org/onlinepubs/009695399/functions/dlerror.html
- http://www.opengroup.org/onlinepubs/009695399/functions/dlsym.html
- http://www.opengroup.org/onlinepubs/009695399/functions/dlclose.html
- http://www.opengroup.org/onlinepubs/009695399/functions/dlopen.html

## Change History

- 2022.03.14
   - Initial version
- 2022.03.15
   - Add automatic install via vcpkg

## 通过vcpkg编译

以下步骤假定使用的环境是：

1. Microsoft Visual Studio 2019社区版，并安装有C++编程支持
2. 通过[vcpkg](https://github.com/Microsoft/vcpkg#quick-start-windows)下载、编译和安装相关的库

以下步骤包括了x86（32位）和x86\_64（64位）的支持。

### 编译库

1. 先启动开发环境：
   - 编译32位库：开始菜单 >> `x86 Native Tools Command Prompt fo VS 2019`  
     或  
     编译64位库：开始菜单 >> `x64 Native Tools Command Prompt fo VS 2019`
   - ▼ 以下所有指令都假定在此开发环境中执行
   
2. 下载vcpkg：
   ```powershell
   git clone https://github.com/microsoft/vcpkg
   .\vcpkg\bootstrap-vcpkg.bat
   ```
   
3. 通过vcpkg下载、编译、安装程序库：

| 链接方式 | 32/64位 | 编译类型 | vcpkg库名 | `CMAKE_TOOLCHAIN_FILE` | `VCPKG_TARGET_TRIPLET` |
|:---:|:-------:|:-----:|:---:|:---:|:---:|
| DLL | 32 | Release/Debug | `dlfcn-win32:x86-windows` | `{path_to_vcpkg}/scripts/buildsystems/vcpkg.cmake` | `x86-windows` |
| LIB | 32 | Release/Debug | `dlfcn-win32:x86-windows-static` | `{path_to_vcpkg}/scripts/buildsystems/vcpkg.cmake` | `x86-windows-static` |
| DLL | 64 | Release/Debug | `dlfcn-win32:x64-windows` | `{path_to_vcpkg}/scripts/buildsystems/vcpkg.cmake` | `x64-windows` |
| LIB | 64 | Release/Debug | `dlfcn-win32:x64-windows-static` | `{path_to_vcpkg}/scripts/buildsystems/vcpkg.cmake` | `x64-windows-static` |

1. 下载、编译程序库
   ```powershell
   .\vcpkg\vcpkg install {vcpkg库名}
   ```
   
2. 在VS缺省编译配置中增加通过vcpkg安装的库
   ```bash
   .\vcpkg\vcpkg integrate install
   ```
   如果使用CMake，则在`CMakeLists.txt`中增加
   ```cmake
   find_package(dlfcn-win32 CONFIG REQUIRED)
   target_link_libraries({proj} PRIVATE dlfcn-win32::dl)
   ```


### References

- https://devblogs.microsoft.com/cppblog/vcpkg-updates-static-linking-is-now-available/

## 手动编译

以下步骤假定使用的是Microsoft Visual Studio 2019社区版，并安装有以下功能：

1. C++编程支持
2. CMake支持

另外，源码库需要使用Git下载／同步。

以下步骤包括了x86（32位）和x86\_64（64位）上静态库（LIB）和动态库（DLL）的支持，并可选择生成Debug或Release库。

### 编译库

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