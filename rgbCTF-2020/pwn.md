# Too Slow  
Let's take a look at main:  
```c
int main(void)

{
  uint uVar1;
  
  puts("Flag Decryptor v1.0");
  puts("Generating key...");
  uVar1 = getKey();
  win((ulong)uVar1);
  return 0;
}
```
so far it looks pretty normal, let's take a look at getKey: 
```c
ulong getKey(void)

{
  uint i;
  uint c;
  
  i = 0;
  while (i < 0x265d1d23) {
    c = i;
    while (c != 1) {
      if ((c & 1) == 0) {
        c = (int)c / 2;
      }
      else {
        c = c * 3 + 1;
      }
    }
    i = i + 1;
  }
  return (ulong)i;
}
```  
as you can see it looks like `c` is operated a lot, but the function return `i`, `i` didn't affected by `c` and only incremented by 1 every loop, so we know that getKey will return 0x265d1d23.  
We can use GDB to change the value and skip the getKey function (btw I use gdb gef).  
```
b main
r
set $eax = 0x265d1d23
jump *main+53
```
flag : `rgbCTF{pr3d1ct4bl3_k3y_n33d5_no_w41t_cab79d}`  
  
# Advanced Reversing Mechanics 1  
let's take a look at encryptFlag() function:  
```c
void encryptFlag(char *flag)

{
  char flag2;
  
  flag2 = *flag;
  if (flag2 == '\0') {
    return;
  }
  do {
    *flag = flag2 + -1;
    flag = flag + 1;
    flag2 = *flag;
  } while (flag2 != '\0');
  return;
}
```  
it might be a little confusing for some people, but basically `*flag` with `*` is the value of the curent flag address, and `flag` without `*` is the the current index of the flag, so the code will looks something like this:  
```python
i = 0
flag2 = flag[0]
while True:
  flag[i] = flag2 - 1
  i = i + 1
  try:
    flag2 = flag[i]
  except IndexError:
    break
```  
so basically the code lops through the flag and decrease all character by one, now let's write the solve script that increment all the character by one.  
```python
x = [0x71, 0x66, 0x61, 0x42, 0x53, 0x45, 0x7A, 0x40, 0x51, 0x4C, 0x5E, 0x30, 0x79, 0x5E, 0x31, 0x5E, 0x64, 0x59, 0x5E, 0x38, 0x61, 0x36, 0x65, 0x37, 0x63, 0x7C]
flag = ""
for i in x:
  flag += chr(i+1)
print (flag)
```  
flag : `rgbCTF{ARM_1z_2_eZ_9b7f8d}`