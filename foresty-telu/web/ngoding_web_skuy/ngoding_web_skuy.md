## Ngoding web skuy (50 pts)

Kita buka challege nya dan kita akan langsung dihadapi dengan sebuah website seperti berikut

![image1](ngodingwebskuy1.png)

Setelah itu kita langsung cari cari hal yang sekiranya akan menarik perhatian (*mencurigakan*)

Dan setelah dilihat lihat kita akan mendapatkan hal berikut

![image2](ngodingwebskuy3.png)

Dia mengatakan flag nya ada di style maka kita langsung saja lihat pada style nya dengan inspect element

![image2](ngodingwebskuy4.png)

Ternyata style nya berada pada ```/style/style.css```

Maka kita langsung saja masuk pada link itu dan kita cari kembali hal yang *interesting*

![image2](ngodingwebskuy5.png)

Digambar terlihat ada sebuah text yang kita sudah pasti biasa melihat dan bisa langsung *recognize* bahwa itu adalah **base64** encrypted text maka kita langsung saja decrypt

```bash
echo 'Rm9yZXN0eTEwMX' | base64 -d > flag.txt
```

Bisa kita lihat pada `flag.txt` dan mungkin flag nya belum selesai(?) maka dari itu kita kembali melihat pada source di inspect element, dan kita akan dapatkan hal menarik seperti berikut

![image2](ngodingwebskuy2.png)

Terlihat ada source file lainnya yaitu `/style/script.js`

![image2](ngodingwebskuy6.png)

Setelah kita buka dan lihat maka kita akan menemukan **base64** encrypted text lainnya, lakukan hal yang sama dan kita dapatkan seluruh flag nya

```bash
echo 'X3RpZGFrX2FkYV95Z19hbWFu' | base64 -d > flag.txt
```

> Masukkan flag nya dengan wrapper **Foresty2019{}**