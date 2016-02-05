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
    
Chapter #9: Conclusion and other useful LLVM tidbits - This chapter wraps up the series by talking about potential ways to extend the language, but also includes a bunch of pointers to info about “special topics” like adding garbage collection support, exceptions, debugging, support for “spaghetti stacks”, and a bunch of other tips and tricks.

By the end of the tutorial, we’ll have written a bit less than 1000 lines of non-comment, non-blank, lines of code. With this small amount of code, we’ll have built up a very reasonable compiler for a non-trivial language including a hand-written lexer, parser, AST, as well as code generation support with a JIT compiler. While other systems may have interesting “hello world” tutorials, I think the breadth of this tutorial is a great testament to the strengths of LLVM and why you should consider it if you’re interested in language or compiler design.

A note about this tutorial: we expect you to extend the language and play with it on your own. Take the code and go crazy hacking away at it, compilers don’t need to be scary creatures - it can be a lot of fun to play with languages!