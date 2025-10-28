# 1. Trivial Flag Transfer Protocol
This challenge involves analyzing a network capture file to uncover how a flag was transferred. The file provided is a pcapng file, which typically contains network traffic data. The goal is to extract the flag hidden within the files.

## Solution:
1. So first I didnt understand what to do so first I began to search about pcapng file as it was the extension of the file named - `tftp.pcapng`. Then I found that wireshark app is used to take a look for these types of file.
2. So I head on to my linux and then search for wireshark. There was a app so I opened it.
    <img width="1920" height="1080" alt="image (2)" src="https://github.com/user-attachments/assets/a66f01a7-8d73-4384-b56e-f6a4566ebd7c" />

3. I browsed through different protocol layers and packet details, but I was not abke to do anything. Then I noticed in the hex view a mention of `instruction.txt`, which suggested there might be files transferred that I could download. So after googling I found that we can download all the files from File > objects something.  
4. I went to `File > Export Objects > TFTP`(I found tftp options as my file name was that and it was a luck) to see if I could get any files from the capture. Many files appeared, including an instruction file, 3 images, a plan, and a `.deb` package file. I saved all these files to my local system in a folder.
    <img width="1920" height="1080" alt="image (3)" src="https://github.com/user-attachments/assets/8b8a10b2-04f3-4826-b5ef-cff86ccb9033" />

5. I read `instructions.txt`, I saw a string of text that was not readable directly. I tried common decoding methods like base64, but they didn't work. Then I remembered that ROT13 or ROT47 is often used like in Citadel CTF.
    ```bash
    ┌──(neels㉿neel)-[~/PicoCTF/TrivialFlagTransfer]
    └─$ cat instructions.txt
    GSGCQBRFAGRAPELCGBHEGENSSVPFBJRZHFGQVFTHVFRBHESYNTGENAFSRE.SVTHERBHGNJNLGBUVQRGURSYNTNAQVJVYYPURPXONPXSBEGURCYNA
    ```
6. After applying ROT47 which dosent seem anything good, I applied ROT13 to the text in `instructions.txt`, I found that it said something.
    ```
    TFTPDOESNTENCRYPTOURTRAFFICSOWEMUSTDISGUISEOURFLAGTRANSFER.FIGUREOUTAWAYTOHIDETHEFLAGANDIWILLCHECKBACKFORTHEPLAN
    ```
    Here something about plan was mentioned, so I cat plan.

7. I then opened the `plan` file, which also contained an encoded string. Decoding it with ROT13 revealed a message indicating to run the program.
    ```
    ┌──(neels㉿neel)-[~/PicoCTF/TrivialFlagTransfer]
    └─$ cat plan

    VHFRQGURCEBTENZNAQUVQVGJVGU-QHRQVYVTRAPR.PURPXBHGGURCUBGBF
    ```
    ```
    IUSEDTHEPROGRAMANDHIDITWITH-DUEDILIGENCE.CHECKOUTTHEPHOTOS
    ```  
8. I recognized the `.deb` file as a Debian package. To analyze its contents, I installed it using `sudo apt-get install ./program.deb` which i googled because I dont know how to install debian package using CLI. 
    ```
    ┌──(neels㉿neel)-[~/PicoCTF/TrivialFlagTransfer]
    └─$ sudo apt-get install ./program.deb
    Reading package lists... Done
    Building dependency tree... Done
    Reading state information... Done
    Note, selecting 'steghide' instead of './program.deb'
    The following packages will be DOWNGRADED:
    steghide
    0 upgraded, 0 newly installed, 1 downgraded, 0 to remove and 1351 not upgraded.
    Need to get 0 B/138 kB of archives.
    After this operation, 52.2 kB disk space will be freed.
    Do you want to continue? [Y/n] Y
    Get:1 /home/neels/PicoCTF/TrivialFlagTransfer/program.deb steghide amd64 0.5.1-9.1+b1 [138 kB]
    dpkg: warning: downgrading steghide (0.5.1-15) to (0.5.1-9.1+b1)
    (Reading database ... 403335 files and directories currently installed.)
    Preparing to unpack .../program.deb ...
    Unpacking steghide (0.5.1-9.1+b1) over (0.5.1-15) ...
    Setting up steghide (0.5.1-9.1+b1) ...
    Processing triggers for man-db (2.13.1-1) ...
    Processing triggers for kali-menu (2025.2.7) ..
    ```
9. Here it was installing steghide. I searched and found that - it can hide something in images like jpg and others but `bmp` was also mentioned which was the extension of our image. It supports hiding data in bmp file.
10. So I used steghide to extract but on google it was mentioned that it need a a password.
11. After many hit and trial I was not able to figure out. After much time it striked that in the plan it was written - `WITH-DUEDILIGENCE` so i tried password - `duediligence`. So I tried for all three images one by one and the third picture wordked.
    ```bash
    ┌──(neels㉿neel)-[~/PicoCTF/TrivialFlagTransfer]
    └─$ steghide extract -sf ./picture3.bmp -p "DUEDILIGENCE"
    the file "flag.txt" does already exist. overwrite ? (y/n) y
    wrote extracted data to "flag.txt".
    ```
