---
layout: post
title:  "Doom Emacs 中的 C++ 开发环境"
date:   2020-01-03
categories: programming
---

# 重拾 C++
上一次严肃的用 C++ 可能还是研究生的时候学习图形学的时候，这么多年自己用 `Python`
用的最多，可能做大数据的时候还用一些 `Scala` , 自己去做一些小东西的时候也会选择
`Haskell` , `Lisp` 之类。 C++对我个人来说是一个最后的选择，我不讨厌 C++, 但如果
我有别的选择的时候也一般果断的会选择其他选项，C++ 需要注意的细节还是太多会分散很
多精力以至于无法把大部分精力放在该放的地方。 

这次遇到的问题是我试图在本地做一个日麻的环境，找来找去也没有找到比较合适的选择，
恰巧去年年底的时候 [botzone](https://botzone.org.cn/game/Riichi-Mahjong) 上线了
日麻的环境，我自己在 botzone 上玩过，于是乎就找到了 botzone 上的代码来做一个简单
的日麻环境。

这个日麻无论是裁判代码还是客户端玩家的代码都是 C++ ，北大的小伙伴看来还是很钟爱
C++ 的么。 于是把这些 `mahjong.cpp` 搬到本地，可能自己也会做一些修改。

# 环境配置
长久以来都是用 `Emacs` 作为自己的编辑器，这两年接触了 `Doom Emacs` 之后也就一直
没有换过，所以常规操作，先在 `init.el` 中反注释掉 `cc` 这一行，`./bin/doom
update` 一下将需要的包安装一下。

但是不出所料还是遇到了问题，当然我们 Emacs 用户如果不遇到问题那还和 Visual
Studio Code 的用户有什么两样。兵来将挡，水来土掩，还是需要自己厘清楚问题，然后一
个一个解决喽。

## 问题一 Irony 编译安装
`Doom Emacs` 默认使用的补全和分析工具是 `irony-mode` 但是这个工具是默认没有安装
的，问题也不大，找到了 `doom` 中 `cc` 目录下的 `README` 开始按照提供的命令安装
irony.

```
brew install cmake
brew install llvm  # 1gb+ installation! May take a while!

git clone https://github.com/Sarcasm/irony-mode irony-mode
mkdir irony-mode/server/build
pushd irony-mode/server/build

DEST="$HOME/.emacs.d/.local/etc/irony-server/"
cmake -DCMAKE_INSTALL_RPATH_USE_LINK_PATH=ON \
      -DCMAKE_INSTALL_PREFIX="$DEST" ../
cmake --build . --use-stderr --config Release --target install

install_name_tool -change @rpath/libclang.dylib \
    /usr/local/opt/llvm/lib/libclang.dylib \
    "$DEST/bin/irony-server"

# cleanup
popd
rm -rf irony-mode
```

但是这个代码的 cmake 那一步不对，貌似是找不到 clang 的 `.cmake` 文件，搜索了一下
这个问题的原因应该是 `llvm` 的 prefix 没有配置，加上了选项
`-DCMAKE_PREFIX_PATH=/usr/local/opt/llvm/` 之后 irony 安装成功。 


## 问题2  标准库头文件无法找到
给我自己的项目加了一个 `CMakeLists.txt` 之后，按照 `irony` 的文档用 cmake 生成
`compile` 命令的文件，`cmake -DCMAKE_EXPORT_COMPILE_COMMANDS=ON .`。

重新打开文件发现还有一个问题, `C++` 标准库对应的头文件无法找到了。

![无法找到
vector的头文件](./img/stdheader-missing.png "无法找到vector的头文件")

```
/L/D/C/S/M/u/include> clang++ -v
Apple clang version 11.0.0 (clang-1100.0.33.16)
Target: x86_64-apple-darwin19.2.0
Thread model: posix
InstalledDir: /Library/Developer/CommandLineTools/usr/bin
```
发现头文件在 `/Library/Developer/CommandLineTools/usr/include/c++/v1` 中，但是
irony `compile_commands.json` 中读取，在这个内容里加入 `-I` 选项之后就搞定了，错
误信息消失，补全也可以工作了。

## 小问题 C++ 标准
虽然现在已经到 2020 了，默认的 C++ 标准也已经来到了 c++17 , 但是一些常用的库还是
c++11 的标准，会在一些语法上有一些问题，这个设置在 `doom` 的 `cc` 模块中有一个设
置默认的 `c++` 选项的问题。 在

```
(defvar +cc-default-compiler-options
  `((c-mode . nil)
    (c++-mode
     . ,(list "-std=c++1z" ; use C++17 draft by default
              (when IS-MAC
                ;; NOTE beware: you'll get abi-inconsistencies when passing
                ;; std-objects to libraries linked with libstdc++ (e.g. if you
                ;; use boost which wasn't compiled with libc++)
                "-stdlib=libc++")))
    (objc-mode . nil))
  "A list of default compiler options for the C family. These are ignored if a
compilation database is present in the project.

This is ignored by ccls.")
```
中可以修改默认的 `c++17` 的选项。

# 总结
c++ 还是太底层，不过这么一段时间之后再回来看 c++ 也还是比之前要轻松不少。
