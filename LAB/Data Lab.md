# DataLab

## datalab简介

因为自己看完了csapp的第一二章和cmu的视频嘛，来做一下datalab，写一下笔记

LAB网址：http://csapp.cs.cmu.edu/3e/labs.html

![1.png](https://i.loli.net/2020/12/23/TKhGwYL9QHMFnSe.png) 

注意的问题以及使用的方法：

- make后如果报错没有libc-header-start.h，安装 gcc-multlib即可
- 编译后的fshow可以查看浮点数

![2.png](https://i.loli.net/2020/12/23/SXfWKt47pxMYrNd.png)

可以查看我们的sign exp frac从而表示这个十六进制数字代表什么浮点数

- btest来判断我们实现的函数是否正确
- dlc -e 输出我们每个函数所使用的运算符的个数
- driver.pl是功能btest和dlc的结合体

## lab代码

### bitXor

bitXor -  x^y using only ~ and &

异或只能用与非来表示，我们可以看一下位级远算表：

![3.png](https://i.loli.net/2020/12/23/l9I6uEVZCY8D4Ob.png) 

因为A^B = ~(A&B)&(A|B) ------> A|B = ~(~A&~B) 所以 A^B = ~(A&B)&~(~A&~B)

```c
int bitXor(int x, int y) 
{
  // x ^ y : 相同的 1  1   0  0  0  1  1  0  0  0
  /*
  真值表：
  xor:            or:           and:
  1 1 = 0         1 1 = 1       1 1 = 1
  1 0 = 1         1 0 = 1       1 0 = 0
  0 1 = 1         0 1 = 1       0 1 = 0
  0 0 = 0         0 0 = 0       0 0 = 0
  */
  //假设1 1 的情况最后的结果是false所以一定要最后进行的操作等于0 所以中间是and的操作要把两个A B 变成一个是1 一  个是 0      ~(A&B)&(A|B)
  //A^B = ~(A&B)&(A|B)  --->  A|B  = ~(~A&~B)  ---> ~(A&B)&(~(~A&~B))
  return ~(x&y)&(~(~x&~y));
}
```

### tmin

tmin - return minimum two's complement integer

Tmin = 2^(m-1) 因为编译是32位的所以 `1<<31`

```c
int tmin(void) 
{
  //TMIN = 2^(M-1)  (32位)  2^31
  return 1<<31;
}
```

### isTmax

isTmax - returns 1 if x is the maximum, two's complement number,and 0 otherwise

Tmax = 2^(m-1) - 1 因为 Tmax+1 = Tmin Tmin = ~(Tmax+1)+1 = -Tmin 因为除了Tmin=-Tmin 还有个情况就是 0 = - 0 所以要除去全1的情况

```c
int isTmax(int x) 
{
  //TMAX = 2^31 - 1  如果 + 1  = 2^31就是TMIN了
  //TMIN = 1000...00    ~TMIN = TMIN  自己异或自己就是0 overflow也是0
  return (!((~(x+1)+1)^(x+1)))&(!!(x+1));
}
```

### allOddBits

allOddBits - return 1 if all odd-numbered bits in word set to 1，where bits are numbered from 0 (least significant) to 31 (most significant)

因为要判断奇数位置是否为1，所以我们可以直接将偶数位置的都为1，和奇数位置都为1的数进行或操作，进行取反看是否是0

```c
int allOddBits(int x) 
{
  //如果所有奇数位上的值是1 那么就返回1 
  //0xFFFFFFFD = 1111 1111 1111 1111 1111 1111 1111 1101
  //0xAAAAAAAA = 1010 1010 1010 1010 1010 1010 1010 1010
  //好像偶数位置是1的时候才是1 偶数位置不是1的时候就是0
  //规定了整数常量的范围 0 - 255
  int a = 0x55; //0101
  a = a + (a << 8) + (a << 16) + (a << 24);
  return !(~(a|x));
}
```

### negate

negate  - return -x 

这个就比较简单了，取反加一即可

```c
int negate(int x) 
{
  return (~x+1);
}
```

### isAsciiDigit

isAsciiDigit - return 1 if 0x30 <= x <= 0x39 (ASCII codes for characters '0' to '9')

计算输入的数字是否是 0 - 9 的ASCII，可以看出来一个特点，数字+0x30以后就是自身的ASCII码，使用减法的指令来判断，因为减法就是加上一个负数，假设我们x在范围中减去0x30得到的是正数符号位为0，减去0x39是负数最高位是1，我们直接判定最高位即可，但是0x39这里有个情况就是0x39-0x39最高位是0，且本身是0，我们取反即可

```c
int isAsciiDigit(int x) 
{
  return (!((x+(~0x30+1))>>31)) & (((x+(~0x39+1))>>31) | !(x+(~0x39+1)));
}
```

### conditional

conditional - same as x ? y : z 

要判定第一个是否为1或者0

```c
int conditional(int x, int y, int z)
{
  //x判定是1还是0
  //!x
  return ((~(((!x)<<31)>>31))&y) + ((((!x)<<31)>>31)&z);
}
```

### isLessOrEqual

isLessOrEqual - if x <= y  then return 1, else return 0 

判断两种情况，一个是符号相同（正负），一个就是符号不相同，如果符号相同的话，判断的结果如上面的isAsciiDigit即可，我们判定的条件就是减法的结果是正就是小于，如果符号不相同就很好判断了

```c
int isLessOrEqual(int x, int y) 
{
  //x-y
  //x+((~y)+1)>>31 & 0x1
  int sub = x+((~y)+1); //x-y
  int subL = (sub>>31)&1;
  int xL = (1<<31)&x;
  int yL = (1<<31)&y;
  int xor = ((xL^yL)>>31)&1;
  return ((subL|!sub)&!xor)|(xor&(!(yL>>31)));
}
```

### logicalNeg

logicalNeg - implement the ! operator, using all of the legal operators except !

用位级运算表示逻辑非，0的相反数还是0，所以相或的话，我们的最高位还是0

```c
int logicalNeg(int x) 
{
  return ((x|((~x)+1))>>31)+1;
}
```

### howManyBits

howManyBits - return the minimum number of bits required to represent x in two's complement

判定two's complement有多少位，思路就是 16+8+4+2+1+0 ---> 移位判定，不管是正数负数都会有符号位的

```c
int howManyBits(int x) 
{
  int b16,b8,b4,b2,b1,b0;
  x = ((x>>31)&~x)|(~(x>>31)&x);
  b16 = !!(x>>16)<<4;
  x = x>>b16;
  b8 = !!(x>>8)<<3;
  x = x>>b8;
  b4 = !!(x>>4)<<2;
  x = x>>b4;
  b2 = !!(x>>2)<<1;
  x = x>>b2;
  b1 = !!(x>>1);
  x = x>>b1;
  b0 = x;
  return b16+b8+b4+b2+b1+b0+1;
}
```

### floatScale2

floatFloat2Int - Return bit-level equivalent of expression (int) f for floating point argument f.

乘2，因为浮点数表示为：(-1)^s1 M1 2^(E) ，规格化数如果我们乘2相当于E+1，非格式化数就直接尾数左移1

```c
unsigned floatScale2(unsigned uf) 
{
  int exp = (uf & 0x7f800000) >> 23;
  int sig = (uf>>31)<<31;
  if(exp == 0)
  {
    return sig | uf<<1;
  }
  if(exp == 255)
  {
    return uf;
  }
  if(exp+1 == 255)
  {
    return sig | 0x7f800000;
  }
  return ((exp+1)<<23) | (uf & 0x807fffff) ;
}
```

### floatFloat2Int

floatFloat2Int - Return bit-level equivalent of expression (int) f for floating point argument f.

将浮点数转换为整数

```c
int floatFloat2Int(unsigned uf) {
  int exp = ((uf&0x7f800000)>>23)-127;
  int sig = (uf>>31)&0x1;
  int fac = uf&0x7fffff;
  int M = fac + (1<<23);
  if(exp < 0)
  {
    return 0;
  }
  if(exp>31)
  {
    return 0x80000000u;
  }
  if(exp>23) 
  M=M<<(exp-23);
	else 
  M=M>>(23-exp);	

  if(sig)
  {
    if(M>>31)
    {
    return 0x80000000u;
    }
    else
    {
      return ~M+1; 
    }   
  }
  else
  {
    if(M>>31)
    {
    return 0x80000000u;
    }
    else
    {
      return M;
    } 
  }
}
```

### floatPower2

floatPower2 - Return bit-level equivalent of the expression 2.0^x (2.0 raised to the power x) for any 32-bit integer x.

2.0浮点数的乘法，我们主要去看exp即可，因为E = e - 127 范围为 -126到127

```c
unsigned floatPower2(int x) {
	int max=0x7f800000;
	if(x<-126) 
  return 0;
	if(x>127) 
  return max;
	return (x+127)<<23;
}
```

### 成果图：

![4.png](https://i.loli.net/2020/12/23/uYs7BN4WeaMxAZU.png)
