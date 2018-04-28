# java-
异常处理
一, 什么是java里的异常
 
由于java是c\c++ 发展而来的,  首先我们先看看c语言里的错误.
1.1 c语言里的错误
        我们实现1个程序的过程包括,  代码编写, 编译代码成为程序,  执行程序.
 
        其中大部分常见的语法错误都会被编译代码这样部过滤掉.   但是即使通过了编译. 执行程序这一步可能还是会有错误.
        原因很多, 例如常见的除数为0,    内存溢出(数组的index超出界限), 或者内存被其他程序修改等.
        最简单的例子:
[java] view plain copy
 

    #include <stdio.h>  
      
    int f(int a, int b){  
        return a/b;  
    }     
      
    int main(){  
        int i = f(8,0);  
            printf("i is %d\n",i);  
        return 0;     
    }  




       上面的例子编译时是无错的,  但是一旦执行就会提示吐核错误了. 
 
       c语言里对这种执行时出现的错误是无能为力的,  一旦出错就会整个程序崩溃, 就不会在继续执行下面的代码.
 
       而且很多时候出错信息很少, 让你无法判断出错的原因和地方, 只能一步步小心debug...
 
       所以很多用c写的程序有时会出现非法关闭的现象.
 
       解决方法只能是在代码里对可能出错的地方添加if 判断.
 
       例如f()函数里可以对b进行判断, 如果是0就不执行.
 
 
1.2 java里运行时出现的错误
       java里编译器对代码的规范性比c严格得多. 但是即使如此,  通过编译的java程序有时也很难避免执行时出错.
 
       例如, 将上面的c程序改编成java程序:
 
[java] view plain copy
 

    package Exception_kng;  
      
    class Exp1{  
        public int f(int a, int b){  
            return a/b;  
        }  
    }  
      
    public class Expt_1{  
        public static void g(){  
            Exp1 e = new Exp1();  
            int i = e.f(8,0);  
            System.out.printf("i is %d\n", i);  
        }  
    }  

 
      运行时一样会出错, 下面是出错信息:
 
[java] view plain copy
 

    [java] Caused by: java.lang.ArithmeticException: / by zero  
    [java]  at Exception_kng.Exp1.f(Expt_1.java:5)  
    [java]  at Exception_kng.Expt_1.g(Expt_1.java:12)  
    [java]  at Enter_1.main(Enter_1.java:31)  

 
          但是可以见到, java告诉你出错的类型: 运算错误(ArithmeticExcetion), 出错信息和出错的类与文件行数输出, 方便你调试.  jvm虚拟机是会对错误作出一定的处理的.
  
          所以可以简单地将java里的异常理解成java运行时出现的错误,  异常机制就是对这种错误进行处理的机制.
 
 
1.3 java异常的定义
        实际上, 当java程序执行时出现错误时, jvm会把执行时出错的信息(例如出错原因, 类型, 位置) 收集,然后打包成为1个对象(object),  程序员可以对这种对象进行处理. 这种对象就是所谓的异常.
 
        可能出现的异常的代码并不是肯定会出现异常, 取决于执行环境和数据.!
 
二, java里的异常的分类.
 
见下图:
 
                       Throwable
                      /                \
              Error             Exception
                 /                   /               \
         xxxxxx             xxxxxx          RuntimeException
                                                          /                   \
                                                     xxxxxx             ArithmeticException
 
 
 
上图的所有对象都是类.     
 
Throwable 代表是可抛出的.
Error            代表的是严重错误,  这种错误程序员无法进行处理, 例如操作系统崩溃, jvm出错, 动态链接库失败等.  Error并不是异常, 不是本文的重点.
 
Exception    代表的就是异常了.  它下面很多派生类,   其中它的派生类也分两种, 一种是RuntimeException(运行时异常), 其他的都是非运行时异常
 
RuntimeException    包括除数为0, 数组下标超界等. 运行时异常的派生类有很多, 其产生频率较高.  它的派生类可以由程序处理或者抛给(throw) 给jvm处理. 例如上面的例子就是抛给了jvm处理, jvm把程序中断执行, 并把错误信息输出到终端上.
 
非RuntimeExcption   这种异常属于Excepion的派生类(上面红色的xxx), 但是不是RuntimeException的派生类,  这种异常必须由程序员手动处理,否则不通过编译.
 
