# Typeracer  
if we take a look at the page source, we can see obf.js, inside it was a very obfuscated code, using [this tool](https://beautifier.io/) we can see the code much clearer. Let's take a look at some interesting part of the code.  
```javascript
'_0x1ec2f5': _0x7379('0x26f', 'QvUT'),
        '_0x1c2030': _0x7379('0x20', 'zyUu') + 'ou have be' + _0x7379('0x4c4', '9f2)') + _0x7379('0x570', 'Y@$M') + _0x7379('0x505', 'XVoV') + 'iQ1RGe3c0M' + _0x7379('0x649', '*a95') + _0x7379('0x55a', 'nB&s') + 'l80bm4weTF' + _0x7379('0x143', '&H2q'),
        '_0x1e6fbe': function(_0x2a6b00, _0x43d36c) {
            return _0x2a6b00 + _0x43d36c;
        },
        '_0x141f90': _0x7379('0x3cb', '*f#2') + _0x7379('0x4eb', '%7!)') + 'y keyboard' + _0x7379('0x22a', 'M9PA') + _0x7379('0x191', 'Y@$M') + 'em the fla' + 'g. Perhaps' + _0x7379('0x120', 'T2UE') + 'n?',
        '_0x8fd743': 'wpm',
        '_0x4ea8ac': _0x7379('0x566', 'I7*N'),
```  
we can see there is some strings like `em flag. Perhaps` that looks like the message that we got after we finished the typing challenge, we can confirm this by running it on the javascript console. As you continue running some part of the code around it that's actually the congratulations message.  
`_0x7379('0x20', 'zyUu') + 'ou have be' + _0x7379('0x4c4', '9f2)') + _0x7379('0x570', 'Y@$M') + _0x7379('0x505', 'XVoV') + 'iQ1RGe3c0M' + _0x7379('0x649', '*a95') + _0x7379('0x55a', 'nB&s') + 'l80bm4weTF' + _0x7379('0x143', '&H2q')`  
which translates to  
`Congrats you have beaten me! Here's your flag: cmdiQ1RGe3c0MHdfajR2NDJjcjFwN18xMl80bm4weTFuZ30=`  
decode the base64 and we got the flag.  
flag : `rgbCTF{w40w_j4v42cr1p7_12_4nn0y1ng}`