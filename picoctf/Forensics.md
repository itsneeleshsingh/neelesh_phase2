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