ArithmeticExcpetion   算术异常, 它是RuntimeException的派生类, 所以程序员不手动处理也通过编译, 只不过出错时会被jvm处理.
 
 
 
 
三, java里对异常的处理
java里对异常的处理有三种.
3.1 程序猿对有可能出现的异常使用try catch处理.
例如我们将上面的例子改动一下:
 
[java] view plain copy
 

    package Exception_kng;  
      
    class Exp2{  
        public int f(int a, int b){  
            int i = 0;  
            try{  
                i = a/b;  
            }  
            catch(Exception e){  
                System.out.printf("Exception occurs!!\n");  
                System.out.println(e.getMessage());  //print the root cause  
                System.out.printf("===========================\n");  
                e.printStackTrace(); //print the info of function stuck.  
            }  
      
            return i;  
        }  
    }  
      
    public class Expt_2{  
        public static void g(){  
            Exp2 ex = new Exp2();  
            int i = ex.f(8,0); //call f()  
            System.out.printf("i is %d\n", i);  //successfully executed  
        }  
    }  



 
在f()函数中对可能出现的异常的代码进行try catch处理后,  程序会执行catch里的代码. 而且不会中断整个程序, 继续执行try catch后面的代码.
 
程序执行输出:
 
[java] view plain copy
 

    [java] Exception occurs!!  
    [java] / by zero  
    [java] ===========================  
    [java] java.lang.ArithmeticException: / by zero  
    [java]  at Exception_kng.Exp2.f(Expt_2.java:7)  
    [java]  at Exception_kng.Expt_2.g(Expt_2.java:23)  
    [java]  at Enter_1.main(Enter_1.java:31)  
    [java] i is 0  



注意最终也执行了g()函数中的最后一条语句, 输出了i的值.
 
也就是说try catch处理后并不会终止程序, 令程序即使出现了错误,  也可以对错误进行一定的处理后继续执行. 这就是java异常机制比c语言安全的地方.
 
下面会详细讲解 try catch.
 
注:
 
getMessage() 方法:   Exception类的方法之一,  返回异常的原因,   上面的 / by zero 就是这个方法输出的.
 
printStackTrace():      Exception类的方法之一,  在屏幕输出函数栈信息,  也就是异常出现的地方.
 
 
3.2 函数里并不处理异常, 使用throw or throws 关键字 把可能出现的异常抛给调用该函数的上级函数处理.
 
例如我在f()函数中不想处理可能出现的异常,  想把它抛出上级函数处理:
 
下面是个例子:
 
[java] view plain copy
 

    package Exception_kng;  
      
    class Exp3{  
        public int f(int a, int b){  
            if (0 == b){  
                throw new ArithmeticException("Shit !!! / by zero!");  
                  
            }   
      
            return a/b;  
        }  
    }  
      
    public class Expt_3{  
        public static void g() throws ArithmeticException{  
            Exp3 ex = new Exp3();  
            int i = 22;  
            i = ex.f(8,0); //throw excetpion   
            System.out.printf("i is %d\n", i);  //failed executed   
            System.out.printf("g() is done!!\n");  //failed executed  
        }  
      
        public static void h(){  
            try{  
                g();      
            }catch(ArithmeticException e){  
                System.out.printf("Exception occurs!!\n");  
                System.out.println(e.getMessage());  //print the root cause  
                System.out.printf("===========================\n");  
                e.printStackTrace(); //print the info of function stuck.  
            }  
      
            System.out.printf("h() is done!!\n");  //successfully executed  
        }  
    }  

可以见到f() 加了个条件判断, 如果参数b = 0, 使用throw 直接手动抛出1个异常. 让调用它的函数处理.
 
g()调用f()函数, 预见到f()可能有异常, 但是也不想处理,  使用throws 关键字告诉调用它的函数本函数有可能抛出这种异常. // 注, 这里的throws对程序并没有实质的影响.
 
h()调用g(),  简单g()定义的throws, 用try catch在本函数进行处理.
 
 
输出:
[java] view plain copy
 

    [java] Exception occurs!!  
    [java] Shit !!! / by zero!  
    [java] ===========================  
    [java] java.lang.ArithmeticException: Shit !!! / by zero!  
    [java]  at Exception_kng.Exp3.f(Expt_3.java:6)  
    [java]  at Exception_kng.Expt_3.g(Expt_3.java:18)  
    [java]  at Exception_kng.Expt_3.h(Expt_3.java:25)  
    [java]  at Enter_1.main(Enter_1.java:31)  
    [java] h() is done!!  


