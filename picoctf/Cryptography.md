# 1. RSA oracle
This challenge involves exploiting an RSA encryption system that allows encryption and decryption requests through an oracle. The attacker intercepts ciphertexts and can send messages to be encrypted or decrypted. The main goal is to use multiplicative property of RSA to get flag.

## Solution:
1. So I was given with two files - `password.enc` and  `secret.enc`. I cat both of them to find something but could understand any.
    ```bash
    ┌──(neels㉿neel)-[~/PicoCTF/RsaOracle]
    └─$ cat password.enc

    873224563026311790736191809393138825971072101706285228102516279725246082824238887755080848591049817640245481028953722926586046994669540835757705139131212

    ┌──(neels㉿neel)-[~/PicoCTF/RsaOracle]
    └─$ cat secret.enc

    Salted__W6+N7���$+����zP=��5I�A]        ����
    �������D�KaϬa+A�
    ```
    Then I started by connecting to the provided server via netcat to interact with the RSA oracle, which allowed me to encrypt and decrypt messages. It does not allow direct password decrypt.
2. So I watched a video for how rsa works and after that I got to know how it works `(m^e)^d ~= m (mod n)`.
3. I found a resource(CFT101) where it was written that it holds multiplicative property that is - if  
`(c1 = m1^e mod n) and (c2 = m2^e mod n), then (c1 * c2 mod n = (m1*m2)^e mod n)`. So my roadmap was to first take a small value to encrypt. Mutiply its ciphertext with the password cyphertext and then use the decrypted hex value and divide it to get the original password text.
4.  I chose a value — '2' and ASCII code for '2' is 0x32 which in decimal = 50. So I entered 2 to encrypt using the netcat.
    ```bash
    ┌──(neels㉿neel)-[~/PicoCTF/RsaOracle]
    └─$ nc titan.picoctf.net 64950
    *****************************************
    ****************THE ORACLE***************
    *****************************************
    what should we do for you? 
    E --> encrypt D --> decrypt. 
    E
    enter text to encrypt (encoded length must be less than keysize): 2
    2

    encoded cleartext as Hex m: 32

    ciphertext (m ^ e mod n) 4707619883686427763240856106433203231481313994680729548861877810439954027216515481620077982254465432294427487895036699854948548980054737181231034760249505
    ```
5. I multiplied this ciphertext with the password ciphertext, creating a new ciphertext that corresponds to the product of the plaintexts ('2' * password). 
    ```python
    ┌──(neels㉿neel)-[~/PicoCTF/RsaOracle]
    └─$ python3
    Python 3.13.3 (main, Apr 10 2025, 21:38:51) [GCC 14.2.0] on linux
    Type "help", "copyright", "credits" or "license" for more information.

    >>> c2=4707619883686427763240856106433203231481313994680729548861877810439954027216515481620077982254465432294\
    427487895036699854948548980054737181231034760249505
    >>> cpass=8732245630263117907361918093931388259710721017062852281025162797252460828242388877550808485910498176\
    40245481028953722926586046994669540835757705139131212
    >>> result=c2*cpass
    >>> print(result)
    4110809315826057621832156950548807503751813121602244310508738128584060227812148208929544202582552883315994347832480097453797778820600470059671621681637421791653806771214975829201888584805263611095541715847944793370986898821554147684313674343226871927458131439487360221580528308813519945189044393668053050060
    ``` 
6. I sent this combined ciphertext to the oracle for decryption. The oracle returned a plaintext which was the product of '2' and the password in hex form and got `b2bd79263f6`.  
    ```bash
    ┌──(neels㉿neel)-[~/PicoCTF/RsaOracle]
    └─$ nc titan.picoctf.net 64950
    *****************************************
    ****************THE ORACLE***************
    *****************************************
    what should we do for you? 
    E --> encrypt D --> decrypt. 
    D
    Enter text to decrypt: 4110809315826057621832156950548807503751813121602244310508738128584060227812148208929544202582552883315994347832480097453797778820600470059671621681637421791653806771214975829201888584805263611095541715847944793370986898821554147684313674343226871927458131439487360221580528308813519945189044393668053050060
    decrypted ciphertext as hex (c ^ d mod n): b2bd79263f6
    decrypted ciphertext: 
                        +×cö

    ```
7. Converting this hex value to decimal, I obtained `12282928194550`. Since the original plaintext was '2', I divided this number by 2 to find the password in decimal form which is 50, which gave `245658563891`.  
8. I converted this number to hex: `3932643533`, then decoded it from hex to ASCII(searched google how to do that or I can also use a online convertor but I wanted to do it with CLI), which resulted in `92d53`.
    ```python
    >>> int(0xb2bd79263f6)
    12282928194550
    >>> d=12282928194550//50
    >>> print(d)
    245658563891
    >>> hex(245658563891)
    '0x3932643533'
    >>> hex="3932643533"
    >>> object=bytes.fromhex(hex)
    >>> string=object.decode('ascii')
    >>> print(string)
    92d53
    ```
    <img width="693" height="466" alt="image (9)" src="https://github.com/user-attachments/assets/367fe12d-0237-4ba6-af7f-d33daa5b533d" />

