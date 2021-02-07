# bomb lab

## BOMB lab简介

这个lab主要是要去拆除6个炸弹，如果不拆除就会boom像这样，感觉这个实验有点像ctf去获得flag的格式

![1.png](https://i.loli.net/2020/12/28/F1O7tZ5WEnRmIAM.png)

将bomb生成bomb.s文件

![2.png](https://i.loli.net/2020/12/28/SfRHTexjgOF6uQL.png)

直接可以对比来看

![3.png](https://i.loli.net/2020/12/28/ZopyweaJlD6ci2K.png)

### phase_1

![4.png](https://i.loli.net/2020/12/28/roPYbQ2IZwsxL7k.png)

```asm
=> 0x0000000000400ee0 <+0>:	sub    rsp,0x8	;提升堆顶
   0x0000000000400ee4 <+4>:	mov    esi,0x402400	;esi = 0x402400
   0x0000000000400ee9 <+9>:	call   0x401338 <strings_not_equal> ;call 地址0x401338
   0x0000000000400eee <+14>:	test   eax,eax ;判定eax是不是等于0
   0x0000000000400ef0 <+16>:	je     0x400ef7 <phase_1+23>	;eax等于0的时候进行跳转
   0x0000000000400ef2 <+18>:	call   0x40143a <explode_bomb>	;eax不等于0执行call
   0x0000000000400ef7 <+23>:	add    rsp,0x8	;恢复堆栈
   0x0000000000400efb <+27>:	ret 	;跳转出去
```

可以看到 rbx存放了我们输入的值，而rbp存放了0x402400地址中的数据

![5.png](https://i.loli.net/2020/12/28/dvbVGNqD3wgAlou.png)

判断输入的大小和地址0x402400中的数据是否一样

![6.png](https://i.loli.net/2020/12/28/3jTbuK7REY8kMei.png)

进行我们输入的数据和地址0x402400的比较

![7.png](https://i.loli.net/2020/12/28/kUXc5Ff7NHEaBqh.png)

如果成功了，我们的输入的地址和比较的地址向后加1，依次遍历一直到比较数组最后为0为止

![8.png](https://i.loli.net/2020/12/28/m7RuUibtH56EgPZ.png)

所以第一关是：

![9.png](https://i.loli.net/2020/12/28/zJEUGwFXPSrvqAj.png)

```
Border relations with Canada have never been better.
```

### phase_2

![10.png](https://i.loli.net/2020/12/28/7wHIYLnGB5SUcZt.png)

```asm
=> 0x0000000000400efc <+0>:	push   rbp	;rbp压入堆栈
   0x0000000000400efd <+1>:	push   rbx	;rbx压入堆栈
   0x0000000000400efe <+2>:	sub    rsp,0x28	;提升rsp
   0x0000000000400f02 <+6>:	mov    rsi,rsp	;rsi = rsp
   0x0000000000400f05 <+9>:	call   0x40145c <read_six_numbers>	;call 地址
   0x0000000000400f0a <+14>:	cmp    DWORD PTR [rsp],0x1	;比较rsp地址内存值与0x1是否相等
   0x0000000000400f0e <+18>:	je     0x400f30 <phase_2+52>		;相等跳转
   0x0000000000400f10 <+20>:	call   0x40143a <explode_bomb>	;false
   0x0000000000400f15 <+25>:	jmp    0x400f30 <phase_2+52>	;跳转到f30
   0x0000000000400f17 <+27>:	mov    eax,DWORD PTR [rbx-0x4]	;将rbx-0x4的地址的内容给eax
   0x0000000000400f1a <+30>:	add    eax,eax	;eax*2
   0x0000000000400f1c <+32>:	cmp    DWORD PTR [rbx],eax	;比较rbx内容和eax的值
   0x0000000000400f1e <+34>:	je     0x400f25 <phase_2+41>	;相等跳转到25
   0x0000000000400f20 <+36>:	call   0x40143a <explode_bomb>	;false
   0x0000000000400f25 <+41>:	add    rbx,0x4	;	rbx+0x4 ->下一个数字
   0x0000000000400f29 <+45>:	cmp    rbx,rbp	;	比较rbx是否等于rbp，这个rbp应该就是最后的判定到哪里
   0x0000000000400f2c <+48>:	jne    0x400f17 <phase_2+27>	;回到17
   0x0000000000400f2e <+50>:	jmp    0x400f3c <phase_2+64>	;到3c正确了
   0x0000000000400f30 <+52>:	lea    rbx,[rsp+0x4]	;	rbx = rsp + 0x4
   0x0000000000400f35 <+57>:	lea    rbp,[rsp+0x18]	;	rbp = rsp + 0x18
   0x0000000000400f3a <+62>:	jmp    0x400f17 <phase_2+27>	;	跳转到17
   0x0000000000400f3c <+64>:	add    rsp,0x28	;	恢复堆栈
   0x0000000000400f40 <+68>:	pop    rbx	;	
   0x0000000000400f41 <+69>:	pop    rbp	;
   0x0000000000400f42 <+70>:	ret  
```

从0x18知道我们要比较6个数字，我们的rbp直接等于了rsp+0x18来限定，rsp+0x4来去判定第二个位置，因为要比较rsp内存地址和0x1的，所以因为后面要比较eax依次乘二，可以推断出来输入的6个数字是：1，2，4，8，16，32

可以看到 0x6037d0是我们输入的东西

![11.png](https://i.loli.net/2020/12/28/SznICPrgAGhLdyR.png)

比较输入的数字的个数是否比5大

![12.png](https://i.loli.net/2020/12/28/KxoynW1LZ9rlJX5.png)

所以第二关是：

![13.png](https://i.loli.net/2020/12/28/CM6NOW7UDGIldH4.png)

```
1 2 4 8 16 32
```

### phase_3

![14.png](https://i.loli.net/2020/12/28/c8MSmIos5DH1hEb.png)

```asm
=> 0x0000000000400f43 <+0>:	sub    rsp,0x18	;提升堆栈
   0x0000000000400f47 <+4>:	lea    rcx,[rsp+0xc]	;rcx = rsp+0xc
   0x0000000000400f4c <+9>:	lea    rdx,[rsp+0x8]	;rdx = rsp+0x8
   0x0000000000400f51 <+14>:	mov    esi,0x4025cf	;esi = 0x4025cf　
   0x0000000000400f56 <+19>:	mov    eax,0x0	;eax = 0
   0x0000000000400f5b <+24>:	call   0x400bf0 <__isoc99_sscanf@plt>	;sscanf
   0x0000000000400f60 <+29>:	cmp    eax,0x1	;比较eax是否等于1，上面的经验sscanf会返回我们输入的个数给eax
   0x0000000000400f63 <+32>:	jg     0x400f6a <phase_3+39>	;大于就到6a
   0x0000000000400f65 <+34>:	call   0x40143a <explode_bomb>	;false
   0x0000000000400f6a <+39>:	cmp    DWORD PTR [rsp+0x8],0x7	;比较rsp+0x8，0x7
   0x0000000000400f6f <+44>:	ja     0x400fad <phase_3+106>	;如果大于就是false
   0x0000000000400f71 <+46>:	mov    eax,DWORD PTR [rsp+0x8]	;eax = rsp+0x8内存中的值
   0x0000000000400f75 <+50>:	jmp    QWORD PTR [rax*8+0x402470]	;跳转到 rax*8 + 0x402470
   ;下面都类似于switch case
   0x0000000000400f7c <+57>:	mov    eax,0xcf	
   0x0000000000400f81 <+62>:	jmp    0x400fbe <phase_3+123>	
   0x0000000000400f83 <+64>:	mov    eax,0x2c3	
   0x0000000000400f88 <+69>:	jmp    0x400fbe <phase_3+123>	
   0x0000000000400f8a <+71>:	mov    eax,0x100	
   0x0000000000400f8f <+76>:	jmp    0x400fbe <phase_3+123>	
   0x0000000000400f91 <+78>:	mov    eax,0x185	
   0x0000000000400f96 <+83>:	jmp    0x400fbe <phase_3+123>	
   0x0000000000400f98 <+85>:	mov    eax,0xce	
   0x0000000000400f9d <+90>:	jmp    0x400fbe <phase_3+123>	
   0x0000000000400f9f <+92>:	mov    eax,0x2aa	
   0x0000000000400fa4 <+97>:	jmp    0x400fbe <phase_3+123>	
   0x0000000000400fa6 <+99>:	mov    eax,0x147	
   0x0000000000400fab <+104>:	jmp    0x400fbe <phase_3+123>	
   0x0000000000400fad <+106>:	call   0x40143a <explode_bomb>	
   0x0000000000400fb2 <+111>:	mov    eax,0x0	
   0x0000000000400fb7 <+116>:	jmp    0x400fbe <phase_3+123>	
   0x0000000000400fb9 <+118>:	mov    eax,0x137	
   0x0000000000400fbe <+123>:	cmp    eax,DWORD PTR [rsp+0xc]	;比较
   0x0000000000400fc2 <+127>:	je     0x400fc9 <phase_3+134>	
   0x0000000000400fc4 <+129>:	call   0x40143a <explode_bomb>	
   0x0000000000400fc9 <+134>:	add    rsp,0x18	
   0x0000000000400fcd <+138>:	ret	
```

可以知道输入是两个参数

![15.png](https://i.loli.net/2020/12/28/pTUAfuJeYinK3tH.png)

获取switch case的跳转表

![16.png](https://i.loli.net/2020/12/28/R7XGvl5f3CADxQM.png)

获得我们地址和rax的关系：

```
0x0000000000400f7c(0)	0x0000000000400fb9(1)
0x0000000000400f83(2)	0x0000000000400f8a(3)
0x0000000000400f91(4)	0x0000000000400f98(5)
0x0000000000400f9f(6)	0x0000000000400fa6(7)
```

对照cmp的比较表可以得到：

```
0 0xcf
1	0x137
2 0x2c3
3 0x100	
4 0x185	
5 0xce	
6	0x2aa	
7	0x147
```

所以第三关是：

![17.png](https://i.loli.net/2020/12/28/9uXUOCcrgsZRJzn.png) 

```
0 207
1	331
2 707
3 256
4 389
5 206	
6	682	
7	327
```

### phase_4

![18.png](https://i.loli.net/2020/12/28/Yk9GS7tx8E1QZnK.png) 

```asm
=> 0x000000000040100c <+0>:		sub    rsp,0x18	;栈提升
   0x0000000000401010 <+4>:		lea    rcx,[rsp+0xc]	;rcx = rsp + 0xc
   0x0000000000401015 <+9>:		lea    rdx,[rsp+0x8]	;rdx = rsp + 0x8
   0x000000000040101a <+14>:	mov    esi,0x4025cf	;esi = 0x4025cf
   0x000000000040101f <+19>:	mov    eax,0x0	;eax = 0
   0x0000000000401024 <+24>:	call   0x400bf0 <__isoc99_sscanf@plt> ;sscanf
   0x0000000000401029 <+29>:	cmp    eax,0x2	;比较输入的参数是否等于2
   0x000000000040102c <+32>:	jne    0x401035 <phase_4+41>	;不等于2 false
   0x000000000040102e <+34>:	cmp    DWORD PTR [rsp+0x8],0xe	;比较第一个参数与0xe
   0x0000000000401033 <+39>:	jbe    0x40103a <phase_4+46>	;如果小于等于跳转到3a
   0x0000000000401035 <+41>:	call   0x40143a <explode_bomb>	;false
   0x000000000040103a <+46>:	mov    edx,0xe	;edx = 0xe
   0x000000000040103f <+51>:	mov    esi,0x0	;esi = 0x0
   0x0000000000401044 <+56>:	mov    edi,DWORD PTR [rsp+0x8]	;edi = 第一个参数
   0x0000000000401048 <+60>:	call   0x400fce <func4>	;call func4
   0x000000000040104d <+65>:	test   eax,eax	;满足func返回的eax等于1的时候才可以继续往下执行jne下面的代码
   0x000000000040104f <+67>:	jne    0x401058 <phase_4+76>
   0x0000000000401051 <+69>:	cmp    DWORD PTR [rsp+0xc],0x0	;第二个参数等于0的时候
   0x0000000000401056 <+74>:	je     0x40105d <phase_4+81>
   0x0000000000401058 <+76>:	call   0x40143a <explode_bomb>
   0x000000000040105d <+81>:	add    rsp,0x18
   0x0000000000401061 <+85>:	ret  
```

知道输入的参数是两个

![19.png](https://i.loli.net/2020/12/28/xPbWYJOyA1BFvig.png)

看一下fun4函数做了什么：

因为我们知道第二个参数是0，第一个参数是fun4的参数，我们直接将fun4转化成c语言，之后我们再跑出符合返回值结果的即可

```asm
=> 0x0000000000400fce <+0>:		sub    rsp,0x8	
   0x0000000000400fd2 <+4>:		mov    eax,edx	
   0x0000000000400fd4 <+6>:		sub    eax,esi	
   0x0000000000400fd6 <+8>:		mov    ecx,eax	
   0x0000000000400fd8 <+10>:	shr    ecx,0x1f	
   0x0000000000400fdb <+13>:	add    eax,ecx	
   0x0000000000400fdd <+15>:	sar    eax,1	
   0x0000000000400fdf <+17>:	lea    ecx,[rax+rsi*1]	
   0x0000000000400fe2 <+20>:	cmp    ecx,edi	
   0x0000000000400fe4 <+22>:	jle    0x400ff2 <func4+36>	
   0x0000000000400fe6 <+24>:	lea    edx,[rcx-0x1]
   0x0000000000400fe9 <+27>:	call   0x400fce <func4>
   0x0000000000400fee <+32>:	add    eax,eax
   0x0000000000400ff0 <+34>:	jmp    0x401007 <func4+57>
   0x0000000000400ff2 <+36>:	mov    eax,0x0
   0x0000000000400ff7 <+41>:	cmp    ecx,edi
   0x0000000000400ff9 <+43>:	jge    0x401007 <func4+57>
   0x0000000000400ffb <+45>:	lea    esi,[rcx+0x1]
   0x0000000000400ffe <+48>:	call   0x400fce <func4>
   0x0000000000401003 <+53>:	lea    eax,[rax+rax*1+0x1]
   0x0000000000401007 <+57>:	add    rsp,0x8
   0x000000000040100b <+61>:	ret   
```

```c
/*
=> 0x0000000000400fce <+0>:		sub    rsp,0x8
   0x0000000000400fd2 <+4>:		mov    eax,edx
   0x0000000000400fd4 <+6>:		sub    eax,esi
   0x0000000000400fd6 <+8>:		mov    ecx,eax
   0x0000000000400fd8 <+10>:	shr    ecx,0x1f
   0x0000000000400fdb <+13>:	add    eax,ecx
   0x0000000000400fdd <+15>:	sar    eax,1
   0x0000000000400fdf <+17>:	lea    ecx,[rax+rsi*1]
   0x0000000000400fe2 <+20>:	cmp    ecx,edi
   0x0000000000400fe4 <+22>:	jle    0x400ff2 <func4+36>
   0x0000000000400fe6 <+24>:	lea    edx,[rcx-0x1]
   0x0000000000400fe9 <+27>:	call   0x400fce <func4>
   0x0000000000400fee <+32>:	add    eax,eax
   0x0000000000400ff0 <+34>:	jmp    0x401007 <func4+57>
   0x0000000000400ff2 <+36>:	mov    eax,0x0
   0x0000000000400ff7 <+41>:	cmp    ecx,edi
   0x0000000000400ff9 <+43>:	jge    0x401007 <func4+57>
   0x0000000000400ffb <+45>:	lea    esi,[rcx+0x1]
   0x0000000000400ffe <+48>:	call   0x400fce <func4>
   0x0000000000401003 <+53>:	lea    eax,[rax+rax*1+0x1]
   0x0000000000401007 <+57>:	add    rsp,0x8
   0x000000000040100b <+61>:	ret
*/

#include "stdio.h"

//A -> edi  B -> esi C -> edx D -> ecx
int64_t  func4(int64_t a1, int64_t a2, int64_t a3)
{
    signed int v3; // ecx
    int64_t result; // rax

    v3 = (a3 - a2) / 2 + a2;
    if ( v3 > a1 )
        return 2 * (unsigned int)func4(a1, a2, (unsigned int)(v3 - 1));
    result = 0LL;
    if ( v3 < a1 )
        result = 2 * (unsigned int)func4(a1, (unsigned int)(v3 + 1), a3) + 1;
    return result;
}


int main(){
    for(int i = 0 ; i < 0xe ; i++)
    {
        int answer = func4(i, 0 , 0xe);
        if(answer == 0){
            printf("%d\n",i);
        }
    }
    return 0;
}
```

直接跑出来结果：

![20.png](https://i.loli.net/2020/12/28/BKAPjQzOclvWiZI.png)

所以第四关是：

![21.png](https://i.loli.net/2020/12/28/EZhq5KbXTAMJlBx.png)

```
0 0
1 0
3 0
7 0
```

### phase_5

![22.png](https://i.loli.net/2020/12/28/aU6VfNFi5HuetCG.png)

```asm
=> 0x0000000000401062 <+0>:	push   rbx
   0x0000000000401063 <+1>:	sub    rsp,0x20
   0x0000000000401067 <+5>:	mov    rbx,rdi
   0x000000000040106a <+8>:	mov    rax,QWORD PTR fs:0x28
   0x0000000000401073 <+17>:	mov    QWORD PTR [rsp+0x18],rax
   0x0000000000401078 <+22>:	xor    eax,eax
   0x000000000040107a <+24>:	call   0x40131b <string_length>
   0x000000000040107f <+29>:	cmp    eax,0x6
   0x0000000000401082 <+32>:	je     0x4010d2 <phase_5+112>
   0x0000000000401084 <+34>:	call   0x40143a <explode_bomb>
   0x0000000000401089 <+39>:	jmp    0x4010d2 <phase_5+112>
   0x000000000040108b <+41>:	movzx  ecx,BYTE PTR [rbx+rax*1]
   0x000000000040108f <+45>:	mov    BYTE PTR [rsp],cl
   0x0000000000401092 <+48>:	mov    rdx,QWORD PTR [rsp]
   0x0000000000401096 <+52>:	and    edx,0xf
   0x0000000000401099 <+55>:	movzx  edx,BYTE PTR [rdx+0x4024b0]
   0x00000000004010a0 <+62>:	mov    BYTE PTR [rsp+rax*1+0x10],dl
   0x00000000004010a4 <+66>:	add    rax,0x1
   0x00000000004010a8 <+70>:	cmp    rax,0x6
   0x00000000004010ac <+74>:	jne    0x40108b <phase_5+41>
   0x00000000004010ae <+76>:	mov    BYTE PTR [rsp+0x16],0x0
   0x00000000004010b3 <+81>:	mov    esi,0x40245e
   0x00000000004010b8 <+86>:	lea    rdi,[rsp+0x10]
   0x00000000004010bd <+91>:	call   0x401338 <strings_not_equal>
   0x00000000004010c2 <+96>:	test   eax,eax
   0x00000000004010c4 <+98>:	je     0x4010d9 <phase_5+119>
   0x00000000004010c6 <+100>:	call   0x40143a <explode_bomb>
   0x00000000004010cb <+105>:	nop    DWORD PTR [rax+rax*1+0x0]
   0x00000000004010d0 <+110>:	jmp    0x4010d9 <phase_5+119>
   0x00000000004010d2 <+112>:	mov    eax,0x0
   0x00000000004010d7 <+117>:	jmp    0x40108b <phase_5+41>
   0x00000000004010d9 <+119>:	mov    rax,QWORD PTR [rsp+0x18]
   0x00000000004010de <+124>:	xor    rax,QWORD PTR fs:0x28
   0x00000000004010e7 <+133>:	je     0x4010ee <phase_5+140>
   0x00000000004010e9 <+135>:	call   0x400b30 <__stack_chk_fail@plt>
   0x00000000004010ee <+140>:	add    rsp,0x20
   0x00000000004010f2 <+144>:	pop    rbx
   0x00000000004010f3 <+145>:	ret  
```

比较输入的字符的个数：输入的字符的个数需要6个

![23.png](https://i.loli.net/2020/12/28/UJLY8Teh53SHMgc.png)

输入的字符在赋给了rcx，输入字符的后4位给了edx，edx相当于索引，0x4024b0应该有索引的表

![24.png](https://i.loli.net/2020/12/28/LbDXzqPpxG35iCI.png)

索引表：

![25.png](https://i.loli.net/2020/12/28/9r8jcySCMKkm37i.png)

```
maduiersnfotvbylSo you think you can stop the bomb with ctrl-c, do you?
```

因为最大就是0xf，所以maduiersnfotvbyl是数组的索引，索引最后的值需要等于flyers

![26.png](https://i.loli.net/2020/12/28/FgPfvdUSyXaQVlw.png)

所以索引分别是：0x9,0xf,0xe,0x5,0x6,0x7

`man ascis`查看

![27.png](https://i.loli.net/2020/12/28/XLOuTgf1QARMtr3.png)

所以可以是：IONEFG

![28.png](https://i.loli.net/2020/12/28/G6URL9u1JPzHbnc.png)

所以第五关是：

```
IONEFG
```

### phase_6

![29.png](https://i.loli.net/2020/12/28/4hfb7irOPAvjSY9.png)

```asm
=> 0x00000000004010f4 <+0>:	push   r14
   0x00000000004010f6 <+2>:	push   r13
   0x00000000004010f8 <+4>:	push   r12
   0x00000000004010fa <+6>:	push   rbp
   0x00000000004010fb <+7>:	push   rbx
   0x00000000004010fc <+8>:	sub    rsp,0x50
   0x0000000000401100 <+12>:	mov    r13,rsp
   0x0000000000401103 <+15>:	mov    rsi,rsp
   0x0000000000401106 <+18>:	call   0x40145c <read_six_numbers>
   0x000000000040110b <+23>:	mov    r14,rsp
   0x000000000040110e <+26>:	mov    r12d,0x0
   0x0000000000401114 <+32>:	mov    rbp,r13
   0x0000000000401117 <+35>:	mov    eax,DWORD PTR [r13+0x0]
   0x000000000040111b <+39>:	sub    eax,0x1
   0x000000000040111e <+42>:	cmp    eax,0x5
   0x0000000000401121 <+45>:	jbe    0x401128 <phase_6+52>
   0x0000000000401123 <+47>:	call   0x40143a <explode_bomb>
   0x0000000000401128 <+52>:	add    r12d,0x1
   0x000000000040112c <+56>:	cmp    r12d,0x6
   0x0000000000401130 <+60>:	je     0x401153 <phase_6+95>
   0x0000000000401132 <+62>:	mov    ebx,r12d
   0x0000000000401135 <+65>:	movsxd rax,ebx
   0x0000000000401138 <+68>:	mov    eax,DWORD PTR [rsp+rax*4]
   0x000000000040113b <+71>:	cmp    DWORD PTR [rbp+0x0],eax
   0x000000000040113e <+74>:	jne    0x401145 <phase_6+81>
   0x0000000000401140 <+76>:	call   0x40143a <explode_bomb>
   0x0000000000401145 <+81>:	add    ebx,0x1
   0x0000000000401148 <+84>:	cmp    ebx,0x5
   0x000000000040114b <+87>:	jle    0x401135 <phase_6+65>
   0x000000000040114d <+89>:	add    r13,0x4
   0x0000000000401151 <+93>:	jmp    0x401114 <phase_6+32>
   0x0000000000401153 <+95>:	lea    rsi,[rsp+0x18]
   0x0000000000401158 <+100>:	mov    rax,r14
   0x000000000040115b <+103>:	mov    ecx,0x7
   0x0000000000401160 <+108>:	mov    edx,ecx
   0x0000000000401162 <+110>:	sub    edx,DWORD PTR [rax]
   0x0000000000401164 <+112>:	mov    DWORD PTR [rax],edx
   0x0000000000401166 <+114>:	add    rax,0x4
   0x000000000040116a <+118>:	cmp    rax,rsi
   0x000000000040116d <+121>:	jne    0x401160 <phase_6+108>
   0x000000000040116f <+123>:	mov    esi,0x0
   0x0000000000401174 <+128>:	jmp    0x401197 <phase_6+163>
   0x0000000000401176 <+130>:	mov    rdx,QWORD PTR [rdx+0x8]
   0x000000000040117a <+134>:	add    eax,0x1
   0x000000000040117d <+137>:	cmp    eax,ecx
   0x000000000040117f <+139>:	jne    0x401176 <phase_6+130>
   0x0000000000401181 <+141>:	jmp    0x401188 <phase_6+148>
   0x0000000000401183 <+143>:	mov    edx,0x6032d0
   0x0000000000401188 <+148>:	mov    QWORD PTR [rsp+rsi*2+0x20],rdx
   0x000000000040118d <+153>:	add    rsi,0x4
   0x0000000000401191 <+157>:	cmp    rsi,0x18
   0x0000000000401195 <+161>:	je     0x4011ab <phase_6+183>
   0x0000000000401197 <+163>:	mov    ecx,DWORD PTR [rsp+rsi*1]
   0x000000000040119a <+166>:	cmp    ecx,0x1
   0x000000000040119d <+169>:	jle    0x401183 <phase_6+143>
   0x000000000040119f <+171>:	mov    eax,0x1
   0x00000000004011a4 <+176>:	mov    edx,0x6032d0
   0x00000000004011a9 <+181>:	jmp    0x401176 <phase_6+130>
   0x00000000004011ab <+183>:	mov    rbx,QWORD PTR [rsp+0x20]
   0x00000000004011b0 <+188>:	lea    rax,[rsp+0x28]
   0x00000000004011b5 <+193>:	lea    rsi,[rsp+0x50]
   0x00000000004011ba <+198>:	mov    rcx,rbx
   0x00000000004011bd <+201>:	mov    rdx,QWORD PTR [rax]
   0x00000000004011c0 <+204>:	mov    QWORD PTR [rcx+0x8],rdx
   0x00000000004011c4 <+208>:	add    rax,0x8
   0x00000000004011c8 <+212>:	cmp    rax,rsi
   0x00000000004011cb <+215>:	je     0x4011d2 <phase_6+222>
   0x00000000004011cd <+217>:	mov    rcx,rdx
   0x00000000004011d0 <+220>:	jmp    0x4011bd <phase_6+201>
   0x00000000004011d2 <+222>:	mov    QWORD PTR [rdx+0x8],0x0
   0x00000000004011da <+230>:	mov    ebp,0x5
   0x00000000004011df <+235>:	mov    rax,QWORD PTR [rbx+0x8]
   0x00000000004011e3 <+239>:	mov    eax,DWORD PTR [rax]
   0x00000000004011e5 <+241>:	cmp    DWORD PTR [rbx],eax
   0x00000000004011e7 <+243>:	jge    0x4011ee <phase_6+250>
   0x00000000004011e9 <+245>:	call   0x40143a <explode_bomb>
   0x00000000004011ee <+250>:	mov    rbx,QWORD PTR [rbx+0x8]
   0x00000000004011f2 <+254>:	sub    ebp,0x1
   0x00000000004011f5 <+257>:	jne    0x4011df <phase_6+235>
   0x00000000004011f7 <+259>:	add    rsp,0x50
   0x00000000004011fb <+263>:	pop    rbx
   0x00000000004011fc <+264>:	pop    rbp
   0x00000000004011fd <+265>:	pop    r12
   0x00000000004011ff <+267>:	pop    r13
   0x0000000000401201 <+269>:	pop    r14
   0x0000000000401203 <+271>:	ret    
```

read_six_number:

```asm
=> 0x000000000040145c <+0>:	sub    rsp,0x18
   0x0000000000401460 <+4>:	mov    rdx,rsi
   0x0000000000401463 <+7>:	lea    rcx,[rsi+0x4]
   0x0000000000401467 <+11>:	lea    rax,[rsi+0x14]
   0x000000000040146b <+15>:	mov    QWORD PTR [rsp+0x8],rax
   0x0000000000401470 <+20>:	lea    rax,[rsi+0x10]
   0x0000000000401474 <+24>:	mov    QWORD PTR [rsp],rax
   0x0000000000401478 <+28>:	lea    r9,[rsi+0xc]
   0x000000000040147c <+32>:	lea    r8,[rsi+0x8]
   0x0000000000401480 <+36>:	mov    esi,0x4025c3
   0x0000000000401485 <+41>:	mov    eax,0x0
   0x000000000040148a <+46>:	call   0x400bf0 <__isoc99_sscanf@plt>
   0x000000000040148f <+51>:	cmp    eax,0x5
   0x0000000000401492 <+54>:	jg     0x401499 <read_six_numbers+61>
   0x0000000000401494 <+56>:	call   0x40143a <explode_bomb>
   0x0000000000401499 <+61>:	add    rsp,0x18
   0x000000000040149d <+65>:	ret    
```

输入6个数字：

![30.png](https://i.loli.net/2020/12/28/CgzSUxOI48loyZt.png)

输入的数字不可以超过6

![31.png](https://i.loli.net/2020/12/28/tCwzWqsMbkBfLcQ.png)

不可以输入相同的数字

![32.png](https://i.loli.net/2020/12/28/nRVdtHBeS6rbi5s.png)

7-输入的数值，替换我们

![33.png](https://i.loli.net/2020/12/28/ZvJdqMEUyWGI6gD.png)

我们转化的值，相当于链表的索引：

![34.png](https://i.loli.net/2020/12/28/7YFBCXPHgZu4Qbn.png)

```
0x6032d0 <node1>:	0x000000010000014c	
0x6032e0 <node2>:	0x00000002000000a8	
0x6032f0 <node3>:	0x000000030000039c	
0x603300 <node4>:	0x00000004000002b3	
0x603310 <node5>:	0x00000005000001dd	
0x603320 <node6>:	0x00000006000001bb
```

索引要从大到小开始排列：

![35.png](https://i.loli.net/2020/12/28/qI7xvV8RrF9Bcph.png)

即：

```
a8->2->5
14c->1->6
1bb->6->1
1dd->5->2
2b3->4->3
39c->3->4
//4 3 2 1 6 5
```

所以第六关是：

```
4 3 2 1 6 5
```

![36.png](https://i.loli.net/2020/12/28/hD5HkWeITswZLqy.png)

### secret_phase

![37.png](https://i.loli.net/2020/12/28/3FMPEB2hvSg5xyf.png)

在我们输入后判断是第六关后，会进行一个判定，判定了第四关的时候，输入0 0 后还有一个字符串的判定

![38.png](https://i.loli.net/2020/12/28/FYLuRD6gJjq8sQy.png)

进入隐藏关卡的办法：第四关的时候后面输入字符串->DrEvil

![39.png](https://i.loli.net/2020/12/28/MKCsI4rUSgQx2tE.png)

调试：

![40.png](https://i.loli.net/2020/12/28/eX8BkdCnPZxpIHJ.png)

```asm
=> 0x0000000000401242 <+0>:	push   rbx
   0x0000000000401243 <+1>:	call   0x40149e <read_line>
   0x0000000000401248 <+6>:	mov    edx,0xa
   0x000000000040124d <+11>:	mov    esi,0x0
   0x0000000000401252 <+16>:	mov    rdi,rax
   0x0000000000401255 <+19>:	call   0x400bd0 <strtol@plt>
   0x000000000040125a <+24>:	mov    rbx,rax
   0x000000000040125d <+27>:	lea    eax,[rax-0x1]
   0x0000000000401260 <+30>:	cmp    eax,0x3e8
   0x0000000000401265 <+35>:	jbe    0x40126c <secret_phase+42>
   0x0000000000401267 <+37>:	call   0x40143a <explode_bomb>
   0x000000000040126c <+42>:	mov    esi,ebx
   0x000000000040126e <+44>:	mov    edi,0x6030f0
   0x0000000000401273 <+49>:	call   0x401204 <fun7>
   0x0000000000401278 <+54>:	cmp    eax,0x2
   0x000000000040127b <+57>:	je     0x401282 <secret_phase+64>
   0x000000000040127d <+59>:	call   0x40143a <explode_bomb>
   0x0000000000401282 <+64>:	mov    edi,0x402438
   0x0000000000401287 <+69>:	call   0x400b10 <puts@plt>
   0x000000000040128c <+74>:	call   0x4015c4 <phase_defused>
   0x0000000000401291 <+79>:	pop    rbx
   0x0000000000401292 <+80>:	ret 
```

Fun7:

```asm
   0x0000000000401204 <+0>:	sub    rsp,0x8
   0x0000000000401208 <+4>:	test   rdi,rdi
   0x000000000040120b <+7>:	je     0x401238 <fun7+52>
   0x000000000040120d <+9>:	mov    edx,DWORD PTR [rdi]
   0x000000000040120f <+11>:	cmp    edx,esi
   0x0000000000401211 <+13>:	jle    0x401220 <fun7+28>
   0x0000000000401213 <+15>:	mov    rdi,QWORD PTR [rdi+0x8]
   0x0000000000401217 <+19>:	call   0x401204 <fun7>
   0x000000000040121c <+24>:	add    eax,eax
   0x000000000040121e <+26>:	jmp    0x40123d <fun7+57>
   0x0000000000401220 <+28>:	mov    eax,0x0
   0x0000000000401225 <+33>:	cmp    edx,esi
   0x0000000000401227 <+35>:	je     0x40123d <fun7+57>
   0x0000000000401229 <+37>:	mov    rdi,QWORD PTR [rdi+0x10]
   0x000000000040122d <+41>:	call   0x401204 <fun7>
   0x0000000000401232 <+46>:	lea    eax,[rax+rax*1+0x1]
   0x0000000000401236 <+50>:	jmp    0x40123d <fun7+57>
   0x0000000000401238 <+52>:	mov    eax,0xffffffff
   0x000000000040123d <+57>:	add    rsp,0x8
   0x0000000000401241 <+61>:	ret  
```

输入的参数不可以大于999，且fun7返回的值等于2

```c
signed __int64 __fastcall fun7(__int64 a1, int a2)
{
  signed __int64 result;

  if ( !a1 )
    return 0xFFFFFFFFLL;
  if ( *(_DWORD *)a1 > a2 )
    return 2 * (unsigned int)fun7(*(_QWORD *)(a1 + 8));
  result = 0LL;
  if ( *(_DWORD *)a1 != a2 )
    result = 2 * (unsigned int)fun7(*(_QWORD *)(a1 + 16)) + 1;
  return result;
```

看一下a1二叉树的结构：

![41.png](https://i.loli.net/2020/12/28/aGJPuleUIwYCL4p.png)

```
0x24--0x8--0x6--0x1
|			|			|
|			|			0x7
|     0x16--0x14 
|			|
|			0x23
0x32--0x2d--0x28
|			|
|			0x2f
0x6b--0x63
|
0x3e9
```

所以满足的只有0x16与0x14


所以第七关是：

```
20/22
```

![42.png](https://i.loli.net/2020/12/28/q6E3ae5tJvSOxzl.png)

