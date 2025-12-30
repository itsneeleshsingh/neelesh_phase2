# YARA Rules – YARA mean one!

## Task 1 (Introduction)
I was asked to start the virtual machine to begin the room.

### Steps
1. I started the VM  
2. After it was ready, I moved to the next task  

## Task 2 (YARA Rules)
This task explained what YARA is and how it can be used to detect malware by writing custom rules instead of relying only on antivirus tools.

### Steps
1. I read the short intro about YARA and its use cases  
2. I learned that YARA rules are built using metadata, strings, and conditions  
3. I learned about different string types like text, hex, and regex  
4. I learned how conditions work using logic like `and`, `or`, `not`, and `all of them`  
5. I created a new YARA rule file  
    ```bash
    ubuntu@tryhackme:~$ nano day13.yar
    ```
6. I added a basic rule to detect the string TBFC
    ```
    rule TBFC_check
    {
        strings:
            $TBFC = "TBFC"
        condition:
            all of them
    }
    ``` 
7. I ran YARA recursively(-r) on the target directory
    ```bash
    ubuntu@tryhackme:~$ yara -r day13.yar ./Downloads/easter/
    ```
8. The output showed multiple matching files
    ```bash
    TBFC_string ./Downloads/easter//easter46.jpg
    TBFC_string ./Downloads/easter//embeds
    TBFC_string ./Downloads/easter//easter52.jpg
    TBFC_string ./Downloads/easter//easter25.jpg
    TBFC_string ./Downloads/easter//easter10.jpg
    TBFC_string ./Downloads/easter//easter16.jpg
    ```
9. From this, I counted the image files containing the string
10. The total number of image files was 5
11. I then identified the correct regex pattern used in the files
12. The regex matched the format TBFC:<word>
13. I noticed one unusual file named embeds
14. I opened it to inspect its contents
    ```bash
    ubuntu@tryhackme:~$ cat ./Downloads/easter/embeds
    ```
15. The file revealed the hidden phrase written into the images
    ```python
    import glob, random seed = 42 phrase = "Find me in HopSec Island" words = phrase.split() images = sorted(glob.glob("easter*.jpg")) random.seed(seed) chosen = random.sample(images, len(words)) # 5 distinct random images chosen.sort() # keep word order increasing by filename for img, word in zip(chosen, words): with open(img, "ab") as f: f.write(f"\nTBFC:{word}\n".encode()) print(f"{img} <- TBFC:{word}")
    ```

### Answers
-   Number of image files containing TBFC\
    `5`
-   Correct regex used\
    `/TBFC:[A-Za-z0-9]+/`
-   Hidden message found\
    `Find me in HopSec Island`