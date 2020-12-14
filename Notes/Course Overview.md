# Course Overview
csapp : http://www.cs.cmu.edu/afs/cs/academic/class/15213-f15/www/index.html 
实验都是直接来自于书中讨论的内容，建议每一章读三遍，然后完成练习  
咳咳咳！想了一下！还是用中文吧！自己复习起来看的比较快，记录笔记也比较方便  
这门课主要告诉我们：你即使学会了编程，但是你可能并不了解计算机的本质，而这门课程就是要让你能够深入了解当执行你的代码时，计算机具体是在做什么的，通过这些来帮助你在你想做的事情上做得更好 
## Great Reality 1：Ints are not Integers, Floats are not Reals
我们理解中的数学`x^2 >= 0`在实数域中是成立的，如果计算一个数的平方，只要不是虚数，那么结果至少是0，假设一个例子，如果我们计算40,000的平方，那么大部分的计算机都会返回一个正确的结果，但是如果我们计算的是50,000的平方: (使用的工具Linux上是GDB,Mac os上是LLDB)  
![1.png](https://i.loli.net/2020/12/14/ZbfV6AexQGTrvoa.png)  
其实我们的程序没有出错，是因为我们的这台计算机期望的数字是32-bit的值：上限2,147,483,647，因为我们的2,500,000,000大于了2,147,483,647，多了352,516,353，所相减就是负数的那个值  
![2.png](https://i.loli.net/2020/12/14/LhpeDx8Z7nKHXcY.png)  
例子说明我们对整数运算的正常期望可能成立，也可能不成立，我们会看到这种情况是因为，当这些数字上升到能表示的极限最大值的时候，再加一就会变成了负数  
再看个例子:  
![3.jpg](https://i.loli.net/2020/12/14/uOrSMplmINL4k8Z.png)  
可以看到乘法的int满足交换律，当然在计算中int也满足加法的交换律`(x+y)+z=x+(y+z)`，但是再float中就不一定  
(3.14+1e20)-1e20求的值是0.0，而3.14+(1e20-1e20)求的值是3.14，因为1e20相比3.14大太多了，3.14就显得微不足道了，所以括号里的结果就是-1e20，使得最后得到错误的结果0  
`计算机中，float的取值范围特别大，当两个相差特别大的数进行运算时，可能会使得较小的数字消失，所以不满足结合律`
## Great Reality 2：Memory Matters
创建一个结构体对其进行赋值:  
```
#include <stdio.h>
typedef struct {
  int a[2];
  double d;
} struct_t;

double fun(int i) {
  volatile struct_t s;
  s.d = 3.14;
  s.a[i] = 1073741824; /* Possibly out of bounds */
  return s.d;
}

int main()
{
 double x = fun(0);
 printf("%.20f\n",x);
 double y = fun(1);
 printf("%.20f\n",y);
 double z = fun(2);
 printf("%.20f\n",z);
 double v = fun(3);
 printf("%.20f\n",v);
 double a = fun(4);
 printf("%.20f\n",a);
 double b = fun(5);
 printf("%.20f\n",b);
 double c = fun(6);
 printf("%.20f\n",c);
 return 0;
}
```
运行结果:  
![4.png](https://i.loli.net/2020/12/14/wegiNOZBMvflsuA.png)  
形成的原因和数据如何在内存中布局有关，也是在c运行的运行的时候不会做边界的检查，他们很希望我们访问，但是操作系统不是很喜欢，因为我们越界了  
![5.png](https://i.loli.net/2020/12/14/pQ8oS5cJ1FAlfKI.png)  
可以看到通过eax来改变了内存的内容，导致了内容改变，这是课上讲义上的图比较直观:  
![6.png](https://i.loli.net/2020/12/14/7RwCM6yrqWYd8fz.png)  
可以看到我们在2 3的时候改变了3.14的值，在6的时候修改了该程序的某些状态导致了程序的崩溃，它用于维持程序运行，记录已经分配的内存  