注意这个程序没有执行g() 最后的代码.
 
 
throw 和 throws 后面也会详细讲解.
 
 
3.3 交给jvm虚拟机处理
 
假如上面的例子h() 也不处理怎么办?  就如1.2 的例子, 会抛给jvm处理.
 
但是这种情况只适用于RuntimeExecption及其派生类.
 
jvm怎么处理呢,  就是中断整个程序, 并把异常信息输出到屏幕上.

实际上, 当java程序的1个函数抛出异常时,  
首先会检查当前函数有没有try catch处理, 如果无检查上一级函数有无try..catch处理....
这样在函数栈里一级一级向上检查, 如果直至main函数都无try..catch, 则抛给jvm..
 
项目中强烈建议尽量手动处理, 不要把异常交给jvm.
 
 
四,Try catch finally 的处理机制.
这里开始详解try catch finally了.
 
语法是这样的.
 
try{
       可能出异常的若干行代码;
}
catch(ExceptionName1 e){
       产生ExceptionName 1的处理代码;
}
catch(ExceptionName2 e){
       产生ExceptionName 2的处理代码;
}
...
finally{
      无论如何, 最终肯定会执行的代码
}


 
4.1 try catch finally的执行路线.
下面用个例子来说明:

[java] view plain copy
 

    try{  
        f();  
        ff();  
    }  
    catch(ArithmeticException e){  
        g();  
    }  
    catch(IOException e){  
        gg();  
    }  
    catch(AuthorizedException e){  
        ggg();  
    }  
    finally{  
        h();  
    }  
      
    k();  


4.1.1 当try里面的f()抛出了IOException
当f()抛出了异常, 那么ff()就不会执行了.  程序会尝试捕捉异常.

首先捕捉ArithmeticException,  捕捉失败.

接下来捕捉IOException, 捕捉成功, 执行gg();

一旦捕捉到一个异常, 不会再尝试捕捉其他异常, 直接执行finally里的h();

执行后面的函数k().

也就是说路线是:

f()  ->  gg()  -> h() -> k()


有2点要注意的.

1. f()函数极有可能未完整执行, 因为它抛出了异常, 抛出异常的语句执行失败, 之后的语句放弃执行.

2. try{} 里面, f()之后的语句, 例如ff()放弃执行.


4.1.2 没有任何异常抛出

这种情况很简单, 就是try{}里面的代码被完整执行, 因为没有抛出任何异常, 就不会尝试执行catch里的部分, 直接到finally部分了.

路线是:
f()  ->  ff()  -> h() -> k()

 

 
4.2 如何确定要捕捉的异常名字.

 

也许有人会问, 我们怎么知道到底会抛出什么异常?

下面有3个解决方案.

1.看代码凭经验, 例如看到1段除法的代码, 则有可能抛出算术异常.

2.在catch的括号里写上Exception e,  毕竟Exception 是所有其他异常的超类, 这里涉及多态的知识,  至于什么是多态可以看看本人的另一篇文章.

3. 观察被调用函数的函数定义, 如果有throws后缀, 则可以尝试捕捉throws 后缀抛出的异常

 
4.3 为什么需要finally

 

包括我在内很多人会觉得finally语句简直多勾余, 既然是否捕捉到异常都会执行, 上面那个例子里的h()为什么不跟下面的k() 写在一起呢.

上面的例子的确看不出区别.

 

但下面两种情况下就体现了finally独特的重要性.

 
4.3.1 抛出了1个异常, 但是没有被任何catch子句捕捉成功.

 

例如try里面抛出了1个A异常,  但是只有后面只有捕捉B异常, 和C异常的子句. 

这种情况下, 程序直接执行finally{}里的子句,  然后中断当前函数, 把异常抛给上一级函数, 所以当前函数finally后面的语句不会被执行.

