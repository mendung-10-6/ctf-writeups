# I Love Rainbows  
We are given a file each line containing 32-64 characters, which looks like hashes, using [crackstation](https://crackstation.net/) we can see that each hash contains 1 to 2 characters from the flag, cracking all the hash and we got the flag.  
flag : `rgbCTF{4lw4ys_us3_s4lt_wh3n_h4shing}`  
  
# e  
For this challenge N is insanely large, because of that the the ciphertext did not reach N at this point, so we can just take eth root of c and get the flag  
```python
import gmpy2
from Crypto.Util.number import long_to_bytes as ltb
n = 0x5fb76f7f36c0d7788650e3e81fe18ad105970eb2dd19576d29e8a8697ebbd97f4fc2582bf1dc53d527953d9615439ca1b546b2fc1cd533db5fce6f72419f268e3182c0324a631a17d6b3e76540f52f2df51ca34983392d274f292139c28990660fa0e23d1b350da7aa7458a3783107a296dcd1720e32afb431954d8896f0587cd1c8f1d20701d6173b7cffe53679ebda80f137c83276d6628697961f5fcd39e18316770917338c6dc59a241dcdc66417fed42524c33093251c1d318b9dbeb6c3d0a69438b875958e8885d242d196e25bc73595e7f237c8124e07a79f7066f2dee393e2130306ba29e7ece1825798ff8b35416b3a0d96bcdc6eca5616ea2874954f8f88232450ddad3e109338bcc5d84e7b592a6b0871bd4130b84f81ed188e9d5495c8545aa8dea2b65e8605f5a49e3a1c221cbcc301665187658784a8f42a23c2ca2572477ba56ff19934019b48f5a1ab8a50626c85bdd476b11e8c1fb0b740c2370de3da5cc06371a7aa2c4e12eee3dc4cda07a8c84ba2bc3ee2017156468af95111d7ee5152ce35e66fa027a944b43c27fbd27faa5b4f9075be3526a7a5be8a533b523cd5c738c724e97597fc2e3666cfcad7c79d972ff8d9572100e860395cdc3761af3f4cc225a6df83a55802723f95cfba5918d83913f2cc9b219210249928c291310d449042772e2d0a50620d666a137f79770de6f10196b30cc756e1
e = 0b1101
c = gmpy2.mpz(0x6003a15ff3f9bc74fcc48dc0f5fc59c31cb84df2424c9311d94cb40570eeaa78e0f8fc2917addd1afc8e5810b2e80a95019c88c4ee74849777eb9d0ee27ab80d3528c6f3f95a37d1581f9b3cd8976904c42f8613ee79cf8c94074ede9f034b61433f1fef835f2a0a45663ec4a0facedc068f6fa2b534c9c7a2f4789c699c2dcd952ed82180a6de00a51904c2df74eb73996845842276d5523c66800034351204b921d4780180ca646421c61033017e4986d9f6a892ed649c4fd40d4cf5b4faf0befb1e2098ee33b8bea461a8626dd8cd2eed05ccd471700e2a1b99ed347660cbd0f202212f6c0d7ad8ef6f878d887af0cd0429c417c9f7dd64890146b91152ea0c30637ce503635018fd2caf436a12378e5892992b8ec563f0988fc0cebd2926662d4604b8393fb2000)
gmpy2.get_context().precision=200
x = int(gmpy2.root(c,e))
assert pow(x,e,n) == c
print ltb(x)[::-1]
```
flag : `rgbCTF{lucky_number_13}`  
  
# Occasionally Tested Protocol  
Classic OTP with time as the seed, we can just get time the same time as we connect to the server, and test the seed locally. You can see the script that I used to find the approximate time on the commented code below.  
```python
from random import seed, randint as w
from time import time
# import pwn
# j = int(time())
# print (j)
# r = pwn.remote("167.172.123.213", 12345)
# print (r.recv())
# r.close()


try_seed = 1594516169
result = [2327,5113,7373,8967,5424,9245,9867,7147,7924,723]
found = False
while not found:
	seed(try_seed)
	for i in range(10):
		if w(5,10000) == result[i]:
			found = True
		else:
			found = False
			try_seed+=1
			break
b = bytearray([w(0, 255) for _ in range(40)])

flag = int.to_bytes(9756397644136381438199947801771447561151891907369603561251869566720,999,'little').rstrip(b"\x00")
n = bytearray([l ^ p for l, p in zip(flag, b)])
print (n)
```
flag : `rgbCTF{random_is_not_secure}`  
  
# Shakespeare Play, Lost (and found!)  
It's a book cipher,the first number correspond to the nth line of the book, and the second number correspond to the nth character of the line.  
```python
keys = [[33, 20],[71, 5],[43, 142],[60, 150],[73, 312],[78, 66],[15, 22],[12, 115],[29, 18],[51, 147],[45, 68],[34, 14],[54, 126],[15, 48],[3, 4],[60, 126],[45, 77],[13, 69]]
text = open("play").read().split("\n")
flag = ''
for key in keys:
	flag += (text[key[0]])[key[1]]
print "rgbCTF{" + flag + "}"
```
flag : `rgbCTF{itsanrgbtreeeeeee!}`
