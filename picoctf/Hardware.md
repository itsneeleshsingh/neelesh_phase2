# 1. IQ Test
This challenge we were given with a image with multiple logic gates. We have to take the input and manually count the 12 bit output.

## Solution:
1. First, I noted the input value `x = 30478191278`. Then I converted this decimal number into its binary form using Python
```
>>> print(format(30478191278,"036b"))
011100011000101001000100101010101110
```
This binary string is the input to the logic circuit.(I did 36 bit conversion as there were 36 input bits - from 0 to 35)
2. The challenge involves multiple gates: XOR, XNOR, NAND, AND, OR, NOR, and NOT.
3. Then I manually calculatted and found out the flag(drawing using canva)
![NeelIQCryptonitehardware](https://github.com/user-attachments/assets/c042b917-45e5-4506-9cf0-6239fd6d33b2)

4. The final output binary sequence I derived was `100010011000`. I then formatted this as the flag:`nite{100010011000}`.

## Flag:
```
nite{100010011000}
```

## Concepts learnt:
- Conversion of decimal numbers to binary.
- Understanding of common logic gates: XOR, XNOR, NAND, AND, OR, NOR, and NOT.
- Patience

## Notes:
- Was very time consuming...

## Resources:
NULL(only image was needed)







# 2. I like logic
This challenge involves working with a file that has an unfamiliar extension (.sal) and contains binary data. Ultimately the goal is to uncover hidden or encoded messages within the file that reveal the flag.

## Solution:
1. First, I opened the `.sal` file, but it appeared to contain some binary data that I could not interpret directly.  
2. I searched for the `.sal` extension on Google, and the top result was related to an application called Logic 2, which is used for logic analysis and signal decoding. It immediately clicked as the question name was also the same.
    <img width="1919" height="579" alt="image (3)" src="https://github.com/user-attachments/assets/579343fc-04aa-45cc-b903-489cfaca064a" />

3. I installed Logic 2 on my Linux system, changed its permission to make it executable, and then ran it through the terminal.  
4. It was asking for some connect logic but there was option of `load capture file` - so I clicked on that selected that file and then there was a yellow waveform type of thing like the morse code in citadel.
5. I zoomed it tried to play but nothing was working. 
6. Then I googled some documents like how to extract some hidden texts or values and I found a github repo in which in README section something about - `Analyzers` and `Async Serial` was written to read some data from the file.
7. I clicked on the Analyzer tab in Logic 2, selected the Async Serial analyzer and it had various settings in it. 
    <img width="1920" height="983" alt="image (4)" src="https://github.com/user-attachments/assets/a96f458a-1d07-4619-b772-f77b0aa52e66" />

8. I was really confused so I selected the channel0 and default settings but nothing worked. Then I did repeatedly but for 3 channel it worked and some texts were written above the waveform.
    <img width="1920" height="983" alt="image (5)" src="https://github.com/user-attachments/assets/bdf007d0-338d-49ba-b2bf-0ff8429726c6" />

9. Then I was not able to read the whole text but after some trials I found the button to see whole text I copied in a normal text file.
    ```
    on't think he makes any claims of
    that kind.  But I do believe he has got something new."

    ..............(some text)..........
    FCSC{b1dee4eeadf6c4e60aeb142b0b486344e64b12b40d1046de95c89ba5e23a9925}
    ..............(some text)..........

    It was a bird's-eye view of creation, as interpreted by science, which,
    in language always clear and sometimes picturesque, he unfolded before
    us.  He told us of t
    ```
    <img width="978" height="939" alt="image (6)" src="https://github.com/user-attachments/assets/bc0dbe0e-ef89-4585-9767-d529a2ff0b0a" />

10. And there was a flag type of text which was the flag.

## Flag:
```
FCSC{b1dee4eeadf6c4e60aeb142b0b486344e64b12b40d1046de95c89ba5e23a9925}
```  

## Concepts learnt:
- Recognizing file types based on extensions and content.  
- Using external tools (Logic 2) for signal analysis and decoding.  
- Loading and analyzing binary or waveform data within a specialized application.  

## Notes:
- Initially, I thought I might need to decode or convert the binary data manually, but using Logic 2 simplified the process.   

## Resources:
- [Saleae Output Parser GitHub Repository](https://github.com/idaholab/Saleae_Output_Parser)  

- [Logic 2 download page](https://www.saleae.com/pages/downloads?srsltid=AfmBOooTHR-DtKVtqo3zATR45Mbo3okc0WhNmuRNUDNLk64ARmGDXE7E)