例子:

 
[java] view plain copy
 

    package Exception_kng;  
      
    import java.net.*;  
    import java.io.*;  
      
    class Exp4{  
        public int f(int a, int b) throws IOException, BindException{  
            return a/b;  
        }  
    }  
      
    public class Expt_4{  
        public static void g(){  
            Exp4 ex = new Exp4();  
            int i = 22;  
            try{  
                System.out.printf("g() : try!!\n");  //failed  
                i = ex.f(8,0); //call f()    
            }  
            catch(BindException e){  
                System.out.printf("g() : BindException!!\n");  //failed  
            }  
            catch(IOException e){  
                System.out.printf("g() : IOException!!\n");  //failed  
            }  
            finally{  
                System.out.printf("g() : finaly!!\n");  //successfully executed  
            }  
            System.out.printf("g() is done!!\n");  //failed  
        }  
      
        public static void h(){  
            try{  
                g();      
            }catch(ArithmeticException e){  
                System.out.printf("Exception occurs!!\n");  
                System.out.println(e.getMessage());  //print the root cause  
                System.out.printf("===========================\n");  
                e.printStackTrace(); //print the info of function stuck.  
            }  
      
            System.out.printf("h() is done!!\n");  //successfully executed  
        }  
    }  


我所说的情况, 就在上面例子里的g()函数,  g()函数里尝试捕捉两个异常, 但是抛出了第3个异常(ArithmeticException 算术异常).

 

所以这个异常会中断g()的执行, 因为没有被捕捉到, 然后抛给调用g()的 h()函数处理,  而在h()捕捉到了, 所以h()函数是能完整执行的.

也就是说g()里的

 
[java] view plain copy
 

    System.out.printf("g() is done!!\n");  //failed  

 

执行失败

而h()里的

 
[java] view plain copy
 

    System.out.printf("h() is done!!\n");  //successfully executed  


执行成功

 

但是无论如何, g()里的finally{}部分还是被执行了

执行结果如下:

 
[java] view plain copy
 

    [java] g() : try!!  
    [java] g() : finaly!!  
    [java] Exception occurs!!  
    [java] / by zero  
    [java] ===========================  
    [java] java.lang.ArithmeticException: / by zero  
    [java]  at Exception_kng.Exp4.f(Expt_4.java:8)  
    [java]  at Exception_kng.Expt_4.g(Expt_4.java:18)  
    [java]  at Exception_kng.Expt_4.h(Expt_4.java:34)  
    [java]  at Enter_1.main(Enter_1.java:31)  
    [java] h() is done!!  

 

 

这种情况是1中编程的低级错误, 在项目中是不允许出现.

避免方法也十分简单,  在catch子句集的最后增加1个catch(Exception e)就ok, 因为Exception是所有异常的超类, 只要有异常抛出, 则肯定会捕捉到.

 

 
4.3.2 在catch子句内有return子句.

 

下面例子:

 
[java] view plain copy
 

    try{  
        f();  
        ff();  
    }  
    catch(ArithException e){  
        g();  
        return j();  
    }  
    catch(IOException e){  
        gg();  
        return j();  
    }  
    catch(AuthorizedException e){  
        ggg();  
        return j();  
    }  
    finally{  
        h();  
    }  
      
    k();  


假如在f()函数抛出了IOExcepion 异常被捕捉到.

 

 

那么执行路线就是

f()  ->  gg()  -> j() -> h() -> 上一级function

也就说, 这种情况下finally里的子句会在return回上一级function前执行. 而后面的k()就被放弃了.

 

 
4.3.3 finally作用小结.

 

可以看出, finally里的语句, 无论如何都会被执行.

至有两种情况除外, 一是断电, 二是exit函数.

 
在项目中, 我们一般在finally编写一些释放资源的动作, 例如初始化公共变量. 关闭connections, 关闭文件等.

 
4.4 try catch finally里一些要注意的问题.

 
4.4.1 无论如何最多只有1个catch被执行

 

这个上面提到过了, 一旦捕捉到1个异常, 就不会尝试捕捉其他异常.

如果try里面的一段代码可能抛出3种异常A B C,  

首先看它先抛出哪个异常, 如果先抛出A,  如果捕捉到A, 那么就执行catch(A)里的代码. 然后finally.. B和C就没有机会再抛出了.

如果捕捉不到A, 就执行finally{}里的语句后中断当前函数, 抛给上一级函数...(应该避免)

 

 
4.4.2 有可能所有catch都没有被执行

 

两种情况, 1就是没有异常抛出, 另一种就是抛出了异常但是没有捕捉不到(应该避免)
4.4.3 先捕捉子类异常, 再捕捉父类异常, 否则编译失败

