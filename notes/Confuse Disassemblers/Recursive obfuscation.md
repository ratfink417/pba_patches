# Hiding Code From Recursive Disassemblers
This is gonna be much easier to do in C than [Linear obfuscation](Linear%20obfuscation.md) was to demonstrate. All that really needs to be done is we need to make some indirect function calls which are done pretty easily using **function pointers**

```c
#include <stdio.h>
int func_one(){
        return 1;
}

int func_two(){
        return 2;
}

int func_three(){
        return 3;
}

int main(){
        int(*func_arr[])() = {func_one,func_two,func_three};
        int opt;
        printf("Supply an integer from 0 to 2\n");
        scanf("%d",&opt);

        printf("printing from pointer referenced function: %d\n",(*func_arr[opt])());

        return 0;
}
```

First we declare some functions at the top

Then we make an array of pointers to those same functions 
```c
int(*func_arr[])() = {func_one,func_two,func_three};
```

Next all we need to do is call one of those function, in this example based on user input 
```c
printf("printing from pointer referenced function: %d\n",(*func_arr[opt])());
```
In [Radare2](Radare2.md) the main function of this program ends up looking like this.
![](Pasted%20image%2020210918150008.png)

The things to pay attention to here are
1: The part where we construct the function pointer array
```output
            0x0040067e    48c745e0460. mov qword [rbp-0x20], sym.func_one
            0x00400686    48c745e8510. mov qword [rbp-0x18], sym.func_two
            0x0040068e    48c745f05c0. mov qword [rbp-0x10], sym.func_three
```
2: The part where a function is opaquely selected and called
```output
|              sym.imp.__isoc99_scanf()
|           0x004006b6    8b45dc       mov eax, [rbp-0x24]
|           0x004006b9    4898         cdqe
|           0x004006bb    488b54c5e0   mov rdx, [rbp+rax*8-0x20]
|           0x004006c0    b800000000   mov eax, 0x0
|           0x004006c5    ffd2         call rdx ;[3]
```
After that _scanf()_ function gets a number from you, which will be used in selecting a function from the array, your registers and stack look like this.

```output
0x7ffd8f225620  0x00000000 0x00000000 0x00000000 0x00000002  ................
0x7ffd8f225630  0x00400646 0x00000000 0x00400651 0x00000000  F.@.....Q.@.....
0x7ffd8f225640  0x0040065c 0x00000000 0xc3498800 0xbf869ef5  \.@.......I.....
0x7ffd8f225650  0x00400700 0x00000000 0xe0110830 0x00007fe3  ..@.....0.......
 r15 0x00000000         r14 0x00000000         r13 0x7ffd8f225730
 r12 0x00400550         rbp 0x7ffd8f225650     rbx 0x00000000
 r11 0x7fe3e02675e0     r10 0x00000000          r9 0x00000000
  r8 0x00000000         rax 0x00000001         rcx 0x00000010
 rdx 0x7fe3e04b6790     rsi 0x00000001         rdi 0x7ffd8f225100
```
your 3 function addresses from the array are on the stack

0x00400646 _func\_one_
0x00400651 _func\_two_
0x0040065c _func\_three_

your input from the _scanf()_ call is on the stack. I put '2' this time.
```0x7ffd8f225620  0x00000000 0x00000000 0x00000000 0x00000002```

The code uses stack address relative to the address held in the **rbp** register. 

What the code is doing is 
- moving the value at ```rbp-0x24``` _0x00000002_ in my case int ```eax```
- executing ```cdqe``` to extend the ```eax``` value into the full ```rax``` register. this is just so it can be referenced in terms of a 64 bit int.
- Then moving the data in ```rbx+rax*8-0x20``` into ```rdx```
  
  There's a little bit to be unpacked here so... ```rbx``` because that's the base address of the stack frame right now and address are relative to it.
  
  ```+rax*8-0x20``` because 0x20 _16_ bytes up from ```rbp``` is the beginning address of the array we declared. while ```rax``` is holding the index into the array and we multiply that by 8 because it's one byte per element in this type of array. 
  
  This corresponds very closely with
  ```c
  (*func_arr[2])();
  ```
  
- Finally, ```call rdx``` is executed which radare knows nothing about at least when analyze the binary statically or before we dynamically follow the call statement.