9. Using `92d53` as the password, I used `openssl` to decrypt the `secret.enc` file with the command(as it was mentioned in hint 2):  
    ```
    openssl enc -aes-256-cbc -d -in secret.enc -k 92d53
    ```  
10. This process revealed the flag.
    ```bash
    ┌──(neels㉿neel)-[~/PicoCTF/RsaOracle]
    └─$ openssl enc -aes-256-cbc -d -in secret.enc -k 92d53
    *** WARNING : deprecated key derivation used.
    Using -iter or -pbkdf2 would be better.
    picoCTF{su((3ss_(r@ck1ng_r3@_92d53250}
    ```

## Flag:
```
picoCTF{su((3ss_(r@ck1ng_r3@_92d53250}
```

## Concepts learnt:
- RSA encryption and decryption process including the mathematical properties.  
- The multiplicative property of RSA: `(c1 * c2) mod n = (m1 * m2)^e mod n`.  
- Converting between hex, decimal, and ASCII representations of data.  
- Using Python for large integer calculations and data decoding.  
- Using `openssl` for decrypting files with a password.  

## Notes:
- Initially, I tried to decrypt directly with the password but realized I needed to use the multiplicative property of RSA for this attack.  
- I divided by 2 which was wrong as it was '2' and not decimal value 2.

