### gcc “-I”（大写i），“-L”（大写l），“-l”（小写l）的区别
我们用gcc编译程序时，可能会用到“-I”（大写i），“-L”（大写l），“-l”（小写l）等参数，下面做个记录：

例：

gcc -o hello hello.c -I /home/hello/include -L /home/hello/lib -lworld

上面这句表示在编译hello.c时：

-I /home/hello/include表示将/home/hello/include目录作为第一个寻找头文件的目录，寻找的顺序是：/home/hello/include-->/usr/include-->/usr/local/includ

-L /home/hello/lib表示将/home/hello/lib目录作为第一个寻找库文件的目录，寻找的顺序是：/home/hello/lib-->/lib-->/usr/lib-->/usr/local/lib

 -lworld表示在上面的lib的路径中寻找libworld.so动态库文件（如果gcc编译选项中加入了“-static”表示寻找libworld.a静态库文件）

 gcc -l参数和-L参数

-l参数就是用来指定程序要链接的库，-l参数紧接着就是库名，那么库名跟真正的库文件名有什么关系呢？就拿数学库来说，他的库名是m，他的库文件名是libm.so，很容易看出，把库文件名的头lib和尾.so去掉就是库名了。

好 了现在我们知道怎么得到库名，当我们自已要用到一个第三方提供的库名字libtest.so，那么我们只要把libtest.so拷贝到/usr/lib 里，编译时加上-ltest参数，我们就能用上libtest.so库了（当然要用libtest.so库里的函数，我们还需要与libtest.so配 套的头文件）

放在/lib和/usr/lib和/usr/local/lib里的库直接用-l参数就能链接了，但如果库文件没放在这三个目 录里，而是放在其他目录里，这时我们只用-l参数的话，链接还是会出错，出错信息大概是：“/usr/bin/ld: cannot find -lxxx”，也就是链接程序ld在那3个目录里找不到libxxx.so，这时另外一个参数-L就派上用场了，比如常用的X11的库，它在/usr /X11R6/lib目录下，我们编译时就要用-L/usr/X11R6/lib -lX11参数，-L参数跟着的是库文件所在的目录名。再比如我们把libtest.so放在/aaa/bbb/ccc目录下，那链接参数就是-L /aaa/bbb/ccc -ltest

另外，大部分libxxxx.so只是一个链接，以RH9为例，比如libm.so它链接到/lib/libm.so.x，/lib/libm.so.6又链接到/lib/libm-2.3.2.so，

如果没有这样的链接，还是会出错，因为ld只会找libxxxx.so，所以如果你要用到xxxx库，而只有libxxxx.so.x或者libxxxx-x.x.x.so，做一个链接就可以了ln -s libxxxx-x.x.x.so libxxxx.so

手工来写链接参数总是很麻烦的，还好很多库开发包提供了生成链接参数的程序，名字一般叫xxxx-config，一般放在/usr/bin目录下，比如

gtk1.2的链接参数生成程序是gtk-config，执行gtk-config --libs就能得到以下输出"-L/usr/lib -L/usr/X11R6/lib -lgtk -lgdk -rdynamic -lgmodule -lglib -ldl -lXi -lXext -lX11 -lm"，这就是编译一个gtk1.2程序所需的gtk链接参数，xxx-config除了--libs参数外还有一个参数是--cflags用来生成头 文件包含目录的，也就是-I参数，在下面我们将会讲到。你可以试试执行gtk-config --libs --cflags，看看输出结果

现 在的问题就是怎样用这些输出结果了，最笨的方法就是复制粘贴或者照抄，聪明的办法是在编译命令行里加入这个`xxxx-config --libs --cflags`，比如编译一个gtk程序：gcc gtktest.c `gtk-config --libs --cflags`这样就差不多了。注意`不是单引号，而是1键左边那个键。

5、-include和-I参数

-include 用来包含头文件，但一般情况下包含头文件都在源码里用#include xxxxxx实现，-include参数很少用。-I参数是用来指定头文件目录，/usr/include目录一般是不用指定的，gcc知道去那里找，但是如果头文件不在/usr/include里我们就要用-I参数指定了，比如头文件放在/myinclude目录里，那编译命令行就要加上-I /myinclude参数了，如果不加你会得到一个"xxxx.h: No such file or directory"的错误。-I参数可以用相对路径，比如头文件在当前目录，可以用-I.来指定