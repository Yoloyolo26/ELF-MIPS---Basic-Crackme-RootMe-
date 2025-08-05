# ELF-MIPS---Basic-Crackme-RootMe-
i finished a Cracking challenge on RootMe ,and there are only two solutions on RootMe ,i tried to google for the imformation of this challenge but found nothing,so i make this simple writeup to try my best to explian it!!!if i said something wrong pls tell me ,thanks guys !!

## enviroment and tool
-__wsl__  
-__IDA__

## download the cracking file 
`weget` https://static.root-me.org/cracking/ch27/ch27.bin

![image](https://github.com/Yoloyolo26/ELF-MIPS---Basic-Crackme-RootMe-/blob/main/images/2025-08-04%20220709.png)

## check the format of the cracking file 
`flie` ch27.bin  
![image](https://github.com/Yoloyolo26/ELF-MIPS---Basic-Crackme-RootMe-/blob/main/images/2025-08-05%20120234.png?raw=true)  

we can see it an ELF file with MIPS architecture

## working on IDA  
### overview the all functions  
![image](https://github.com/Yoloyolo26/ELF-MIPS---Basic-Crackme-RootMe-/blob/main/images/main%202025-08-05%20121200.png)   

### press shift + F12 to find the strings 
we find something intersting it may hide some passwd or the flag there, step into that "__well done__" string  
![image](https://github.com/Yoloyolo26/ELF-MIPS---Basic-Crackme-RootMe-/blob/main/images/strings%202025-08-05%20121525.png)   

### analyise the file  
![image](https://github.com/Yoloyolo26/ELF-MIPS---Basic-Crackme-RootMe-/blob/main/images/func%202025-08-05%20122103.png)   
we noticed that the it an Cross References (or simply XREFs)  

moving on the __sub_4007C0__ and __sub_400814__  
![image](https://github.com/Yoloyolo26/ELF-MIPS---Basic-Crackme-RootMe-/blob/main/images/func1%202025-08-05%20123029.png)   
![image](https://github.com/Yoloyolo26/ELF-MIPS---Basic-Crackme-RootMe-/blob/main/images/fun2%202025-08-05%20123045.png)   

it seem just a print func ,we need using xref to find the calling func , target that right message print func ，select the  __sub_4007C0__ and press __x__  
![image](https://github.com/Yoloyolo26/ELF-MIPS---Basic-Crackme-RootMe-/blob/main/images/calling%202025-08-05%20123951.png)  
it only one derection!!!  step in it !

### check the graph overview
only one area uisng __jal__ to jump to this address   
So we look back along this area  
at there you can find many structures like this  
![image](https://github.com/Yoloyolo26/ELF-MIPS---Basic-Crackme-RootMe-/blob/main/images/structure%202025-08-05%20130711.png)   

by reading the assemble code we can know that is an strings check func to see the enter passwd is right or not  
we write down all the charaters and the stack offset  
![image](https://github.com/Yoloyolo26/ELF-MIPS---Basic-Crackme-RootMe-/blob/main/images/var%202025-08-05%20155933.png)   

there are two special area in this main func   
__$__ _one is a loop that will load the serval times __i__ into memory_  
  but many times does it load ? let us check the assembly code  
  
![image](https://github.com/Yoloyolo26/ELF-MIPS---Basic-Crackme-RootMe-/blob/main/images/special1r%202025-08-05%20161200.png) 

the __var_50__ was initialized as __-0x50__ on the top of main func, and the init i is -0x68 plus 0x50 whcih is 8, and i ++ in each loop the jupm out condition `slti    $v0, 0x11` equals __< 17__     
so the whole assemebly loop code above in c++ is like   
`for (int i = 8 , i < 17 ,i ++)`  
it will load __i__ 9 times  into memeroy   
`access address = ($fp - 0x4C) + i = input_start + i`  
so the stack offset of i is start from __0x44__ to __0x3c__   
taking a note of this and see next one   

__$__ _the another area is making sure that the values of __var_48 (R)__  and __var_47__ differ by three , so the value of the __var_47__ is __(u)   
![image](https://github.com/Yoloyolo26/ELF-MIPS---Basic-Crackme-RootMe-/blob/main/images/another%202025-08-05%20163641.png)   

now we got all the charaters of that "possible passwd" ，We sort by offset from largest to smallest 
![image](https://github.com/Yoloyolo26/ELF-MIPS---Basic-Crackme-RootMe-/blob/main/images/flag%202025-08-05%20164528.png)   

there we have itt!!!!that the final flagg!!!!

### the finnal falg is __cantnotrunmiiiiiiiiips__





