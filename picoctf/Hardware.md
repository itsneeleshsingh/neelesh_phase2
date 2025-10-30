# IQ Test
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