加入try 里面尝试捕捉两个异常, 1个是A, 1个是B, 但是A是B的父类.

这种情况下, 应该把catch(B)写在catch(A)前面.

原因也很简单, 加入把catch(A)写在前面, 因为多态的存在, 即使抛出了B异常, 也会被catch(A)捕捉, 后面的catch(B)就没有意义了.

也就是说如果捕捉Exception这个异常基类, 应该放在最后的catch里,  项目中也强烈建议这么做, 可以避免上述4.3.1的情况出现.

 

 
4.4.4 catch与catch之间不能有任何代码.

 

这个没什么好说的. 语法规则

 

 
4.4.5 finally里不能访问catch里捕捉的异常对象e

 

每1个异常对象只能由catch它的catch子句里访问.

 

 
4.4.6 try里面的定义变量不能在try外面使用.

 

跟if类似, 不多说了.

 

 
4.4.7 try catch finally可以嵌套使用.

 

这个也不难理解..

五, throw 和throws的机制和用法.

 

下面开始详讲异常另一种处理方法throw 和 throws了.

注意的是, 这两种用法都没有真正的处理异常, 真正处理的异常方法只有try catch, 这两种方法只是交给上一级方法处理.

 

就如一个组织里 , 有1个大佬, 1个党主, 1个小弟.

大佬叫党主干活, 堂主叫小弟干活,  然后小弟碰上麻烦了,   但是小弟不会处理这个麻烦, 只能中断工作抛给党主处理, 然后堂主发现这个麻烦只有大佬能处理, 然后抛给大佬处理..

道理是相通的..

 
5.1 throw 的语法与作用

 

throws的语法很简单.

语法:

throw new XException();

其中xException必须是Exception的派生类.

这里注意throw 出的是1个异常对象, 所以new不能省略

 

作用就是手动令程序抛出1个异常对象.

 

 

 
5.2 throw 1个 RuntimeException及其派生类

我们看回上面3.2 的例子:

 

 
[java] view plain copy
 

    public int f(int a, int b){  
            if (0 == b){  
                throw new ArithmeticException("Shit !!! / by zero!");  
      
            }   
      
            return a/b;  
    }  

 

 

 
5.2.1 throw会中断当前函数, 当前函数执行失败(不完整)

当这个函数的if 判断了b=0时, 就利用throws手动抛出了1个异常.  这个异常会中断这个函数. 也就是说f()执行不完整, 是没有返回值的.

 

 

 

 
5.2.2, 接下来哪个调用这个函数就会在调用这个函数的语句上收到异常.
[java] view plain copy
 

    public void g(){  
        int i;  
        h();  
        i = f(); //recevie excepton  
        k();  
    }  

 

例如上没的g()函数, 在调用f() 会收到1个异常.

这时g()函数有三种选择.

 

1. 不做任何处理

这时, g()收到f()里抛出的异常就会打断g()执行, 也就是说g()里面的k(); 被放弃了, 然后程序会继续把这个函数抛给调用g()函数.

 

然后一级一级寻求处理, 如果都不处理, 则抛给jvm处理.  jvm会中断程序, 输出异常信息. 这个上没提到过了.

 

2. 使用try catch处理

如果catch成功, 则g()函数能完整执行, 而且这个异常不会继续向上抛.

如果catch失败(尽量避免), 则跟情况1相同.

 

 

 

 
5.3 throw 1个 非RuntimeException派生类的异常

将上面的例子改一下:

 

 
[java] view plain copy
 

    public int f(int a, int b){  
            if (0 == b){  
                throw new IOException("Shit !!! / by zero!");  
            }   
      
            return a/b;  
        }  


例如, 我不想抛出ArithmeticException,  我想抛出IOExcetpion.

 

注意 这里, IOException虽然逻辑上是错误的(完全不是IO的问题嘛), 但是在程序中完全可行, 因为程序猿可以根据需要控制程序指定抛出任何1个异常.

 

但是这段代码编译失败, 因为IOException 不是 RuntimeException的派生类.

 

java规定:

 

 
5.3.1 如果一个方法里利用throw手动抛出1个非RuntimeException异常, 必须在函数定义声明里加上throws 后缀

 

 

 

