# Intern  
> 461 points, 12 solves  
> Thomson and Rotenberg Trading solutions has a new intern who is trying to implment the most secure system to send messages. The prototype is running here. Can you do a testing for us?  
> nc chall.nitdgplug.org 30205

In the service we get a value N and e, then we can do two things, we can print a current state, or encrypt the flag+\<the next state\>  
First we need to know 6 states in a row and then 2 encrypted flags right after that, you will see why in a minute
  
```
N : 54439204956884783583825707168407568536835108052943949822551259932256881394973671736239067995622919684388619703821965523142762491076269760818544447580952682425465643049313067352343894429851757916015291665625834181760425722639470057329036107836251426132596367705144804879686641513517888321300298117168596010249
e : 3
state for you: 361371748344852650374
state for you: 56207032340858927864
state for you: 362261237309305005908
state for you: 360122031065628859358
state for you: 248142098831853526892
state for you: 95052532927957477980
ENC(flag+next_num): 34281972808681760275933440736038270800674452338419069472192377045399071887745590950258302747456953913892470114641070931724355186295476008757607325450478874103165368211167560302342262605417459625129515589869452221543511496756925855078769232739615534129547981306444608151367566518743745368775094548114716950211
ENC(flag+next_num): 21892151338049698231730069775454913474831485630854511992721417730063522904697736389288118608164105943141234468977413240188220642538808557023852642632374620940662548022472836900356487845009159286542070698696198471556241325800539457530315897481354104862790297198518565604528784837380142259003237539219654231738

```

