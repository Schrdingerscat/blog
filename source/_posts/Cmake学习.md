---
title: Cmake学习
date: 2023-12-04 20:06:39
tags: cpp
description: CMake 是一个开源的构建工具，用于管理和构建跨平台的 C/C++ 项目。
---
# CMake 文档

## 介绍

CMake 是一个开源的构建工具，用于管理和构建跨平台的 C/C++ 项目。它提供了一个简洁的构建系统，并通过生成平台特定的构建文件（如 Makefile 或 Visual Studio 解决方案）来实现跨平台性。本文档将介绍 CMake 的基本概念、命令和用法，以帮助你编写和管理 CMake 构建脚本。

## 安装

在开始之前，你需要先安装 CMake。你可以从 CMake 官方网站（https://cmake.org）下载适合你操作系统的安装程序，并按照官方指南进行安装。

## 编写 CMakeLists.txt

CMake 使用名为 CMakeLists.txt 的文件来描述项目的构建规则。在你的项目根目录下创建一个 CMakeLists.txt 文件，并按照以下结构编写你的构建脚本：

``` cmake
cmake_minimum_required(VERSION <version>)
project(<project_name>)

# 设置变量和选项

# 添加子目录

# 添加可执行文件或库

# 链接库和依赖

# 安装规则和脚本
```

下面将详细介绍每个部分的内容。

#### 3.1 cmake_minimum_required

``` cmake
cmake_minimum_required(VERSION <version>)
```

cmake_minimum_required 命令指定所需的 CMake 最低版本。你应该根据你的项目要求指定适当的 CMake 版本。

#### 3.2 project

``` cmake
project(<project_name>)
project 命令定义项目名称。你需要指定一个唯一的项目名称，以便在构建过程中引用。
```

#### 3.3 设置变量和选项

在这一部分，你可以设置项目所需的变量和选项。例如，你可以设置编译器选项、构建类型、输出路径等。以下是一些示例命令：

``` cmake
set(CMAKE_CXX_FLAGS "-Wall -std=c++11")
set(CMAKE_BUILD_TYPE "Debug")
set(CMAKE_INSTALL_PREFIX "/usr/local")
```

#### 3.4 添加子目录

如果你的项目包含多个子目录，你可以使用 add_subdirectory 命令将它们添加到构建过程中。例如：

``` cmake
add_subdirectory(src)
add_subdirectory(test)
```

这样，CMake 将进入每个子目录并处理其中的 CMakeLists.txt 文件。

#### 3.5 添加可执行文件或库

你可以使用 add_executable 命令添加可执行文件或使用 add_library 命令添加库。以下是一些示例命令：

``` cmake
add_executable(myapp main.cpp)
add_library(mylib mylib.cpp)
```

#### 3.6 链接库和依赖

如果你的项目依赖于其他库，你可以使用 target_link_libraries 命令将它们链接到你的可执行文件或库。例如：

``` cmake
target_link_libraries(myapp mylib)
```

这将链接 mylib 库到 myapp 可执行文件。

#### 3.7 安装规则和脚本

你可以使用 install 命令定义安装规则和脚本。通过这些规则，你可以指定要安装的文件、目录和权限。以下是一些示例命令：

``` cma
install(TARGETS myapp DESTINATION bin)
install(FILES myheader.h DESTINATION include)
install(DIRECTORY mydir DESTINATION share)
```


这些命令是 CMake 的基本结构和常用命令。根据你的项目需求，你可以根据这些命令编写自己的 CMakeLists.txt 文件。

### 构建项目

完成 CMakeLists.txt 文件后，你可以开始构建项目了。在项目根目录下创建一个名为 build 的目录，并进入该目录。然后执行以下命令：

``` bash
cmake ..
```

这将生成平台特定的构建文件（如 Makefile 或 Visual Studio 解决方案）。

接下来，你可以使用适合你的构建系统执行构建命令。例如，如果你使用的是 Makefile，可以执行：

``` bash
make
```

如果你使用的是 Visual Studio 解决方案，可以打开解决方案并进行构建。

