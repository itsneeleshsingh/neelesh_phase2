# 1. GDB baby step 1
This challenge ask us to analyze a binary program and determine the value of the eax register at the end of the main function. To do this we need to disassemble the program and locate the main function to identify what value is stored in eax.

## Solution:
1. First, I understood that to get the flag, we have to find the value of the eax register at the end of the main function. This value would be the flag in decimal form.
2. I searched for resources and watched a tutorial explaining that GDB can be used for debugging and disassembly. So I installed GDB using  
`sudo apt install gdb`.
3. After installing GDB, I tried to run the binary with `gdb debugger0_a`. However, I encountered a permission denied error because the binary had no execute permissions.
    ```
    ┌──(neels㉿neel)-[~/PicoCTF/GDB Baby 1]
    └─$ gdb debugger0_a 
    GNU gdb (Debian 16.3-5) 16.3
    Copyright (C) 2024 Free Software Foundation, Inc.
    License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
    This is free software: you are free to change and redistribute it.
    There is NO WARRANTY, to the extent permitted by law.
    Type "show copying" and "show warranty" for details.
    This GDB was configured as "x86_64-linux-gnu".
    Type "show configuration" for configuration details.
    For bug reporting instructions, please see:
    <https://www.gnu.org/software/gdb/bugs/>.
    Find the GDB manual and other documentation resources online at:
        <http://www.gnu.org/software/gdb/documentation/>.

    For help, type "help".
    Type "apropos word" to search for commands related to "word"...
    Reading symbols from debugger0_a...
    (No debugging symbols found in debugger0_a)
    (gdb) run
    Starting program: /home/neels/PicoCTF/GDB Baby 1/debugger0_a 
    zsh:1: permission denied: /home/neels/PicoCTF/GDB Baby 1/debugger0_a
    During startup program exited with code 126.
    (gdb)
    ```
4. So I tried to directly see the functions in the program using the command `info functions`.
    ```
    (gdb) info functions
    All defined functions:

    Non-debugging symbols:
    0x0000000000001000  _init
    0x0000000000001030  __cxa_finalize@plt
    0x0000000000001040  _start
    0x0000000000001070  deregister_tm_clones
    0x00000000000010a0  register_tm_clones
    0x00000000000010e0  __do_global_dtors_aux
    0x0000000000001120  frame_dummy
    0x0000000000001129  main
    0x0000000000001140  __libc_csu_init
    0x00000000000011b0  __libc_csu_fini
    0x00000000000011b8  _fini
    ```
    <img width="1920" height="1080" alt="image (2)" src="https://github.com/user-attachments/assets/80b35d93-acaf-4493-ac5a-7a9f2eab1f1e" />

5. Here I found that the main function was there, so to disassemble that first we have to convert into intel format as it is more understandable for me and also the video that i saw used that.
    ```
    (gdb) set disassembly-flavor intel
    ```
6. Now to disaasssemble main function I used command - 
    ```
    (gdb) disassemble main
    Dump of assembler code for function main:
    0x0000000000001129 <+0>:     endbr64
    0x000000000000112d <+4>:     push   rbp
    0x000000000000112e <+5>:     mov    rbp,rsp
    0x0000000000001131 <+8>:     mov    DWORD PTR [rbp-0x4],edi
    0x0000000000001134 <+11>:    mov    QWORD PTR [rbp-0x10],rsi
    0x0000000000001138 <+15>:    mov    eax,0x86342
    0x000000000000113d <+20>:    pop    rbp
    0x000000000000113e <+21>:    ret
    End of assembler dump.
    ```
7. Here the move function was assigning `0x86342` value to our eax register and that is our flag. Now we have to convert this hex into decimal so i used python for it.
8. To convert `0x86342` into decimal, I opened another terminal and launched Python with `python3`.
    ```
    ┌──(neels㉿neel)-[~/PicoCTF/GDB Baby 1]
    └─$ python3
    Python 3.13.3 (main, Apr 10 2025, 21:38:51) [GCC 14.2.0] on linux
    Type "help", "copyright", "credits" or "license" for more information.
    ```
9. In Python, I ran `int(0x86342)` which returned `549698`.
    ```
    >>>int(0x86342)
    549698
    ```
10. Therefore, the flag is `picoCTF{549698}`.

## Flag:
```
picoCTF{549698}
```

