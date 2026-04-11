## 2.3.1使用nano编辑器创建C程序
![[Pasted image 20251104160614.png]]
保存文件：Ctrl+O(输出文件)
退出nano:Ctrl +X
剪切一行：Ctrl+K
粘贴一行：Ctrl+U
查找文本：Ctrl+W
替换文本：Ctrl+\
取消操作：Ctrl+C
## 2.3.2编译链接
gcc -o OutputFile SourceFile -g
-o：用于指定编译链接生成的可执行文件名字。
-g：用于指示生成一个可调试的可执行文件。
## 2.3.3调试可执行程序
 gdb hello --silent
 查看源代码 list
 在第五行设置一个断点 break 5
 查看当前设置了哪些断点 info break
 开始调试 run
 继续执行gdb“next”(运行一行c语言代码)
 继续执行gdb"step"(运行一行c语言代码)
 gdb的"next"命令和"step"命令都是执行一行C语言代码，两者的不同之处在于："next"命令可
以解释为step over，"step"命令可以解释为step into，即当要执行的行是一个函数时，"next"命令会
将函数作为一行代码，不会进入函数内部执行，而"step"命令则会进入函数内部执行。
继续执行到下一个断点(没有断点就执行结束) continue
中断调试 kill
退出调试 quit
## 2.3.4交付产品程序
执行不带-g的gcc命令，完成编译
gcc -o hello hello.c
ls -l
./hello
## 2.4Make和Makefile
make是Linux上的一个自动化构建工具，用于管理和自动化软件项目的编译过程。它根据一
个名为Makefile的文件中定义的规则和依赖关系，决定哪些部分需要重新编译，从而提高构建效
率。Makefile包含了目标文件、源文件及其依赖关系的定义，并指定了如何生成目标文件的具体
命令。通过使用make工具，开发者可以方便地编译大型项目，减少重复劳动，确保项目的各个部
分都按正确的顺序和依赖关系进行构建。
example：
cd ~/example
nano Makefile
```makefile
hello: hello.0
	gcc -o hello hello.o
hello.o: hello.c
	gcc -c -o hello.o hello.c
clean:
	rm -f *o hello
```
(最后按"Ctrl+x"键、字母"y"键和回车键保存退出。)
使用"make"命令编译"hello.c"程序。
```cmd
[root@test example]# make clean
rm -f *o hello
[root@test example]# make
gcc -c -o hello.o hello.c
gcc -o hello hello.o
[root@test example]# ./hello
Hello, World!
[root@test example]#
```
"make clean"命令将清除之前编译产生的文件；执行"make"命令将执行编译链接操作生成可
执行程序"hello"。
## 2.5gdb调试器
1.gdb子命令print,打印变量a的值 print a
2.gdb子命令print，打印变量a的地址 print &a
3.执行gdb命令的子命令disassemble，反汇编main函数（反汇编是将内存中的二进制机器码转换为汇编指令助记符)。(按小写字母"c"，显示反汇编main函数剩下的输出。)
4.执行gdb命令的子命令，在第19行处设置一个断点，并查看当前设置了哪些调试断点。 break 19
5.执行gdb命令的子命令del，删除编号num=1的调试断点。 del 1
6.执行gdb命令的子命令cont（continue子命令的缩写）。
7.执行gdb命令的子命令quit，退出gdb调试器。
8.执行gdb命令的子命令backtrace，查看栈帧的情况。
9.执行gdb命令的子命令info args，查看当前栈帧的调用参数。
10.执行gdb命令的子命令info locals，查看当前栈帧中的局部变量。
11.执行gdb命令的子命令up 1，向上移动1级栈帧。
12.执行gdb命令的子命令info frame，查看当前栈帧（0号栈帧）的详细情况。
13.执行gdb命令的子命令info frame1，查看指定的1号栈帧的详细情况。
(gdb调试多进程、多线程、汇编程序)

## 2.6 使用core文件定位程序错误

## 3.2Vim编辑器
vi mynewfile
启动vi编辑器后，vi编辑器处于编辑模式的替换（REPLACE）状态，建议此时按Esc键（位于键盘的最左上角），确保vi编辑器肯定处于命令模式。
在vi编辑器的命令模式，可以输入vi编辑子命令。vi编辑子命令i表示vi编辑器进入编辑模式，
在当前光标的前面为文件插入新的字符。按下小写字母i，进入vi编辑模式的INSERT状态
在vi编辑器的命令模式下，按下冒号":"，或者斜杠""，或者问号"？"，将使vi编辑器进入最后
一行模式。在最后一行模式，可以对vi进行设置操作、查找文件内容、或者决定是否保存文件内容后退出vi编辑器。(:q!和↩︎)
（:wq和回车）
综上，***三种模式之间的切换*** 可总结如下:
(1)无论当前vi处于何种模式，只要按下Esc键，vi就会回到命令模式。
(2）命令模式下，输入vi编辑子命令（如小写字母i，a，O，或者其他)，将使vi进入编辑模式。
(3）编辑模式下，可以输入文本信息。
(4）文本信息输入完成后，按Esc键，再次返回命令模式。
(5）命令模式下，按冒号（:)，或者斜杠（/)，或者问号（?)，将使vi切换到最后一行模式。
(6）在最后一行模式中完成vi子命令的输入，按"回车键"，将从最后一行模式切换回命令模式。
### 3.2.2控制光标移动

## Bash Shell
[[Base Shell]]


