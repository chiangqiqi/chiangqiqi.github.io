---
layout: post
title:  "C++ 开发工具问题整理"
date:   2019-03-10 11:58:58 +0800
categories: programming
---

一直在用 `Python`, 很久没有用过 `c++`. 最近在做的项目里有一个基础库，用`c++` 实
现的，比较痛苦，又重新熟悉了一遍 `c++` 的开发工具。最开始初衷只是解决 `Emacs` 中
`c++` 代码的跳转问题，当然这个看似简单的问题实际并不是那么简单。 

# 补全
Emacs 中常用的 `c++` 代码跳转工具是 `rtags`, `rtags` 需要读入 `compilation_commands.json` 

# 构建工具

## bazel
`bazel` 是google 开发的 c++ 的构建工具，比较好用。但是对于生成
`compile_commands.json` 不是很好用。
一般使用 `Bear` 来监听构建工具用来编译时候使用的命令来构建
`compile_commands.json`。

但是这个工具在

由于现在使用的桌面系统是 `macOS Mojave`, `Bear` 不是很好使，只好曲线救国，现将现
在的构建工具 `bazel` 生成 `CMakeList.txt`，继而使用 `cmake` 生成
`compile_commands.json`。

## CMake

`cmake` 也是一种常见的构建系统，因为 `bear` 的问题，考虑使用 `cmake` 来生成 
`compile_commands.json`, 而 `CMakeList.txt` 也可以从 `bazel` 中的依赖关系中生成。

[bazel-cmakelists](https://github.com/lizan/bazel-cmakelists)，讲当前的 bazel 转
换为 `cmake`. 继而生成 `compile_commands.json`. 从而可以支持 `Emacs` 中 c++ 代码
的自由跳转。

上面的工具中生成的 `CMakeList` 没有包含 `boost`，再手工在 `Cmake` 中添加 `boost`

```cmake
set(Boost_USE_STATIC_LIBS OFF)
set(Boost_USE_MULTITHREADED ON)
set(Boost_USE_STATIC_RUNTIME OFF)
find_package(Boost 1.55.0 COMPONENTS random)

include_directories(${Boost_INCLUDE_DIRS})
```
之后执行 `cmake -DCMAKE_EXPORT_COMPILE_COMMANDS=ON .\n`

大功告成！终于可以 