So first things first, by looking at the variables given, this is obviously an RSA encryption, I assume you are familiar with RSA, if not, go read it [here](https://en.wikipedia.org/wiki/RSA_(cryptosystem))

There are 2 candidates of vulnerability here:
1. The value of e is too small
2. We can see as many states as we can

But that raises some questions:
1. How exactly is this thing insecure just because e is small?
2. How are we supposed to know what type of random generator is this?

Let's forget about the state and focus on the 2 ciphertext

Note that the next_num padding is relatively small, so the difference is negligible, combined with the fact that e=3, this condition satisfy the [Coppersmith's short pad attack](https://en.wikipedia.org/wiki/Coppersmith%27s_attack#Coppersmith%E2%80%99s_short-pad_attack)

![image](https://user-images.githubusercontent.com/67094427/113403166-cc048780-93d8-11eb-9693-18e996326ee0.png)

It basically says that if 2 identical long message are padded with different, small padding, then it is possible to recover the plaintext.

I used [This very nice implementation](https://github.com/ValarDragon/CTF-Crypto/blob/master/RSA/FranklinReiter.sage) for the attack, and sure enough, we can see the plaintext, but not the flag (wait what?)
```sage
N = 54439204956884783583825707168407568536835108052943949822551259932256881394973671736239067995622919684388619703821965523142762491076269760818544447580952682425465643049313067352343894429851757916015291665625834181760425722639470057329036107836251426132596367705144804879686641513517888321300298117168596010249
e = 3
c1=34281972808681760275933440736038270800674452338419069472192377045399071887745590950258302747456953913892470114641070931724355186295476008757607325450478874103165368211167560302342262605417459625129515589869452221543511496756925855078769232739615534129547981306444608151367566518743745368775094548114716950211
c2=21892151338049698231730069775454913474831485630854511992721417730063522904697736389288118608164105943141234468977413240188220642538808557023852642632374620940662548022472836900356487845009159286542070698696198471556241325800539457530315897481354104862790297198518565604528784837380142259003237539219654231738
CoppersmithShortPadAttack(e,N,c1,c2)

result:
9255258906689088471398655118284473286203063538915008006852277152113405554901602899418617599602512732857086190350269171325121899733400211331788609372157534462527761785318093023109854403049137554142782504462654697026273914170390166661428840936687767785512597471423930984185584
Message is the following hex, but potentially missing some zeroes in the binary from the right end
0x446f20796f75207265616c6c79207468696e6b204c43472077697468205253412077696c6c206d616b65207365637572652073797374656d2062747720666c616720697320474c55477b6e34346d5f6c336b335f6b34346d5f343135315f3530757263335f373372336957b4601b6e223ef0
Message is one of:
b'Do you really think LCG with RSA will make secure system btw flag is GLUG{n44m_l3k3_k44m_4151_50urc3_73r3iW\xb4`\x1bn">\xf0'
b'\x88\xde@\xf2\xde\xea@\xe4\xca\xc2\xd8\xd8\xf2@\xe8\xd0\xd2\xdc\xd6@\x98\x86\x8e@\xee\xd2\xe8\xd0@\xa4\xa6\x82@\xee\xd2\xd8\xd8@\xda\xc2\xd6\xca@\xe6\xca\xc6\xea\xe4\xca@\xe6\xf2\xe6\xe8\xca\xda@\xc4\xe8\xee@\xcc\xd8\xc2\xce@\xd2\xe6@\x8e\x98\xaa\x8e\xf6\xdchh\xda\xbe\xd8f\xd6f\xbe\xd6hh\xda\xbehbjb\xbej`\xea\xe4\xc6f\xbenf\xe4f\xd2\xafh\xc06\xdcD}\xe0'
---------------------------------------------------------------------------
Error                                     Traceback (most recent call last)
```
You might encounter an error, but you can just decode the hex manually, or just fix the bug (it's not that hard) ;)

Alright, now we know that the random generator is LCG, we can also see part of the flag, the flag broke because of that next_num pad.

So we indeed need to somehow predict this next_num given the states, fortunately this is possible, [this article](https://tailcall.net/blog/cracking-randomness-lcgs/) explain the whole concept very nicely. The article explains that we only need 6 states in order for us to predict the next_num, now you know why we needed 6 states from the start, so all we need to do left is to run the solver and get the flag, right? well, not really
```python
from functools import *
from math import gcd
from Crypto.Util.number import long_to_bytes as ltb

def egcd(a, b):
    if a == 0:
        return (b, 0, 1)
    else:
        g, x, y = egcd(b % a, a)
        return (g, y - (b // a) * x, x)

def modinv(b, n):
    g, x, _ = egcd(b, n)
    if g == 1:
        return x % n

def modinv(b, n):
    g, x, _ = egcd(b, n)
    if g == 1:
        return x % n
        
def crack_unknown_increment(states, modulus, multiplier):
    increment = (states[1] - states[0]*multiplier) % modulus
    return modulus, multiplier, increment

def crack_unknown_multiplier(states, modulus):
    multiplier = (states[2] - states[1]) * \
        modinv(states[1] - states[0], modulus) % modulus
    return crack_unknown_increment(states, modulus, multiplier)

def crack_unknown_modulus(states):
    diffs = [s1 - s0 for s0, s1 in zip(states, states[1:])]
    zeroes = [t2*t0 - t1*t1 for t0, t1, t2 in zip(diffs, diffs[1:], diffs[2:])]
    modulus = abs(reduce(gcd, zeroes))
    return crack_unknown_multiplier(states, modulus)

n2, m, c = crack_unknown_modulus([361371748344852650374, 56207032340858927864, 362261237309305005908,
                                  360122031065628859358, 248142098831853526892, 95052532927957477980])
state = 95052532927957477980 # the very last state
nexstate = ((state*m)+c) % n2

# The one we get from the sage script
padded_text = 0x446f20796f75207265616c6c79207468696e6b204c43472077697468205253412077696c6c206d616b65207365637572652073797374656d2062747720666c616720697320474c55477b6e34346d5f6c336b335f6b34346d5f343135315f3530757263335f373372336957b4601b6e223ef0
print(ltb(padded_text-nexstate))

result:
Traceback (most recent call last):
  File "/mnt/d/technical/ctf/foobar/lcg.py", line 43, in <module>
    n2, m, c = crack_unknown_modulus([361371748344852650374, 56207032340858927864, 362261237309305005908,
  File "/mnt/d/technical/ctf/foobar/lcg.py", line 41, in crack_unknown_modulus
    return crack_unknown_multiplier(states, modulus)
  File "/mnt/d/technical/ctf/foobar/lcg.py", line 32, in crack_unknown_multiplier
    multiplier = (states[2] - states[1]) * \
TypeError: unsupported operand type(s) for *: 'int' and 'NoneType'
```
You see, appearantly we got unlucky and the states somehow doesn't work, so we need to try again, this time I'll just write a script to keep reading the staste until we found the sequence of states that is feasible to crack
```python
from functools import *
from math import gcd
from Crypto.Util.number import long_to_bytes as ltb

def egcd(a, b):
    if a == 0:
        return (b, 0, 1)
    else:
        g, x, y = egcd(b % a, a)
        return (g, y - (b // a) * x, x)

def modinv(b, n):
    g, x, _ = egcd(b, n)
    if g == 1:
        return x % n

def crack_unknown_increment(states, modulus, multiplier):
    increment = (states[1] - states[0]*multiplier) % modulus
    return modulus, multiplier, increment

def crack_unknown_multiplier(states, modulus):
    multiplier = (states[2] - states[1]) * \
        modinv(states[1] - states[0], modulus) % modulus
    return crack_unknown_increment(states, modulus, multiplier)

def crack_unknown_modulus(states):
    diffs = [s1 - s0 for s0, s1 in zip(states, states[1:])]
    zeroes = [t2*t0 - t1*t1 for t0, t1, t2 in zip(diffs, diffs[1:], diffs[2:])]
    modulus = abs(reduce(gcd, zeroes))
    return crack_unknown_multiplier(states, modulus)

from pwn import *
r = remote("chall.nitdgplug.org", 30205)
f=True
r.recvuntil("N : ")
N = int(r.recvline())
e = 3
while f:
    print("trying again")
    states=[]
    c=[]
    for i in range(6):
        r.recvuntil('$')
        r.sendline('1')
        r.recvuntil('state for you: ')
        states.append(int(r.recvline()))
    try:
        n2,m,inc = crack_unknown_modulus(states)
        if n2 and m and inc:
            f=False
            for i in range(2):
                r.recvuntil('$')
                r.sendline('2')
                r.recvuntil('ENC(flag+next_num): ')
                c.append(int(r.recvline()))
            print(states,N,e,c)
    except:
        pass
result:
[+] Opening connection to chall.nitdgplug.org on port 30205: Done
trying again
trying again
[108975727830538892617, 278185160691479963824, 23703819031623371750, 399873553823252251006, 36854522988099535972, 200589524030510057086] 93397877088234975669426812750253874604273648965336132258949415214983960571228069029059775158770783769343821069606174832107364154205553242117234360035542323720271986081103664482432362702055056448070594650898390587779454665659948325803727681488553591161753988239113018768561100981202799691721869001450238781309 3 [59597650636516543026105780903488200228025821589012408887779417205216234746897685579785622284564117845815935756489380804902821918886646375836429398987715091859020566612248099247520798249051467382128526647375308969591172098171881738624144031210808618493408689379235316282325355466856483929339214121809436263373, 16101336800969255157664374270204837423475336507560060771479222499716594640297544954400598810238194904809379617820366574086826693103899992762765531540762967372198693950400894571956663271831629156790489241602491717260112834539700989671635578434127582872806492466847199888806579686176398139593540340930098264409]
```
finally, now we can run the same script and get the flag

```python
from functools import *
from math import gcd
from Crypto.Util.number import long_to_bytes as ltb


def egcd(a, b):
    if a == 0:
        return (b, 0, 1)
    else:
        g, x, y = egcd(b % a, a)
        return (g, y - (b // a) * x, x)


def modinv(b, n):
    g, x, _ = egcd(b, n)
    if g == 1:
        return x % n


def crack_unknown_increment(states, modulus, multiplier):
    increment = (states[1] - states[0]*multiplier) % modulus
    return modulus, multiplier, increment


def crack_unknown_multiplier(states, modulus):
    multiplier = (states[2] - states[1]) * \
        modinv(states[1] - states[0], modulus) % modulus
    return crack_unknown_increment(states, modulus, multiplier)


def crack_unknown_modulus(states):
    diffs = [s1 - s0 for s0, s1 in zip(states, states[1:])]
    zeroes = [t2*t0 - t1*t1 for t0, t1, t2 in zip(diffs, diffs[1:], diffs[2:])]
    modulus = abs(reduce(gcd, zeroes))
    return crack_unknown_multiplier(states, modulus)


states = [108975727830538892617, 278185160691479963824, 23703819031623371750, 399873553823252251006, 36854522988099535972, 200589524030510057086]
n2, m, c = crack_unknown_modulus(states)
state = states[-1]
nexstate = ((state*m)+c) % n2

# I don't have a python sage libary so I have to run it separately on the sagecell server
padded_text = 0x446f20796f75207265616c6c79207468696e6b204c43472077697468205253412077696c6c206d616b65207365637572652073797374656d2062747720666c616720697320474c55477b6e34346d5f6c336b335f6b34346d5f343135315f3530757263335f373372336c51610d20d4b30e8b
print(ltb(padded_text-nexstate))

result:
b'Do you really think LCG with RSA will make secure system btw flag is GLUG{n44m_l3k3_k44m_4151_50urc3_73r3_bh41_k1}'
```

Flag : GLUG{n44m_l3k3_k44m_4151_50urc3_73r3_bh41_k1}