改成这样就正确了:

 
[java] view plain copy
 

    public int f(int a, int b) throws IOException{  
            if (0 == b){  
                throw new IOException("Shit !!! / by zero!");  
            }   
      
            return a/b;  
    }  


注意在方法定义里加上了throws子句.  告诉调用它的函数我可能抛出这个异常.

 

 

 

 
5.3.2 调用该方法的方法则必须处理这个异常

 

例如抄回上面的例子, g()调用f()函数.

 
[java] view plain copy
 

    public void g(){  
        int i;  
        h();  
        i = f(); //recevie excepton  
        k()  
    }  


但是编译失败.

 

 

因为f()利用throws 声明了会抛出1个非runtimeExcetpion.  这时g()必须做出处理.

处理方法有两种:  

 

1. try catch自己处理:

 
[java] view plain copy
 

    public void g(){  
        int i = 0;  
        h();  
        try{  
            i = f(); //recevie excepton  
        }  
        catch(IOException e){  
      
        }  
        k();  
    }  


需要注意的是, catch里面要么写上throws对应的异常(这里是 IOException), 要么写上这个异常的超类, 否则还是编译失败.

 

 

2.g()利用throws 往上一级方法抛

.
[java] view plain copy
 

    public void g() throws IOException{  
        int i = 0;  
        h();  
        i = f(); //recevie excepton  
        k();  
    }  

 

这是调用g()的函数也要考虑上面的这两种处理方法了... 

但是最终上级的方法(main 方法)还是不处理的话, 就编译失败, 上面说过了, 非runtimeException无法抛给jvm处理.

 

虽然这两种处理方法都能通过编译, 但是运行效果是完全不同的.

第一种, g()能完整执行.

第二种, g()被中断, 也就是g()里面的k(); 执行失败.

 

 
5.4 throws 的语法.

 

throws稍微比throw难理解点:

语法是:

public void f() throws Exception1, Exception2...{

 

}

也就是讲, thorws可以加上多个异常, 注意这里抛出的不是对象, 不能加上new.

而且不是告诉别人这个函数有可能抛出这么多个异常. 而是告诉别人, 有可能抛出这些异常的其中一种.

 

 
5.5 throws 的作用.

 

如果为f()函数加上throws后续, 则告诉调用f()的方法, f()函数有可能抛出这些异常的一种.

 

如果f()throws 了1个或若干个非RuntimeException,  则调用f()的函数必须处理这些非RuntimeException, 如上面的g()函数一样.

如果f() throws的都是RuntimeException, 则调用f()的函数可以不处理, 也能通过编译, 但是实际上还是强烈建议处理它们.

 

实际上, 如果1个方法f() throws A,B

那么它有可能不抛出任何异常.(程序运行状态良好)

也有能抛出C异常(应该避免, 最好在throws上加上C)

 

 
5.6 什么时候应该用throws

 

 
5.6.1 一个函数体里面手动throw了1个RumtimeException, 则这个函数的定义必须加上throws子句

 

这个是强制, 告诉别人这个函数内有炸弹.

 

 
5.6.2 一个函数内有可能由系统抛出异常.

 

这个是非强制的, 但是如果你知道一个函数内的代码有可能抛出异常, 最好还是写上throws 后缀

无论这个异常是否runtimeExcepion.

 

 
5.7 一般情况下,调用1个带有throws方法时怎么办

 

个人建议, 如果你调用1个函数throws A, B, C

那么你就在当前函数写上

try

catch(A)
catch(B)

catch(C)

catch(Exception)

 

 

这样能处理能保证你的函数能完整执行, 不会被收到的异常中断.

 

当然如果你允许你的函数可以被中断, 那么就可以在当前函数定义加上throws A, B 继续抛给上一级的函数.

 

 
5.8 重写方法时, throws的范围不能大于超类的对应方法.

 

例如你在一个派生类重写一个方法f(), 在超类里的f() throws A, B   你重写方法时就不throws出 A,,B,C 或者throws A和B的超类.

 

原因也是由于多态的存在.

因为1个超类的引用可以指向1个派生类的对象并调用不同的方法.  如果派生类throws的范围加大

 

那么利用多态写的代码的try catch就不再适用.  

 
六, throw和throws一些主要区别.

面试问得多,单独拉出来写了:

 

 

 
6.1 throw 写在函数体内, throws写在函数定义语句中.

 

