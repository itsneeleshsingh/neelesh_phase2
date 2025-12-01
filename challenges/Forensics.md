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








# 2. Nutrela Chunks
This challenge involves repairing a corrupted PNG image file. The description hints that there are issues with its structure and chunks. The goal is to reconstruct it back to a valid PNG format so that it can be properly rendered, revealing the  to get the flag.

## Solution:
1. Firts, upon opening the image an error message stated that it was "Not a png file". After reading the description I realised that I need to edit the hex of this `.png` file as it was written about some chunks.
   ![image1](images/NutrelaChunks1.png)
2. I read about PNG structure from a medium blog and got to know about that it consists of a file signature, followed by chunks such as `IHDR, IDAT, and IEND`.
3. I discovered the `pngcheck` command-line tool(while searching how to find errors in a pngfile), which can validate PNG files. So I installed this on my Kali Linux machine using:
   ```bash
   sudo apt install pngcheck
   ```
4. Running `pngcheck` on `nutrela.png`, I received the error indicating that it was neither a PNG nor a valid stream:
   ```bash
   ┌──(neels㉿neel)-[~/CustomQuesCryptonite/Forensics/NutrelaChunks]
   └─$ pngcheck nutrela.png
   nutrela.png  this is neither a PNG or JNG image nor a MNG stream
   ERROR: nutrela.png
   ```
5. The output suggested that the file signature was itself incorrect. So I changed the file signature to .png format. First I opened the file using hex editor:
   ```bash
   ┌──(neels㉿neel)-[~/CustomQuesCryptonite/Forensics/NutrelaChunks]
   └─$ hexedit nutrela.png
   ```
6. I changed the first four bytes from `89 70 6E 67` to `89 50 4E 47`. After saving the changes using `Ctrl+X`, I ran `pngcheck` again.
   ```bash
   ┌──(neels㉿neel)-[~/CustomQuesCryptonite/Forensics/NutrelaChunks]
   └─$ pngcheck nutrela.png
   nutrela.png  first chunk must be IHDR
   ERROR: nutrela.png
   ```
7. The new output indicated the first chunk must be IHDR. I changed the 4 bytes next to the IHDR string from `69 68 64 72` to `49 48 44 52`.
8. Checking with `pngcheck` again revealed an illegal reserved-bit-set chunk for IDAT.
   ```bash
   ┌──(neels㉿neel)-[~/CustomQuesCryptonite/Forensics/NutrelaChunks]
   └─$ pngcheck nutrela.png
   nutrela.png  illegal reserved-bit-set chunk idat
   ERROR: nutrela.png
   ```
9. I then modified the IDAT chunk by changing the four bytes from `69 64 61 74` to `49 44 41 54`. I ran the pngcheck again.
   ```bash
   ┌──(neels㉿neel)-[~/CustomQuesCryptonite/Forensics/NutrelaChunks]
   └─$ pngcheck nutrela.png
   nutrela.png  illegal reserved-bit-set chunk iend
   ERROR: nutrela.png
   ```
   ![image1](images/NutrelaChunks2.png)
10. After several checks to change IEND chunk, but I was not able to locate it, So possibly due to an overflow of the IDAT chunk, maybe IEND has corrupted.
11. Despite this, the file was now opening correctly in which the flag was written.

## Flag:
```
nite{n0w_y0u_kn0w_ab0ut_PNG_chunk5}
```

## Concepts learnt:
- Understanding the structure of PNG files including various chunks such as IHDR, IDAT, IEND.
- Importance of file signatures.
- Chunks in PNG files are case-sensitive, affecting how the file is interpreted(Got to know while changing from ihdr to IHDR).
- Usage of `pngcheck` for locating errors in PNG files.
- Familiarity with hex editors for manipulating file byte structure directly.

## Notes:
- Initially, I misread the file signature and spent extra time figuring out which parts needed modification.
- While trying to find the IEND, I mistakenly thought it would be located similarly to the other chunks, but it was not visible anywhere in the whole hex file.

## Resources:
- [Medium blog on PNG structure](https://medium.com/@0xwan/png-structure-for-beginner-8363ce2a9f73)
- `man pngcheck` for more information on the command and its use
- Various online searches related to PNG corruption and recovery techniques.