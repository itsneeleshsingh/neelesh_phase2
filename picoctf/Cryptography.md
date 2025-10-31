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