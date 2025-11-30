# 1. Hide and seek
This challenge wants us to find hidden data within an image file. We have to use steganography techniques to find the secret information embedded in the image. 

## Solution:
1. I read the challenge description and recognized that it is related to steganography, particularly with the mention of "stegseek" in the givem description file in drive.
2. I started searching "stegseek" and discovered that it may need installation but is usually preinstalled in Kali Linux(which i am using).
3. To use it, I needed the syntax, which is:  
`stegseek [stegofile.jpg] [wordlist.txt]`(mentioned in official stegseek repository).
4. Next, I realized I required a wordlist for the cracking process. After some here and there searching, I found that the "rockyou" wordlist is commonly used and already available on Kali Linux in a specific path.
5. I navigated to the default location of wordlists: 
   ```bash
    ┌──(neels㉿neel)-[~/CustomQuesCryptonite/Forensics/HideandSeek]
    └─$ /usr/share/wordlists

    ┌──(neels㉿neel)-[/usr/share/wordlists]
    └─$ ls
    amass  dirbuster   fasttrack.txt  john.lst  metasploit  rockyou.txt.gz  wfuzz
    dirb   dnsmap.txt  fern-wifi      legion    nmap.lst    sqlmap.txt      wifite.txt
   ```
6. I extracted the zipped "rockyou" file using the command:
   ```bash
    ┌──(neels㉿neel)-[~/CustomQuesCryptonite/Forensics/HideandSeek]
    └─$ sudo gzip -d /usr/share/wordlists/rockyou.txt.gz
    [sudo] password for neels:
   ```
7. After extracting the file, I used stegseek with the command:
   ```bash
    ┌──(neels㉿neel)-[~/CustomQuesCryptonite/Forensics/HideandSeek]
    └─$ stegseek sakamoto.jpg /usr/share/wordlists/rockyou.txt
   ```
8. The output from stegseek indicated that it found a passphrase: "iloveyou1" and provided the original filename as "flag.txt".
9. It also stated that the extracted content is stored in a new file named "sakamoto.jpg.out".
10. I checked the newly created file, and it contained the flag.
![Hide and Seek](images/HideandSeek1.png)
## Flag:
```
nite{h1d3_4nd_s33k_but_w1th_st3g_sdfu9s8}
```

## Concepts learnt:
- The significance of steganography and how it can be used to hide data within images.
- The utility of the stegseek tool for extracting hidden data using a wordlist.
- Using wordlist (like rockyou) in cracking password-protected steganographic files.

## Notes:
- Initially, I attempted to use binwalk on the image file, but it showed only 1 image.
- I mistakenly tried running stegseek on the zipped "rockyou" file, which resulted in an error indicating that it couldn't find a valid passphrase.
    ```bash
    ┌──(neels㉿neel)-[~/CustomQuesCryptonite/Forensics/HideandSeek]
    └─$ stegseek sakamoto.jpg /usr/share/wordlists/rockyou.txt.gz
    StegSeek 0.6 - https://github.com/RickdeJager/StegSeek

    [i] Progress: 35.20% (17.9 MB)

    [!] error: Could not find a valid passphrase.
    ```
    Thus after that I got to know that I have to first unzip it...

## Resources:
- [Stegseek Information](https://medium.com/@ria.banerjee005/steganography-tools-techniques-bba3f95c7148) - I got to know that what stegseek is...
- [stegseek GitHub Repository](https://github.com/RickdeJager/stegseek)