12. Opening `flag.txt`, I obtained the flag: 
    ```bash
    ┌──(neels㉿neel)-[~/PicoCTF/TrivialFlagTransfer]
    └─$ cat flag.txt

    picoCTF{h1dd3n_1n_pLa1n_51GHT_18375919}
    ```
    <img width="1920" height="1080" alt="image (4)" src="https://github.com/user-attachments/assets/c748d658-c37f-4160-a8d6-6289365d08d1" />

## Flag:
```
picoCTF{h1dd3n_1n_pLa1n_51GHT_18375919}
```

## Concepts learnt:
- How to analyze pcapng files using Wireshark to find transferred objects.
- Exporting objects from Wireshark using `File > Export Objects`.
- Recognizing encoded text and decoding with ROT13.
- Understanding the use of steganography tools like steghide for hiding and extracting data.
- Installing and using `.deb` packages on Linux.
- The importance of contextual hints in decoding and extracting hidden data.

## Notes:
- Initially, I considered base64 and other encoding methods but found they were not effective.
- I explored different protocols within Wireshark but focused on the files transferred via TFTP later.
- There was a trial-and-error process with passwords for steghide, which led to the key "duediligence" based on the hint in the plan.
- Mistakenly, I first tried to run the `.deb` file directly without installing it, which didn't work, so I realized I needed to install it first.

