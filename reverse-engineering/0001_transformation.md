# transformation

Find the challenge [here](https://play.picoctf.org/practice/challenge/104).

## Solution

This challenge provides the user with the _enc_ file containing the following string: _灩捯䍔䙻ㄶ形楴獟楮獴㌴摟潦弸彤㔲挶戹㍽_. It also provides a Python function:

```
''.join([chr((ord(flag[i]) << 8) + ord(flag[i + 1])) for i in range(0, len(flag), 2)])
```

Reading through the function you can see an undefined _flag_ variable being used, which leads you to the understanding that this function was used to encode the flag. Also note in the function every two characters are added up after a bit shift in the first character, resulting in very large numbers which in UTC are usuallly Chinese characters such as the ones provived in the challenge.

To solve this challenge an understanding of bitwise operators is needed. First let's see what happens in this portion of the code: `ord(flag[i]) << 8) + ord(flag[i + 1])`. In picoCTF every flag has a structure of _picoCTF{flag-content-here}_, so let's use this in the example. First with the character _p_, perform `bin(ord('p'))` for its binary Unicode representation **0b1110000**. Performing a bit shift of 8 bits to the left results in **0b111000000000000**, leaving enough space for the second character to be added without changing its code. Adding the binary representation of _i_ results in **0b111000001101001**, which is the Unicode for the _灩_ character.

With this understanding we now have a recipe for reverting the string back to its original form.

1. For each Chinese character, get its first 8 bits and then its last 8 bits.
2. Convert the binary values back to characters with the `chr()` function.

```
encodedFlag = "灩捯䍔䙻ㄶ形楴獟楮獴㌴摟潦弸彤㔲挶戹㍽"
flag = ""

for i in encodedFlag:
    letter1Code =  ord(i) >> 8
    
    mask = (1 << 8) - 1
    
    letter2Code = ord(i) & mask
    
    flag += chr(letter1Code)
    flag += chr(letter2Code)

print(flag)
```
