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







# 3. RAR of the Abyss
The challenge revolves around analyzing a network capture file in order to decrypt a RAR file to get the flag.

## Solution:
1. First, I opened the provided `.pcap` file using Wireshark, a tool I was familiar with from previous CTF challenges.
   ![image2](images/RARoftheAbyss1.png)
2. Upon going through the captured packets, I noticed a variety of protocols in use such as TCP,ICMP,etc and started scrolling through them.
3. I started to look all the packets one by one and at some packets, found some string mentioning Camus and Nietzsche.
4. During this, I found a string that appeared to be a password: `b3y0ndG00dand3vil` in one of the TCP packets and I was not sure that whether this was the Correct password or not and for what(I was just going through the hint provided).
   ![image2](images/RARoftheAbyss4.png)
5. I also located a packet that indicated the presence of a RAR file, as the word "rar" was visible in the contents.
![image2](images/RARoftheAbyss2.png)
6. To extract the RAR file, I selected the relevant TCP packet containing the RAR data and followed it by clicking on `Follow > TCP Stream` and then saved the output as `output.rar` in my folder.
   ![image2](images/RARoftheAbyss3.png)
7. When I attempted to extract the contents of the RAR file, it prompted me for a password. I entered `b3y0ndG00dand3vil` as password.
   ![image2](images/RARoftheAbyss5.png)
8. The extraction was successful, where a `flag.txt` file was there which contained the flag.
   ![image2](images/RARoftheAbyss6.png)

## Flag:
```
nite{thus_sp0k3_th3_n3tw0rk_f0r3ns1cs_4n4lyst}
```

## Concepts learnt:
- Extracting RAR files from TCP packets
- Analyzing packets to retrieve specific strings or data
- Understanding the usage of network forensics tools like Wireshark

## Notes:
- Initially, I had trouble finding the RAR file in the packet capture, which led me to consider other methods for viewing binary files which I looked in google and pages.
- I spent some time exploring different options in Wireshark before deciding to focus on identifying specific streams and strings.

