# Jarkom_Modul5_Lapres_B02

**Pembagian Kelas**

![](.//media/image1.png)

**Treenya**

![](.//media/image2.png)

**Topologi**

Atur interface

![](.//media/image3.png)

![](.//media/image4.png)

![](.//media/image5.png)

![](.//media/image6.png)

![](.//media/image7.png)

![](.//media/image8.png)

![](.//media/image9.png)

![](.//media/image10.png)

![](.//media/image11.png)

Buat routing di surabaya

![](.//media/image12.png)

uncomment net.ipv4.ip_forward=1 /etc/sysctl.conf, lalu sysctl -p di
semua router

Atur dhcp server dan relay

![](.//media/image13.png)

![](.//media/image14.png)

![](.//media/image15.png)

![](.//media/image16.png)

![](.//media/image17.png)

![](.//media/image18.png)

**SOAL**

1.  **Agar topologi yang kalian buat dapat mengakses keluar, kalian
    diminta untuk mengkonfigurasi SURABAYA menggunakan iptables, namun
    Bibah tidak ingin kalian menggunakan MASQUERADE.**

> *iptables -t nat -A POSTROUTING -s 192.168.0.0/16 -o eth0 -j SNAT
> \--to-source 10.151.74.14*
>
> ![](.//media/image19.png)

2.  **Kalian diminta untuk mendrop semua akses SSH dari luar Topologi
    (UML) Kalian pada server yang memiliki ip DMZ (DHCP dan DNS SERVER)
    pada SURABAYA demi menjaga keamanan.**

Install openssh-server di surabaya, malang, mojokerto

> tes ssh
>
> ![](.//media/image20.png)
>
> **Iptables di surabaya**
>
> *iptables -A FORWARD -d 10.151.83.24/29 -i eth0 -p tcp \--dport 22 -j
> DROP*
>
> ![](.//media/image21.png)
>
> Tes dengan melakukan nc dari terminal kelompok
>
> ![](.//media/image22.png)

3.  **Karena tim kalian maksimal terdiri dari 3 orang, Bibah meminta
    kalian untuk membatasi DHCP dan DNS server hanya boleh menerima
    maksimal 3 koneksi ICMP secara bersamaan yang berasal dari mana saja
    menggunakan iptables pada masing masing server, selebihnya akan di
    DROP.**

> *iptables -A INPUT -p icmp -m connlimit \--connlimit-above 3
\--connlimit-mask 0 -j DROP*

> ![](.//media/image23.png)
>
> ![](.//media/image24.png)
>
> Ketika ada lebih dari 3 koneksi icmp ke mojokerto
>
> ![](.//media/image25.png)

4.  **Akses dari subnet SIDOARJO hanya diperbolehkan pada pukul 07.00 -
    17.00 pada hari Senin sampai Jumat.**

> *iptables -A INPUT -s 192.168.1.0/24 -m time \--timestart 07:00
> \--timestop 17:00 \--days Mon,Tue,Wed,Thu,Fri -j ACCEPT*
>
> *iptables -A INPUT -s 192.168.1.0/24 -j REJECT*
>
> ![](.//media/image26.png)
>
> Tes dengan ping dari sidoarjo ke malang di jam tertentu
>
> ![](.//media/image27.png)
>
> ![](.//media/image28.png)

5.  **Akses dari subnet GRESIK hanya diperbolehkan pada pukul 17.00
    hingga pukul 07.00 setiap harinya.**

> *iptables -A INPUT -s 192.168.2.0/24 -m time \--timestart 17:00
\--timestop 23:59 -j ACCEPT*

> *iptables -A INPUT -s 192.168.2.0/24 -m time \--timestart 00:00
> \--timestop 07:00 -j ACCEPT*
>
> *iptables -A INPUT -s 192.168.2.0/24 -j REJECT*
>
> ![](.//media/image29.png)
>
> Tes dengan ping malang dari gresik
>
> ![](.//media/image30.png)
>
> ![](.//media/image31.png)

6.  **Bibah ingin SURABAYA disetting sehingga setiap request dari client
    yang mengakses DNS Server akan didistribusikan secara bergantian
    pada PROBOLINGGO port 80 dan MADIUN port 80.**

> *iptables -t nat -A PREROUTING -p tcp -d 10.151.83.26 -m statistic
> \--mode nth \--every 2 \--packet 0 -j DNAT \--to-destination
> 192.168.0.11:80*
>
> *iptables -t nat -A PREROUTING -p tcp -d 10.151.83.26 -j DNAT
> \--to-destination 192.168.0.10:80*
>
> ![](.//media/image32.png)
>
> Tes dengan nc ke ip malang
>
> ![](.//media/image33.png)

7.  **Bibah ingin agar semua paket didrop oleh firewall (dalam topologi)
    tercatat dalam log pada setiap UML yang memiliki aturan drop.**

> *iptables -N LOGGING*

> *iptables -A INPUT -j LOGGING*
>
> *iptables -A OUTPUT -j LOGGING*
>
> *iptables -A LOGGING -j LOG \--log-prefix \"IPTables-Dropped: \"
> \--log-level 4*
>
> *iptables -A LOGGING -j DROP*
>
> ![](.//media/image34.png)
>
> Tes dengan melakukan ping dari UML lain
>
> ![](.//media/image35.png)