## Resources:
- [RSA Explained - Cryptography Course](https://ctf101.org/cryptography/what-is-rsa/)  
- [YouTube Video on RSA Attacks](https://www.youtube.com/watch?v=Pq8gNbvfaoM)






# 2. Custom encryption
This challenge involves analyzing a given code file and an encrypted file to understand the encryption process used. The goal is to reverse engineer the encoding steps and create a decoding function that can decrypt the contents of the encrypted file.

## Solution:
1. First, I received two files- a Python script and the encrypted data. The script contained functions that performed encryption, so I started by examining the code line by line to understand the process.
2. I identified two main functions: `dynamic_xor_encrypt()` and `encrypt()`. The first function reverses the message and then applies XOR, while the second transforms characters by multiplying ASCII values with a key and 311.
3. To decrypt the data I needed to reverse these steps. I observed that the `encrypt()` function multiplies ASCII values by `key * 311`. To undo this I need to divide the encrypted values by `key * 311`.
4. To find the key, I used the provided parameters `a`, `b`, `p`, `g` and using python wrote a normal program with same functions(Also we have to use the values given in enc_flag):
   ```python
   a = 90
   b = 26
   p = 97
   g = 31
   def generator(g, x, p):
       return pow(g, x, p)
   u = generator(g, a, p)
   v = generator(g, b, p)
   key = generator(v, a, p)
   print(key)
   ```
   The output was `22`.
   <img width="1920" height="1080" alt="image (10)" src="https://github.com/user-attachments/assets/9a34dcd6-07dc-4a03-b2e4-4b55b5f0e222" />

6. Next I copied the list of the cipher values. For each value I divided by `22 * 311` to obtain the ASCII code, then converted it to characters and joined them to get the original string.
7. After reconstructing the string, I remembered that the `dynamic_xor_encrypt()` function reversed the plaintext. So I XORed each character with the key `trudeau` in a repeating pattern, then reversed the string to get the original message.
    ```python
    cipher=[61578, 109472, 437888, 6842, 0, 20526, 129998, 526834, 478940, 287364, 0, 567886, 143682, 34210, 465256, 0, 150524, 588412, 6842, 424204, 164208, 184734, 41052, 41052, 116314, 41052, 177892, 348942, 218944, 335258, 177892, 47894, 82104, 116314]
    updated_cipher=[]
    for i in cipher:
    updated_cipher.append(chr(int(i/(22*311))))
    #print(updated_cipher)
    plain_text = ''.join(updated_cipher)
    print(plain_text)
    text_key="trudeau"
    deciphered = ""
    key_length = len(text_key)
    for i,char in enumerate(plain_text):
    key_char = text_key[i % key_length]
    deciphered += chr(ord(char) ^ ord(key_char))
    print(deciphered[::-1])
    ```
8. The final output was the flag.
    ```bash
    ┌──(neels㉿neel)-[~]
    └─$ /usr/bin/python /home/neels/PicoCTF/Customencryption/dec.py
    @MF*SDV>3 1

    picoCTF{custom_d2cr0pt6d_49fbee5b}
    ```
    <img width="1920" height="1080" alt="image (11)" src="https://github.com/user-attachments/assets/31689278-835e-451d-be25-39f6924a1963" />

   
## Flag:
```
picoCTF{custom_d2cr0pt6d_49fbee5b}
```

## Concepts learnt:
- XOR operation properties and how XOR is reversible.
- Reversing string transformations to decode encrypted messages.
- Using Python to automate decryption steps and reverse engineer encryption processes.

## Notes:
- Initially, I did not understand why the values of a and b were given. Eventually I understood it.
- I was also not able to first get how to reverse that `dynamic_xor_encrypt()` function. But it striked immediately about the reverse property of xor so when decrypting nothing has to be changed there.
- Some syntax errors were there while coding which I corrected using some basic coding fundamentals.

## Resources:
- [GeeksforGeeks: enumerate in Python](https://www.geeksforgeeks.org/python/enumerate-in-python/)
- [XOR Video](https://www.youtube.com/watch?v=h7Cgx-pn9bw)










# 3. miniRSA
This challenge involves decrypting a message encrypted with RSA where the public exponent e is very small. The ciphertext provided is a large number, and the goal is to recover the original plaintext message that contains the flag.

## Solution:
1. So first we were given with all the values of N, e and c and we have to find the actual m - message. I knew that in RSA encryption, the ciphertext `cipher = (message^e) mod n`. Since e is 3, the encryption simplifies to `cipher = message^3` when the message is smaller than n, which is often the case with small e values. 
2. But if the message itself is a larger, then:
    ```
    message = (cipher + k*n)^(1/3)
    where k = 0,1,2,3,4,5 and so on..
    ```
3. Now I focused on what I already had and what I needed to find. I had cipher, n, and I knew e = 3. So the remaining part was just the calculation, which I decided to do using a Python script.  
I then searched on net and found that -> `e=3` is vulnerable and easy to crack using `eth root` in the wikipedia page of the hint provided.
    <img width="1919" height="993" alt="image (12)" src="https://github.com/user-attachments/assets/51edfeca-1074-428c-864c-bca7611f3864" />

4. The first step was to take the cube root of my ciphered content (which is a very big number). So I started to find how to do that and came upon a python library - `gmpy2`, which provides fast and precise mathematical functions like `iroot` for integer roots.
5. I created a virtual environment, activated it and installed `gmpy2`.
    ```bash
    ┌──(neels㉿neel)-[~/PicoCTF/miniRSA]
    └─$ python3 -m venv ./venv

    ┌──(neels㉿neel)-[~/PicoCTF/miniRSA]
    └─$ source venv/bin/activate

    ┌──(venv)─(neels㉿neel)-[~/PicoCTF/miniRSA]
    └─$ pip install gmpy2
    ```
6. In code I imported it and copied all the values of  n,e and c from the file and started writing from 1 to range 5000(doont know but taken it approx) for loop.  
Then I applied the `iroot` function in `gmpy2` and if root found then storing that binary number in `messg`.  
Then converted that messg to hex and to ascii form to get the flag.
    ```python
    import gmpy2

    n=29331922499794985782735976045591164936683059380558950386560160105740343201513369939006307531165922708949619162698623675349030430859547825708994708321803705309459438099340427770580064400911431856656901982789948285309956111848686906152664473350940486507451771223435835260168971210087470894448460745593956840586530527915802541450092946574694809584880896601317519794442862977471129319781313161842056501715040555964011899589002863730868679527184420789010551475067862907739054966183120621407246398518098981106431219207697870293412176440482900183550467375190239898455201170831410460483829448603477361305838743852756938687673
    e=3
    c=2205316413931134031074603746928247799030155221252519872650073010782049179856976080512716237308882294226369300412719995904064931819531456392957957122459640736424089744772221933500860936331459280832211445548332429338572369823704784625368933

    for k in range(5000):
        messg,root = gmpy2.iroot(c+k*n,e)
        if root:
            temp=hex(messg)
            print(temp)
            ascii_text = bytes.fromhex(temp[2:]).decode('ascii')
            print(ascii_text)
    ```
7. I ran the script and the flag was shown.
    ```bash
    ┌──(venv)─(neels㉿neel)-[~/PicoCTF/miniRSA]
    └─$ /usr/bin/python /home/neels/PicoCTF/miniRSA/decode.py
    0x7069636f4354467b6e3333645f615f6c41726733725f655f63636161373737367d
    picoCTF{n33d_a_lArg3r_e_ccaa7776}
    ```
    <img width="1920" height="1080" alt="image (13)" src="https://github.com/user-attachments/assets/a5ef3b22-a662-42ac-9154-db0d08425a7e" />


## Flag:
```
picoCTF{n33d_a_lArg3r_e_ccaa7776}
```

## Concepts learnt:
- RSA encryption and decryption basics, especially for small exponents.
- Vulnerability of RSA with small e values, specifically e=3.
- How to find the integer root of a large number using `gmpy2.iroot`.
- Converting hexadecimal strings to ASCII for message decoding.

## Notes:
- Initially, to find root i found a method with binary search approach to find the root but gmpy2 was easy and fast.
- I also experimented with different ranges for `k` and found that 0 to 5000 was sufficient in this case. 

## Resources:
- [gmpy2 library](https://pypi.org/project/gmpy2/)

- [RSA cryptosystem Wikipedia](https://en.wikipedia.org/wiki/RSA_cryptosystem)
