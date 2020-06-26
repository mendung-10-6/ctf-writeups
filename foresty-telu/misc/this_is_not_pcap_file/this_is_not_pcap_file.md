## This is not pcap file (50 pts)

Pada challenge ini kita diberikan file `SkypeIRC.pcap` dan telah diberi tahu secara tidak langsung oleh title challenge bahwa itu bukan merupakan file pcap

Karena ini *mungkin* ada hubungannya dengan **magic byte** maka kita langsung saja cari magic byte untuk file **pcap**

Pada [Wikipedia](https://en.wikipedia.org/wiki/List_of_file_signatures) didapatkan seperti berikut

![image1](thisisnotpcapfile1.png)

Karena mungkin saja ini juga pcapng jadi kita coba buka dengan `ghex` atau hex editor lainnya dan cari magic byte tersebut

Dan saat pencarian dengan menggunakan magic byte yang kedua yaitu `d4 c3 b2 a1` didapatkan flag nya seperti berikut

![image2](thisisnotpcapfile2.png)