### 高级用法

除了上述基本用法外，CMake 还提供了许多高级功能，用于处理更复杂的项目需求。以下是一些常用的高级用法：

使用 find_package 命令查找并加载外部库和模块。

使用条件语句（如 if、else 和 endif）根据条件设置变量和选项。

使用函数和宏来封装重复的代码块。

使用自定义命令和脚本执行额外的构建步骤。

使用 CTest 进行测试。

更多高级用法和命令，请参阅 CMake 官方文档。

6. 总结
   本文档介绍了 CMake 的基本概念、命令和用法，帮助你编写和管理 CMake 构建脚本。通过合理使用 CMake，你可以简化项目的构建过程，并实现跨平台的支持。记得查阅官方文档以获取更多详细信息和示例。

## vcpkg构建跨平台项目

[使用经典模式入门 | Microsoft Learn](https://learn.microsoft.com/zh-cn/vcpkg/examples/installing-and-using-packages#cmake)



要与 vcpkg 一起使用 CMake 构建跨平台项目，可以按照以下步骤进行设置：

1. 安装 vcpkg：

   - 前往 vcpkg 的 GitHub 页面（https://github.com/microsoft/vcpkg）并按照官方文档的说明进行安装。确保将 vcpkg 安装在你的系统中，并且添加到系统的 PATH 环境变量中。

2. 集成 vcpkg 到 CMake 项目：

   - 在项目的根目录中创建一个名为 `CMakeLists.txt` 的文件。

   - 在 `CMakeLists.txt` 文件中，添加以下内容来启用 vcpkg 的集成：

     ```cmake
     set(VCPKG_ROOT "安装vcpkg的目录")
     set(CMAKE_TOOLCHAIN_FILE "$VCPKG_ROOT/scripts/buildsystems/vcpkg.cmake"
         CACHE STRING "Vcpkg toolchain file")
     ```

     将 `<vcpkg_root>` 替换为你安装 vcpkg 的根目录路径。

3. 指定项目的依赖项：

   - 使用 `find_package` 命令来查找并加载 vcpkg 中的库。例如，要添加 OpenSSL 作为项目的依赖项，可以使用以下命令：

```cmake
find_package(OpenSSL REQUIRED)
```

   - 可以根据你的项目需求，添加更多的 `find_package` 命令来引入其他 vcpkg 中的库。

对于庞大的Boost库，引入的时候需要注意，不能够仅仅写`find_package(Boost REQUIRED)`

而是引入需要引入的库，比如Asio库需要使用

``` cmake
find_package(Boost REQUIRED COMPONENTS system)
```

4. 链接依赖项：

   - 在 `CMakeLists.txt` 文件中，使用 `target_link_libraries` 命令将 vcpkg 中的库链接到你的可执行文件或库。例如：

     ```cmake
     target_link_libraries(${PROJECT_NAME} PRIVATE OpenSSL::Crypto OpenSSL::SSL)
     ```

     这将链接 OpenSSL 库到 `myapp` 可执行文件或库。

   - 最好加上链接库和包含文件的目录

     ``` cmake
     target_include_directories(${PROJECT_NAME} PRIVATE "${VCPKG_ROOT}/installed/x64-windows/include")
     
     target_link_libraries(${PROJECT_NAME} PRIVATE "${VCPKG_ROOT}/installed/x64-windows/lib")
     ```

5. 构建项目：

   - 在项目根目录下创建一个名为 `build` 的目录，并进入该目录。

   - 执行以下命令生成平台特定的构建文件：

     ```bash
     cmake ..
     ```

   - 根据你的构建系统执行构建命令。例如，在linux平台上，如果你使用的是 Makefile，可以执行：

     ```bash
     make
     ```

   - 如果在windows平台上

     ``` bash
     $~/project/build cmake --build .
     ```

通过以上步骤，你就可以使用 vcpkg 和 CMake 来构建跨平台项目。确保在 `CMakeLists.txt` 文件中正确设置 vcpkg 的工具链文件路径，并使用适当的 `find_package` 命令来引入依赖项。















