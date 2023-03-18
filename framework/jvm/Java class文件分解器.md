﻿﻿javap是 Java class文件分解器，可以反编译，也可以查看java编译器生成的字节码。用于分解class文件。

分解class文件

```
javap [ options ] class. . .
```

javap命令分解一个class文件，它根据options来决定到底输出什么。如果没有使用options,那么javap将会输出包，类里的protected和public域以及类里的所有方法。javap将会把它们输出在标准输出上。来看这个例子，先编译下面这个类。

javac Test.java

eg:

```
javap -v -p Test.class > F://aaa.txt
```

选项

```
-help 帮助
-l 输出行和变量的表
-public 只输出public方法和域
-protected 只输出public和protected类和成员
-package 只输出包，public和protected类和成员，这是默认的
-p -private 输出所有类和成员
-s 输出内部类型签名
-c 输出分解后的代码，例如，类中每一个方法内，包含java字节码的指令，
-verbose 输出栈大小，方法参数的个数
-constants 输出静态final常量
-v 输出详细信息
```
