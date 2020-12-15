# Bits Bytes and Integr
这节课主要介绍了计算机有符号和无符号的问题，以及补码的问题  
## Everything is bits
现代计算机处理和存储的信息是以二进制信号表示的，是基于二进制编码好处在于：
- 比如我们可以将低电压表示0，将高电压表示1，如果电路中存在噪音或不完善的地方，只要不超过你设定的阈值，你就会得到一个清晰的信号
- 对于信息存储而言，存储一位信息或一个数字值比存储一个模拟值更容易  
![1.png](https://i.loli.net/2020/12/15/M718ZEGoyUSYLjT.jpg) 

在计算机中我们用比特位来表示数字，他可以表示小数，整数等等，因为比如说32位，64位看起来太为繁琐，所以通常以4比特为集合分组，然后用16进制表示，就出现了ABCDEF  
这是我们C设计的时候的一些类型长度，这里的pointer，他会根据自己本身机器的位数来决定它的长度  
![2.png](https://i.loli.net/2020/12/15/UHknCrVbO2yQoRa.jpg)  
## Boolen Algebra
位级运算: 
- And: A & B = 1 when both A = 1 and B = 1
- Or: A | B = 1 when either A = 1 or B = 1
- Not: ~ A = 1 when A = 0
- Exclusive-Or (Xor): A ^ B = 1 when either A = 1 or B = 1 but not both equal

位向量的一个重要应用就是表示有限集合，所以一个字节可以表示8种情况  
比如: [01101001]表示集合{0,3,5,6}，这样布尔运算的|和分别对应于集合的并和交，而~对应于集合的补、^对应于集合的对称差  
## Contrast to Logical Operators
逻辑运算符||、&&和!分别对应于OR、AND和NOT运算  
在逻辑运算中只要是非零的数据就表示为TRUE，全零的数据就表示为FALSE，所以计算时候先将其转换为TRUE和FALSE，然后计算出来的结果只会是0x00或0x01，分别对应FALSE和TRUE，但是在位级运算符中，他们只有BOOL类型的选择也就是0x00与0x01  
逻辑运算中有一个特点：提前终止(Early Termination)，如果左侧表达式可以表达最后的结果，那么右侧表达式则不看，该语句提前终止  
## Shift Operations
这里主要介绍了一个问题就是算数右移和逻辑右移，因为我们数据在左移的情况下是不会出现问题的，但是右移的时候，我们要看是什么类型的右移，逻辑右移就是丢弃最低的k位，并在左侧补充k个零，而算数右移中，因为有符号数使用最高位来表示数字的正负性，丢弃最低的k位，并在左侧补充k个最高位的数值  
当k的值大于数据类型的位数w时，有些机器，比如java/python会通过计算k mod w来确定位移量，所以对于8位的数左移8位，该数不变，但是我们的c语言中不会有这种的设定，需要自己去判定k的取值范围  
## Numberic Rangs
这里介入的两个方程式：
![3.png](https://i.loli.net/2020/12/15/nSlXV9uH4qevTmo.jpg)  
从而我们可以推出来几个公式：
- Umax = ( 2 ^ w ) - 1
- Umin = 0
- Tmax = ( 2 ^ (w-1) ) - 1
- Tmin = - (2 ^ (w-1) )

![6.png](https://i.loli.net/2020/12/15/c15XSGIk3unmYPK.jpg)  
根据上面的图，又可以推导出公式:
- | Tmin | = Tmax + 1
- Umax = Tmax * 2 + 1
C中有一些内置函数返回的是无符号数，比如sizeof，规则是运算的时候，如果其中无符号数参与了运算那么参与运算的有符号数也会变成无符号数，举个例子:  
```
#include "stdio.h"
int main(){
	int x = -1;
	unsigned int y = 0;
	if (x > y) {
		printf("x > y\n");
	} else if (x == y) {
		printf("x = y\n");
	} else {
		printf("x < y\n");
	}
  return 0;
}
```
![4.png](https://i.loli.net/2020/12/15/lGmWPxR6Avrg9Mw.jpg)
![5.png](https://i.loli.net/2020/12/15/CowT58DjtH4FiZ1.jpg)  
可以看到这两个例子的无符号数的运算时候的影响  
## Mapping signed <-> Unsigned
这两种的关系可以用一个方程组来表示:  
T2U -> x>=0时候就是x  /  x<0的时候为(2^w)+x (w是字长)
![7.png](https://i.loli.net/2020/12/15/dwE8l2YbmM7KFys.jpg)
## Sign Extension
![8.png](https://i.loli.net/2020/12/15/z1BGd7JcqrUkLNm.jpg)
如图所示的扩展，是最完美的形式，我们可以用数学来类推，我们最高位是0就不说了，如果是1:假设例:  
1001 ->  1....1001 我们最高位的1是负数，第n-1位置的是n位的1/2，一直截断下去，和原来的结果是一样的！