## Resources:
- [Rot13 Decoder](https://rot13.com/)
- [Wireshark Info](https://en.wikipedia.org/wiki/Wireshark)
- [Steghide Tool Documentation](https://www.kali.org/tools/steghide/)
- Google







# 2. tunn3l v1s10n
This challenge provides a file and we have to recover or find the hidden flag within this file. We have to explore file metadata, examine the file in hex or manipulate its content to reveal the hidden message.

## Solution:
1. First, I downloaded the file and checked its type using the command `file tunn3l_v1s10n`. It returned as `data`, so i didnt get any idea.
2. Since the file type wasnt clear, I thought of trying metadata extraction tools like exiftool like in Citadel. I ran `exiftool` to see if there was any embedded metadata.
    ```bash
    ──(neels㉿neel)-[~/PicoCTF/tunn3l]
    └─$ exiftool tunn3l_v1s10n
    ExifTool Version Number         : 13.25
    File Name                       : tunn3l_v1s10n
    Directory                       : .
    File Size                       : 2.9 MB
    File Modification Date/Time     : 2025:10:27 21:50:21+05:30
    File Access Date/Time           : 2025:10:27 21:50:52+05:30
    File Inode Change Date/Time     : 2025:10:27 21:50:52+05:30
    File Permissions                : -rw-rw-r--
    File Type                       : BMP
    File Type Extension             : bmp
    MIME Type                       : image/bmp
    BMP Version                     : Unknown (53434)
    Image Width                     : 1134
    Image Height                    : 306
    ```
3. The exiftool output showed that the file was a BMP image with a size of 2.9 MB..
4. I attempted to open the file as a BMP image using normal explorer, but it showed an error about an unsupported header size. So I suspected something was wrong with the header or hex data.
    <img width="1920" height="1080" alt="image (5)" src="https://github.com/user-attachments/assets/fa3b8c3c-5a68-417e-aa6d-e402aa31fc39" />

5. I opened the file with hexedit to inspect the raw hexadecimal content.
    ```
    ┌──(neels㉿neel)-[~/PicoCTF/tunn3l]
    └─$ hexedit tunn3l_v1s10n

    00000000   42 4D 8E 26  2C 00 00 00  00 00 BA D0  00 00 BA D0  00 00 6E 04  BM.&,.............n.
    00000014   00 00 32 01  00 00 01 00  18 00 00 00  00 00 58 26  2C 00 25 16  ..2...........X&,.%.
    00000028   00 00 25 16  00 00 00 00  00 00 00 00  00 00 23 1A  17 27 1E 1B  ..%...........#..'..
    ```
6. First I searched on google for some website and found a image:  
    <img width="960" height="720" alt="image (6)" src="https://github.com/user-attachments/assets/d9da1cab-7f2f-4721-84a4-272dab384066" />
    (I found this image on website mentioned in references while searching on net)

The hex data started with `42 4D`, which is the signature for BMP files, confirming its likely an image. I checked the header fields especially the file size, which was `8E 26 2C 00`. Reversing the bytes and converting to decimal gave 2,893,454 bytes around 2.89 MB matching the size(Which I learnt from google by looking through some random website). I did it using python.
    ```python
    >>>int(0x002C268E)
    2893454
    ```
7. I noticed that the header contained incorrect values in some parts, like the offset to the pixel data which didnt match the expected with the image. But somewhere was written that it can be different so I didnt do that. Now leaving some things I saw header something so I selected that part and checked with image - `BA D0 00 00` was given but in image - `28 00 00 00` was given, that is why it was showing error while opening image.
8. So I changed the value and clicked ctrl+s and ctrl+z to exit. I tried opening it again. The image now displayed properly, but the visible content was not the flag but it was just a fake flag - notaflag{sorry}. Now I was really confused after this, so I decided to look further for hex values.
    <img width="1920" height="1080" alt="image (7)" src="https://github.com/user-attachments/assets/3bf10326-0e1f-4b3b-ba7f-7fa80dc34fd9" />

9. I then examined the image's header parameters for width and height. The width was `6E 04 00 00` =  0x0000046E and using python = which converts to 1134 pixels(which is I think the px value) and the height was `32 01 00 00` = 0x00000132 which is 306 pixels.
10. This was really small comparing to the size of the image, so I tried to increase its value but I dont know how to do that. After that I assumed 900 pixels in hex as `0x384` using python, which is `00000384` in hex.
    ```python
    >>>hex(900)
    '0x384'
    ```
11. I replaced the height value in the header with `84 03 00 00` - then saved the file.
12. Opening this modified image revealed the flag:
    <img width="1920" height="1080" alt="image (8)" src="https://github.com/user-attachments/assets/fe871bb3-09e7-4f11-9d19-13bf22faaf27" />


## Flag:
```
picoCTF{qu1t3_a_v13w_2020}
```

## Concepts learnt:
- How to identify file types using the `file` command.
- Extracting and analyzing metadata with `exiftool`.
- Understanding and interpreting BMP headers, including headers, file size, and image dimensions.
- Using hex editors to manually inspect and modify binary files.
- Converting hexadecimal values to decimal using Python.
- Recognizing how header values influence image dimensions and how to manipulate them.

## Notes:
- Initially, I thought the file was completely corrupted or non-standard, but examining the header clarified the structure.
- I experimented with different height values that was - 400 but after that 900 - to reveal the full image containing the flag.

## Resources:
- [Image header details](https://engineering.purdue.edu/ece264/19sp/hw/HW11)
- Google







# 3. m00nwalk
This challenge involves decoding a message that appears to be encoded or hidden in some way, related to the theme of the moon.

## Solution:
1. At first, I was unable to figure out how to decode the message.
2. The hint mentioned `How did pictures from the moon landing get sent back to Earth?` so I tried on net and the first search result was a Wikipedia page where SSTV (Slow Scan Television) was mentioned. 
3. I searched randomly for `SSTV decode` on the internet and found a GitHub repo at https://github.com/colaclanth/sstv that provides tools for decoding SSTV signals. There was also a website coming but I tried that later(and It was really very easy).
4. I cloned the repository with the command: `git clone https://github.com/colaclanth/sstv.git`.  
5. Then, I navigated to the directory and installed the necessary setup with:  
    ```bash
    ┌──(neels㉿neel)-[~/PicoCTF/m00nwalk/sstv]
    └─$ sudo python3 setup.py install
    ```  
6. I went to my location where wav file was and ran the command as was given in README file of repo.
    ```bash
    ┌──(neels㉿neel)-[~/PicoCTF/m00nwalk/sstv/sstv]
    └─$ cd ~/PicoCTF/m00nwalk

    ┌──(neels㉿neel)-[~/PicoCTF/m00nwalk]
    └─$ ls
    message.wav  sstv

    ┌──(neels㉿neel)-[~/PicoCTF/m00nwalk]
    └─$ sstv -d message.wav

    [sstv] Searching for calibration header... Found!

    [sstv] Detected SSTV mode Scottie 1
    [sstv] Decoding image...   [############################################################] 100%
    [sstv] Drawing image data...
    [sstv] ...Done!
    ```  
7. The tool detected the mode as Scottie 1 and decoded the image successfully.  
    ```bash
    ┌──(neels㉿neel)-[~/PicoCTF/m00nwalk]
    └─$ ls

    message.wav  result.png  sstv
    ```
8. I opened the generated image (result.png) and I found the flag written there: `picoCTF{beep_boop_im_in_space}`.  

## Flag:
```
picoCTF{beep_boop_im_in_space}
```  

## Concepts learnt:
- Understanding the use of SSTV (Slow Scan Television) for transmitting images over radio signals.
- Learning how to use available tools to decode audio files that contain encoded images.

## Notes:
- Initially, I performed some exiftools and binwalk to find out some things but after some trials I used the hint.  
- There were some issues while installing the setup.py as I forgot to mention sudo before the command.
- Afterward I also tried the same question with a online website - [Online SSTV Decoder](https://sstv-decoder.mathieurenaud.fr/)

## Resources:
- [SSTV Decoder Repo](https://github.com/colaclanth/sstv)
- [Wikipedia site for Hint that I used](https://en.wikipedia.org/wiki/Apollo_11_missing_tapes)