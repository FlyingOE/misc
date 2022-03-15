# `libffi`

[libffi](https://sourceware.org/libffi/)是一个可移植的外部函数接口（Foreign Function Interface）库。它被广泛应用在如[CPython](http://python.org/)、[OpenJDK](http://openjdk.org/)、[Java Native Access (JNA)](https://jna.dev.java.net/)、[gcj](http://gcc.gnu.org/)等系统中，并可支持在所有常见的CPU架构和操作系统上对接外部函数。

## Change History

- 2022.03.15
   - Initial version

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
| DLL | 32 | Release/Debug | `libffi:x86-windows` | `{path_to_vcpkg}/scripts/buildsystems/vcpkg.cmake` | `x86-windows` |
| LIB | 32 | Release/Debug | `libffi:x86-windows-static` | `{path_to_vcpkg}/scripts/buildsystems/vcpkg.cmake` | `x86-windows-static` |
| DLL | 64 | Release/Debug | `libffi:x64-windows` | `{path_to_vcpkg}/scripts/buildsystems/vcpkg.cmake` | `x64-windows` |
| LIB | 64 | Release/Debug | `libffi:x64-windows-static` | `{path_to_vcpkg}/scripts/buildsystems/vcpkg.cmake` | `x64-windows-static` |

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
   find_package(libffi CONFIG REQUIRED)
   target_link_libraries({proj} PRIVATE libffi)
   ```
   

### References

- https://devblogs.microsoft.com/cppblog/vcpkg-updates-static-linking-is-now-available/
- https://github.com/libffi/libffi/blob/master/README.md
   - https://github.com/libffi/libffi/issues/582