应付面试官.

 

 
6.2 throw 是抛出1个异常对象, throws是有能抛出异常的种类

 

所以throw后面的一般加上new 和exception名字().

而throws后面不能加上new的

 

 
6.3 一个方法最多只能throw1个异常, 但是可以throws多个种类异常

 

因为一旦一个函数throw出1个异常, 这个函数就会被中断执行, 后面的代码被放弃, 如果你尝试在函数内写两个throw, 编译失败.

 

而throws 是告诉别人这个函数有可能抛出这几种异常的一种. 但是最多只会抛出一种.

 

 
6.4 如果在一个函数体内throw 1个非runtimeException, 那么必须在函数定义上加上throws后缀.  但反过来就不是必须的.

 

原因上面讲过了. 

 

 
七, 自定义异常.

 

我们可以自定义异常, 只需要编写1个类, 继承1个异常类就ok

例子:

 
[java] view plain copy
 

    package Exception_kng;  
      
    class User_Exception1 extends ArithmeticException{  
        public User_Exception1(String Exception_name){  
            super(Exception_name);  
        }  
      
        public void printStackTrace(){ //overwrite  
            super.printStackTrace();  
            System.out.printf("hey man, i am an user_defined excetpion\n");    
        }  
    }  
      
    class Exp6{  
        public int f(int a, int b){  
            if (0 == b){  
                throw new User_Exception1("Shit !!! / by zero!"); //use User_defined exception  
            }   
      
            return a/b;  
        }  
    }  
      
    public class Expt_6{  
        public static void g() {  
            Exp6 ex = new Exp6();  
            int i = 22;  
            try{  
                i = ex.f(8,0); //throw excetpion   
            }catch(User_Exception1 e){  
                e.printStackTrace();  
            }  
            System.out.printf("i is %d\n", i);   
            System.out.printf("g() is done!!\n");   
        }  
    }  

 

 

上面的类User_Exception1 就是1个自定义异常, 并重写了printStackTrace()方法.

 

 

 

 
八,java异常的优缺点.
8.1 c语言是如何处理程序错误的.

 

我们要理解异常的优缺点, 首先看看没有异常的C语言是如何处理错误的.

下面是个例子:

 
[cpp] view plain copy
 

    //openfile  
    if (fileOpen() > 0){  
        //check the length of the file  
        if (gotLengthOfTheFile() > 0){  
            //check the memory  
            if (gotEnoughMemory() > 0){  
                //load file to memory  
                if (loadFileToMem() > 0){  
                    readFile();  
                }else{  
                    errorCode = -5;  
                }  
      
            }else{  
                errorCode = -5;  
            }  
      
        }else{  
            errorCode = -5;  
        }  
      
    }else{  
        errorCode = -5;  
    }  
      
    //handle error  
    case errorCode....  
      
    //release Source  
    releaseSource();  

 

 

可以见到c语言处理错误有这些特点

1. 大部分精力都在错误处理.

2. 需要把各种可能出现的错误全部考虑到, 才能保证程序的稳定性.

3. 程序可读性差, 错误处理代码混杂在其他代码中.

4. 出错返回信息少, 一旦出错难以调试.

5. 一旦出现了未考虑到的错误, 资源释放代码无法执行.

 

 

 
8.2 java异常机制下是如何编写上述代码的.

 

 
[java] view plain copy
 

    try{  
        fileOpen();  
        gotLengthOfTheFile();  
        gotEnoughMemory();  
        loadFileToMem();  
        readFile();  
    }  
    catch(fileOpenFail) {  handle1()}  
    catch(gotLengthOfTheFileFail) {  handle2()}  
    catch(gotEnoughMemoryFail) {  handle3()}  
    catch(loadFileToMemFail) {  handle4()}  
    catch(readFileFail) {  handle4()}  
    catch(Exception e)   {  handle5()} //catch unexpected error  
    finally{  
        releasSource();   
    }  



 

 

 
8.3 java异常机制的优点:

 

由上面的代码可以看出部分优点:

1. 业务代码和错误处理代码分离.

2. 强制程序猿考虑程序的稳定性.

3. 有利于代码调试(异常信息)

4. 即使任何异常产生, 能保证占用的释放(finally)

8.4 java异常机制的缺点:

1. 异常嵌套难免影响代码可读性

2. 并不能令程序逻辑更加清晰.

3. 异常并不能解决所有问题