## Concepts learnt:
- How to install and use GDB for debugging binaries.
- How to list functions within a binary using `info functions`.
- How to set the disassembly flavor to Intel syntax with `set disassembly-flavor intel`.
- How to disassemble a specific function using `disassemble main`.(I got to know that we can also provide the location of the main function as parameter)
- Understanding of assembly instructions, especially how values are moved into registers.
- Converting hexadecimal numbers to decimal using Python.

## Notes:
- Initially faced some error in my WSL linux in windows while installing some things. So i tried installing kali linux using virtual box and it worked. Now onwards will use that only.
- Also i tried again to run the program by checking its permissions and then giving it and then re running using gdb.
    ```
    ┌──(neels㉿neel)-[~/PicoCTF/GDB Baby 1]
    └─$ ls debugger0_a -l
    -rw-rw-r-- 1 neels neels 16472 Oct 25 18:13 debugger0_a

    ┌──(neels㉿neel)-[~/PicoCTF/GDB Baby 1]
    └─$ chmod +x debugger0_a

    ┌──(neels㉿neel)-[~/PicoCTF/GDB Baby 1]
    └─$ gdb debugger0_a
    (gdb) run
    Starting program: /home/neels/PicoCTF/GDB Baby 1/debugger0_a
    [Thread debugging using libthread_db enabled]
    Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
    [Inferior 1 (process 33674) exited with code 0102]
    ```

## Resources:
- [GDB Tutorial for Beginners](https://www.youtube.com/watch?v=5Rdrzlhbehw&list=PL-DxAN1jsRa9151ezNuCbh7UkGS0bMPdw&index=11)
- [Install GDB](https://www.kali.org/tools/gdb/)







# 3. Vault door 3
The challenge involves a Java code that manipulates a password string through multiple loops and index operations. Our goal is to get the original password that when processed by the code results in a specific given string.

## Solution:
1. I started by reviewing the Java code to understand how the password is processed. It takes an input string and then performs multiple operations with for loops to rearrange characters into a buffer array, which is then compared to given known string.  
2. The code checks that the password length is exactly 32 characters. It then copies the first 8 characters directly into the buffer.
3. Next it fills the buffer from index 8 to 15 with characters from the input in reverse order.
4. Then for the index from 16 to 31, it assigns characters from the password based on `buffer[i] = password.charAt(46 - i)`and stepping by 2.  
5. The final loop fills the buffer from index 17 to 31 and decreasing by 2, with characters from the password at the same index.
6. The string is then compared with   - 
`jU5t_a_sna_3lpm18gb41_u_4_mfr340`.  
7. Thus to find the original password, I needed to reverse each of these steps and so I wrote a Python script to invert the process.
```python
buffer = list("jU5t_a_sna_3lpm18gb41_u_4_mfr340")
password = [None] * 32

for i in range(0, 8):
    password[i] = buffer[i]

for i in range(8, 16):
    password[23 - i] = buffer[i]

for i in range(16, 32, 2):
    password[46 - i] = buffer[i]

for i in range(31, 16, -2):
    password[i] = buffer[i]

print("picoCTF{" + ''.join(password) + "}")

```
8. The Python code takes the target string then reconstructs the original password by reversing each loop operation.
9. Running the script provided the original password which is the flag(I formatted it at last by adding picoCTF and brackets).
<img width="1919" height="1079" alt="image (1)" src="https://github.com/user-attachments/assets/d4c6fded-a7fa-4e65-bee3-1a632468ae9a" />


## Flag:
```
picoCTF{jU5t_a_s1mpl3_an4gr4m_4_u_1fb380}
```

## Concepts learnt:
- Understanding Java string manipulation through loops and indexing.
- Reversing code using python.  
- Converting a list of characters into a string with `''.join()`.(I searched it on google as i dont know exactly the syntax)

## Notes:
- Initially, I misunderstood the range boundaries in Python, which caused errors in the reversing the code. In java => `i=31; i>=17; i-=2` it includes the last element 17, but in python the last number is not included in normal range.  
`for i in range(31, 16, -2):`  
so instead of this i wrote -   
`for i in range(31, 17, -2):`

## Resources:
- Google for some syntax of python like .join() function.

- Also used [Python - Initialize empty array of given length](https://www.geeksforgeeks.org/python/python-initialize-empty-array-of-given-length/) to know how we initialise empty array in python.(I know in C and C++ but not in python)

