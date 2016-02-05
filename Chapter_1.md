#第一章 教程简介与词法分析器

1. 教程简介
2. Kaleidoscope语言概述
3. 词法分析器

##1. 教程简介

欢迎来到“使用LLVM开发新语言”教程。本教程通过实现一种简单的语言来展示这一过程是多么的简单有趣。它将带你进入LLVM的大门，并且帮助你搭建一个以后可以扩展到其他语言上的框架。本教程中的代码也可以成为你学习LLVM其他功能的试验田。

本教程会逐渐向我们的新语言中添加功能，借此描述如何一步步地进行开发工作。这样的安排能让我们在从语言设计到LLVM具体使用的广大知识范围内涉猎。我们会一直给出并注解相关的代码，以免大量的细节直接让你不知所措。

需要提前指出，本教程介绍的是LLVM和相关的编译器技术，而不是健全的现代软件工业准则。这意味着在编写代码时，我们会使用一些便捷的方式来简化代码，例如：大量使用全局变量、不使用visitor等优秀的设计模式等等。总之，一切简便起见。如果你专注于良好的代码风格或者想把这份代码作为你以后的项目的基础，你可以试着修复这些缺陷——这并不难。

我尽力安排各个章节，让对某些章节的内容有所了解，或者不感兴趣的人能够轻松地直接跳过那一章。本教程的结构如下：

- 第一章： **介绍Kaleidoscope语言，并且实现它的词法分析器** 
    
    阐明我们的目的以及新语言应该实现的基本功能。为了让教程尽量简单明了、易于上手，我们选择了用C++实现一切，而没有使用任何词法分析器和语法分析器生成工具。LLVM也可以配合那些工具一起良好工作，你可以自己尝试。
    
- 第二章： **实现语法分析器和AST（抽象语法树）**
    
    在编写好词法分析器后，我们可以开始探讨语法分析技术和基本的AST构造。本教程介绍了递归向下语法分析以及运算符优先语法分析。前两章并没有使用到LLVM的功能，我们的代码甚至都不需要链接LLVM库。：）
    
- 第三章： **生成LLVM IR（LLVM中间代码）**
    
    编写好AST后，我们可以真正看看生成LLVM IR是多么简单了。
    
- 第四章： **添加JIT（即时编译）与优化器支持**
    
    因为很多人都感兴趣如何使用LLVM实现即时编译功能。我们就来展示一下如何通过添加三行代码来添加JIT支持。LLVM在其他很多方面也非常实用，但是为了炫耀一下LLVM的威力，这是最简单而“惊艳”的方式。：）
    
- 第五章： **拓展语言： 控制流**
    
    现在我们的语言已经可以运行了。我们来展示一下怎么拓展控制流操作（if/then/else 以及 “for”循环）。这让我们有机会讨论一下简单的SSA（静态单赋值形式）构造和控制流。
    
- 第六章： **拓展语言： 自定义运算符**
    
    这一章看起来有点蠢，但是却很有意思：允许用户任意自定义一元与二元的运算符（甚至包括自定义运算符优先级！）。这让我们得以将这门“语言”重要的一部分实现为库函数。
    
- 第七章： **拓展语言： 可变变量**
    
    这一章讨论了加入自定义局部变量和赋值运算符的问题。有趣的是，在LLVM中构造SSA形式简单至极：事实上，LLVM根本不需要你的前端构建SSA形式！
    
- 第八章： **拓展语言： 调试信息**
    
    既然已经完成了一个支持控制流，函数和可变变量的五脏俱全的小型编译器，我们应该考虑一下怎么在独立的可执行文件中加入调试信息。调试信息能够让调试器在Kaleidoscope函数中设置断点，打印参数变量，甚至直接调用函数！
    
- 第九章： **总结、其他实用的LLVM小技巧**
    
    这一章讨论了几种可能的拓展这种语言的方式，也包含了一些对有关专题的建议，例如：加入垃圾回收、异常处理、调试、以及对“spaghetti stacks”的支持，以及其他一些技巧与提示。
    
在教程结束时，我们总共写了不到一千行代码（除去注释与空行），就完成了为一门真正的语言设计的一个真正的编译器。它的词法分析器、语法分析器和AST是完全手写的，并且通过JIT编译器实现了代码生成。也许其他的系统会有有趣的“Hello world”一类的教程，但是我觉得本教程的宽度确确实实展示了LLVM的威力——以及如果你对程序设计语言和编译器的设计有兴趣的话，为什么LLVM是不可少的一课。

一条关于本教程的注意事项：

我们希望你自己拓展这门语言。尽情地使用这份代码，加入你自己的功能。编译器不一定是令人望而生畏的东西，你大可享受和语言一起玩耍的乐趣。

##2. Kaleidoscope语言概述

本教程基于一种名为Kaleidoscope（意为“美轮美奂的”）语言的玩具级语言。Kaleidoscope是一种基于过程的语言，允许你定义函数，使用条件判断、数学运算等等。在教程中，我们还会拓展Kaleidoscope，添加对“`if/then/else`”结构、for循环、自定义运算符，JIT编译的支持。

This tutorial will be illustrated with a toy language that we’ll call “Kaleidoscope” (derived from “meaning beautiful, form, and view”). Kaleidoscope is a procedural language that allows you to define functions, use conditionals, math, etc. Over the course of the tutorial, we’ll extend Kaleidoscope to support the if/then/else construct, a for loop, user defined operators, JIT compilation with a simple command line interface, etc.

Because we want to keep things simple, the only datatype in Kaleidoscope is a 64-bit floating point type (aka ‘double’ in C parlance). As such, all values are implicitly double precision and the language doesn’t require type declarations. This gives the language a very nice and simple syntax. For example, the following simple example computes Fibonacci numbers:

```
# Compute the x'th fibonacci number.
def fib(x)
  if x < 3 then
    1
  else
    fib(x-1)+fib(x-2)

# This expression will compute the 40th number.
fib(40)
```

我们也允许Kaleidoscope语言调用标准库函数（LLVM JIT让这个功能变得十分易于实现）。这意味着你可以使用“`extern`”关键字来在函数被使用之前声明它（这在处理函数相互调用时也是实用的）。例如：

```
extern sin(arg);
extern cos(arg);
extern atan2(arg1 arg2);

atan2(sin(.4), cos(42))
```

A more interesting example is included in Chapter 6 where we write a little Kaleidoscope application that displays a Mandelbrot Set at various levels of magnification.

Lets dive into the implementation of this language!