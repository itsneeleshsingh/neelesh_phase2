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
