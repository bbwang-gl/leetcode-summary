- [多线程 coredump](#多线程-coredump)
- [内存泄漏](#内存泄漏)
  - [内存泄漏诊断与检测工具](#内存泄漏诊断与检测工具)
    - [动态工具：valgrind](#动态工具valgrind)
    - [动态工具：AddressSanitizer (ASan) / LeakSanitizer (LSan)](#动态工具addresssanitizer-asan--leaksanitizer-lsan)
    - [静态分析工具](#静态分析工具)

## 多线程 coredump 

```bash
(gdb) bt # 查看当前线程的调用栈
(gdb) info threads # 查看所有线程和位置
(gdb) thread apply all bt full # 打印所有线程的调用栈以及每个线程的局部变量
```

## 内存泄漏
预防为主: 最好的“工具”是良好的编程习惯——优先使用智能指针和RAII，从根本上杜绝大部分内存泄漏。
### 内存泄漏诊断与检测工具
动态检测（运行时）和静态分析（编译前或编译时） 
#### 动态工具：valgrind
> valgrind 将内存泄漏分为 4 类。
> 
> 明确泄漏（definitely lost）：内存还没释放，但已经没有指针指向内存，内存已经不可访问
> 间接泄漏（indirectly lost）：泄漏的内存指针保存在明确泄漏的内存中，随着明确泄漏的内存不可访问，导致间接泄漏的内存也不可访问
> 可能泄漏（possibly lost）：指针并不指向内存头地址，而是指向内存内部的位置
> 仍可访达（still reachable）：指针一直存在且指向内存头部，直至程序退出时内存还没释放。

- 优点: 检测全面、准确、可靠。是Linux环境下最权威的工具。  
- 缺点: 运行极其缓慢，内存开销大。不支持Windows原生环境（可通过Wine或WSL使用）。  
- 性能问题：程序运行速度会变慢（通常是正常速度的20-50倍），并且占用更多内存。
- 使用方法：
  ```bash
  # 编译时， 增加选项 -g
  gcc -g -o main_c main.c
  # 运行时
  valgrind --tool=memcheck --leak-check=full  ./main_c
  ```  

#### 动态工具：AddressSanitizer (ASan) / LeakSanitizer (LSan)
AddressSanitizer (ASan) 是由Google开发，现已集成到Clang和GCC主流编译器中的快速内存错误检测器。LeakSanitizer (LSan) 是其子集，专门用于检测内存泄漏。  
优点:
- 速度快: 相比Valgrind，性能开销小得多（通常慢2-3倍），可以集成到日常开发和CI/CD流程中。
- 跨平台: 支持Linux, Windows, macOS。
- 精准定位: 能精确定位到错误发生的代码行。
- 易于集成: 只需添加编译标志，无需额外工具链。  
缺点: 主要用于开发和测试阶段，不适合部署到生产环境（因为有性能开销和依赖， 2～3倍）。有时对某些复杂的系统调用或内联汇编支持不够好。 
- 使用方法 

    ```bash
    # 使用 Clang 或 GCC 编译，链接 ASan 运行时库
    clang++ -fsanitize=address -g -o my_program my_program.cpp
    # 或者
    g++ -fsanitize=address -g -o my_program my_program.cpp

    # 直接运行程序
    ./my_program
    ```

#### 静态分析工具
- 工作原理: 解析源代码的抽象语法树（AST），应用一系列规则和模式匹配来推断代码行为。
- 主要工具:
  - Clang Static Analyzer: 集成在Clang编译器中。可以通过scan-build脚本或Xcode使用。能发现空指针解引用、资源泄漏路径、逻辑错误等。  
  -  Cppcheck: 开源的独立静态分析工具。可以检测内存泄漏（如new后没有delete）、数组越界、未初始化变量等。支持大量C/C++标准和自定义配置。

        ```bash
        # 检查单个文件
        cppcheck myfile.cpp
        # 检查整个项目目录（推荐）
        cppcheck /path/to/your/project/
        ```

  - PVS-Studio: 商业工具，功能强大，能发现许多微妙的错误。 
  - 编译器警告: 启用-Wall, -Wextra, -Weffc++等高级警告标志，编译器本身就能发现很多潜在问题。
  
- 优点: 不需要运行程序，可以在编码阶段就发现问题。速度快。  
- 缺点: 可能产生误报 (False Positives) 和漏报 (False Negatives)。无法理解程序的完整运行时行为，因此不如动态分析工具可靠。对于内存泄漏，它只能基于代码路径分析猜测哪里可能忘记释放，而不能像动态工具那样确切知道哪些内存块真的泄漏了。