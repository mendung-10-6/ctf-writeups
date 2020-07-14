# A Basic Challenge  
this challenge is a decoding challenge from various base, starting from binary(base 2),hex(base 16), and so on. I wrote a simple python script that you can hopefully understand  
```python
import base64
cipher = open("basic_chall.txt").read().split(" ")
flag = ""
# binary decode
for each in cipher:
	flag += chr(int(each,2))
cipher = flag.split(" ")
flag = ""
# hex decode
for each in cipher:
	flag += chr(int(each,16))
# base 64 decode
flag = base64.b64decode(flag).decode()
cipher = flag.split(" ")
flag = ""
# octal decode
for each in cipher:
	flag += chr(int(each,8))
print (flag)
```  
flag : `rgbCTF{c0ngr4ts_0n_b3ing_B4SIC}`  
  
# Joke Check!  
given this ciphertext `crmNEQ{l_nstnvpy_nlpdlc_dlwlo}` it looks like it's already on the flag format, decoding using caesar cipher with the amount of 15 gives us the flag.  
flag : `rgbCTF{a_chicken_caesar_salad}`  
  
# A Fine Day  
this time we are given a larger ciphertext with something that looks like the flag at the end, this time because there's a lot of character it is feasible to do a [substitution cipher](https://quipqiup.com/), although [affine cipher](https://www.dcode.fr/affine-cipher) is the intended solution.  
flag : `rgbCTF{a_fine_affine_cipher}`  
  
# r/ciphers  
this time the intended solution is to do a [substitution cipher](https://quipqiup.com/)  
flag : `rgbCTF{just_4sk_th3_int3rn3t_t0_d3crypt_it}`  
  
# Simple RSA  
this is where things starts to get real, we are given [RSA](https://en.wikipedia.org/wiki/RSA_(cryptosystem) public keys, where public keys are used to encrypt a message, to decrypt the message, we have to factor `n`, and then perform some calculations stated in wikipedia, RSA security heavily relies on how large the number is, however in this case `n` is relatively small, we can find it's factor at the [database](http://factordb.com/)  
```python
from Crypto.Util.number import long_to_bytes as ltb
n= 5620911691885906751399467870749963159674169260381
e=65537
c=1415060907955076984980255543080831671725408472748
p = 255097177  
q = 22034393943473183756163118460342519430053
phi = (p-1)*(q-1)

def egcd(a, b):
    if a == 0:
        return (b, 0, 1)
    else:
        g, y, x = egcd(b % a, a)
        return (g, x - (b // a) * y, y)

def modinv(a, m):
    g, x, y = egcd(a, m)
    if g != 1:
        raise Exception('modular inverse does not exist')
    else:
        return x % m
d = modinv(e,phi)
print (ltb(pow(c,d,n)).decode())
```  
we get a flipped flag but we can just flip it back.  
flag : `rgbCTF{brut3_f0rc3}`  
  
# Pieces  
in this challenge we have to reverse the divide() function to get the flag back, we can see the divide function divide our input by 2, and then added it to variable ans, and then compared with something, so we can simply multiply the characters they used to compare and get the flag, however it's not that easy,  
```
61 / 2 = 30
60 / 2 = 30
```
in above example we can see two different numbers have the same result when divided by 2 because of rounding, but we see that they also added `/` or `|` in variable ans so we can know whether or not the character supposed to be even, or odd numbers.  
```python
cipher = "9|2/9/:|4/7|8|4/2/1/2/9/"
flag = ''
for i in range(0,len(cipher),2):
	if cipher[i+1] == "|":
		flag += chr(ord(cipher[i])*2)
	else:
		flag += chr(ord(cipher[i])*2 + 1)
print (flag)
```  
flag: `rgbCTF{restinpieces}`  
  
# Shoob  
for this challenge we gonna use [a very nice stego tool](https://aperisolve.fr/), after uploading the image, we can see the very first superimposed image has some red text in it which is the flag.  
flag : `rgbCTF{3zier_4haN_s4n1ty}`  
  
# Quirky Resolution  
Using aperisolve, we can see a large qrcode, scanning it with a mobile qrcode scanner gives us the flag.  
flag : `rgbCTF{th3_qu1rk!er_th3_b3tt3r}`