## Resources:
- [Wireshark Tutorial Blog](https://www.varonis.com/blog/how-to-use-wireshark)






# 4. Nine Tails
The challenge has flag encrypted as passwords in firefox browser. The task is to locate specific files related to logins and encryption keys. It involves navigating through file directories to uncover credentials that may lead to the flag.

## Solution:
1. I started by extracting the provided RAR file which had an AD1 file which I first didnt understand what extension is this.
2. After some research I learned that this type of file can be examined using a program called `FTK Imager`. So I installed this app using its official site.
3. Upon opening the AD1 file I viewed several files but didnt understand at first of how to open it. I mounted the .ad1 file and tried to look using file browser but nothing was there in that. Then I looked for a video to get how to insert and read ISO file. I took a closer look at the challenge description and realized I was meant to search for passwords in the Firefox folder.
4. After many explorations, I Navigated to the AppData directory and I found the Mozilla folder containing Firefox, but my initial search under the user `j4gjesg4` yielded no passwords or useful files. So I tried more files to find for json or password files.
5. A further search led me to the Roaming directory where I accessed the database and JSON files containing the encrypted password information.
6. To decode the passwords I looked for a suitable tool and found `firepwd` after trying more tools, which is used for cracking passwords.
7. I installed the necessary Python packages for `firepwd` using the commands:
   ```bash
   ┌──(venv)─(neels㉿neel)-[~/CustomQuesCryptonite/Forensics/NineTails]
   └─$ pip install PyCryptodome

   ┌──(venv)─(neels㉿neel)-[~/CustomQuesCryptonite/Forensics/NineTails]
   └─$ pip install pyasn1
   ```
8. I ran `firepwd.py` with the database file:
   ```bash
   ┌──(venv)─(neels㉿neel)-[~/CustomQuesCryptonite/Forensics/NineTails]
   └─$ python firepwd.py -d .  

   globalSalt: b'b5dbfec66b891e193f516eccaf39209a93634332'
   SEQUENCE {
      SEQUENCE {
      OBJECTIDENTIFIER 1.2.840.113549.1.5.13 pkcs5 pbes2
      SEQUENCE {
         SEQUENCE {
            OBJECTIDENTIFIER 1.2.840.113549.1.5.12 pkcs5 PBKDF2
            SEQUENCE {
            OCTETSTRING b'eaa234484d176f2f091e1a9b2162b550a9874bf9ced92daa19c43e058b1328cf'
            INTEGER b'01'
            INTEGER b'20'
            SEQUENCE {
               OBJECTIDENTIFIER 1.2.840.113549.2.9 hmacWithSHA256
            }
            }
         }
         SEQUENCE {
            OBJECTIDENTIFIER 2.16.840.1.101.3.4.1.42 aes256-CBC
            OCTETSTRING b'c361eb13322fd6004ad463de0ef2'
         }
      }
      }
      OCTETSTRING b'c2cb6eb12879f4c649458e59d634f355'
   }
   clearText b'70617373776f72642d636865636b0202'
   password check? True
   SEQUENCE {
      SEQUENCE {
      OBJECTIDENTIFIER 1.2.840.113549.1.5.13 pkcs5 pbes2
      SEQUENCE {
         SEQUENCE {
            OBJECTIDENTIFIER 1.2.840.113549.1.5.12 pkcs5 PBKDF2
            SEQUENCE {
            OCTETSTRING b'ae2720e0964ce4beabedba40345712df4234f9ac9cd86e53a08982b65abbbd9c'
            INTEGER b'01'
            INTEGER b'20'
            SEQUENCE {
               OBJECTIDENTIFIER 1.2.840.113549.2.9 hmacWithSHA256
            }
            }
         }
         SEQUENCE {
            OBJECTIDENTIFIER 2.16.840.1.101.3.4.1.42 aes256-CBC
            OCTETSTRING b'e3f13fa968393e84117de43bee0e'
         }
      }
      }
      OCTETSTRING b'7810f052c04cd95b738f9df37a27fe297c3cc97d3a04ca7a71b89ba111f1dbbf'
   }
   clearText b'ab1ccd54c13b1573648a347a6e4c73dcc27af8cb8ad3c74c0808080808080808'
   decrypting login/password pairs
   https://www.rehack.xyz:b'warlocksmurf',b'GCTF{m0zarella'
   https://ctftime.org:b'ilovecheese',b'CHEEEEEEEEEEEEEEEEEEEEEEEEEESE'
   https://www.reddit.com:b'bluelobster',b'_f1ref0x_'
   https://www.facebook.com:b'flag',b'SIKE'
   https://warlocksmurf.github.io:b'Man I Love Forensics',b'p4ssw0rd}'
   ```
9. The tool processed the data and successfully returned the decrypted password pairs revealing the flag.

## Flag:
```
GCTF{m0zarella_f1ref0x_p4ssw0rd}
```

## Concepts learnt:
- How to extract files from an AD1 format using FTK Imager.
- Understanding the file structure of Firefox password storage including where passwords are located.
- Utilizing forensic tools like `firepwd` for decrypting Firefox-stored passwords.
- Importance of navigating different directories like AppData and Roaming to locate hidden files.

## Notes:
- Initially, I attempted to use `firefox_decrypt.py` which failed due to missing the profile location which I did not understand.
   ```bash
   ┌──(venv)─(neels㉿neel)-[~/CustomQuesCryptonite/Forensics/NineTails]
   └─$ python firefox_decrypt.py .
   2025-12-04 22:46:52,112 - WARNING - profile.ini not found in .
   2025-12-04 22:46:52,112 - WARNING - Continuing and assuming '.' is a profile location
   2025-12-04 22:46:52,113 - ERROR - Couldn't initialize NSS, maybe '.' is not a valid profile?
   ```

## Resources:
- Steps to load a file in FTK Imager: [YouTube Link](https://www.youtube.com/watch?v=CPup3ClC7nE)
- Firepwd repository: [GitHub - firepwd](https://github.com/lclevy/firepwd/tree/master)
- Information on Firefox password storage: [Super User Discussion](https://superuser.com/questions/267005/where-are-my-firefox-passwords-saved)