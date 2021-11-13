# Jarkom-Modul-3-TI4-2021

**Oleh:**
  * Muhammad Nur Fauzan (05311940000035)
  * Ghimnastiar Al Abiyyuna (05311940000042)
  * Christopher Benedict (05311840000024)

---

## **Soal 1 & 2**

**1. Luffy bersama Zoro berencana membuat peta tersebut dengan kriteria EniesLobby sebagai DNS Server, Jipangu sebagai DHCP Server, Water7 sebagai Proxy Server. Lalu Foosha sebagai DHCP Relay**

![image](https://user-images.githubusercontent.com/73151866/141642244-dcf45bcf-8c64-4757-8768-0dd3d8a47e2d.png)

## **Soal 3 - 6**

**3. Luffy dan Zoro menyusun peta tersebut dengan hati-hati dan teliti. Ada beberapa kriteria yang ingin dibuat oleh Luffy dan Zoro, yaitu:

- Semua client yang ada HARUS menggunakan konfigurasi IP dari DHCP Server, Client yang melalui Switch1 mendapatkan range IP dari [prefix IP].1.20 - [prefix IP].1.99 dan [prefix IP].1.150 - [prefix IP].1.169.
- Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.30 - [prefix IP].3.50.
- Client mendapatkan DNS dari EniesLobby dan client dapat terhubung dengan internet melalui DNS tersebut.
- Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 6 menit sedangkan pada client yang melalui Switch3 selama 12 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 120 menit.**

```
#switch1
subnet 192.213.1.0 netmask 255.255.255.0 {
        range 192.213.1.20 192.213.1.99;
        range 192.213.1.150 192.213.1.169;
        option routers 192.213.1.1;
        option broadcast-address 192.213.1.255;
        option domain-name-servers 192.213.2.2;
        default-lease-time 360;
        max-lease-time 7200;
}

#switch3
subnet 192.213.3.0 netmask 255.255.255.0 {
        range 192.213.3.30 192.213.3.50;
        option routers 192.213.3.1;
        option broadcast-address 192.213.3.255;
        option domain-name-servers 192.213.2.2;
        default-lease-time 720;
        max-lease-time 7200;
}
#switch2
subnet 192.213.2.0 netmask 255.255.255.0 {
}
```

## **Soal7**

**7. Luffy dan Zoro berencana menjadikan Skypie sebagai server untuk jual beli kapal yang dimilikinya dengan alamat IP yang tetap dengan IP [prefix IP].3.69.**

```
host Skypie {
    hardware ethernet 6e:75:7d:46:c3:ef;
    fixed-address 192.213.3.69;
}
```

## **Soal8**

**8. Loguetown digunakan sebagai client Proxy agar transaksi jual beli dapat terjamin keamanannya, juga untuk mencegah kebocoran data transaksi. Pada Loguetown, proxy harus bisa diakses dengan nama jualbelikapal.yyy.com dengan port yang digunakan adalah 5000.**

EniesLobby :
```
zone "jualbelikapal.TI4.com" {
type master;
file "/etc/bind/kaizoku/jualbelikapal.TI4.com";
};
```
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     jualbelikapal.TI4.com. root.jualbelikapal.TI4.com. (
                    2         ; Serial
                604800         ; Refresh
                86400         ; Retry
                2419200         ; Expire
                604800 )       ; Negative Cache TTL
;
@       IN      NS      jualbelikapal.TI4.com.
@       IN      A       192.213.2.3
```
Water7
```
http_port 5000
visible_hostname Water7
```

## **Soal9**

**9. Agar transaksi jual beli lebih aman dan pengguna website ada dua orang, proxy dipasang autentikasi user proxy dengan enkripsi MD5 dengan dua username, yaitu luffybelikapalyyy dengan password luffy_yyy dan zorobelikapalyyy dengan password zoro_yyy.**
```
auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd
auth_param basic children 5
auth_param basic realm Proxy
auth_param basic credentialsttl 2 hours
auth_param basic casesensitive on
acl USERS proxy_auth REQUIRED
http_access allow USERS AVAILABLE_WORKING
```
```
htpasswd -cbm /etc/squid/passwd luffybelikapalTI4 luffy_TI4
htpasswd -bm /etc/squid/passwd zorobelikapalTI4 zoro_TI4
```

## **Soal10**

**10. Transaksi jual beli tidak dilakukan setiap hari, oleh karena itu akses internet dibatasi hanya dapat diakses setiap hari Senin-Kamis pukul 07.00-11.00 dan setiap hari Selasa-Jum’at pukul 17.00-03.00 keesokan harinya (sampai Sabtu pukul 03.00)**
```
acl AVAILABLE_WORKING time MTWH 07:00-11:00
acl AVAILABLE_WORKING time TWHF 17:00-23:59
acl AVAILABLE_WORKING time WHFA 00:00-03:00
```
```
include /etc/squid/acl.conf
```


## **Soal11**

**11. Agar transaksi bisa lebih fokus berjalan, maka dilakukan redirect website agar mudah mengingat website transaksi jual beli kapal. Setiap mengakses google.com, akan diredirect menuju super.franky.yyy.com dengan website yang sama pada soal shift modul 2. Web server super.franky.yyy.com berada pada node Skypie.**
```
ServerAlias google.com
```
```
acl lan src 192.213.3.0/24 192.213.1.0/24
acl badsites dstdomain google.com
deny_info http://super.franky.TI4.com lan
http_reply_access deny badsites lan
dns_nameservers 192.213.2.2
```


## **Soal 12 & 13**

**12. Saatnya berlayar! Luffy dan Zoro akhirnya memutuskan untuk berlayar untuk mencari harta karun di super.franky.yyy.com. Tugas pencarian dibagi menjadi dua misi, Luffy bertugas untuk mendapatkan gambar (.png, .jpg), sedangkan Zoro mendapatkan sisanya. Karena Luffy orangnya sangat teliti untuk mencari harta karun, ketika ia berhasil mendapatkan gambar, ia mendapatkan gambar dan melihatnya dengan kecepatan 10 kbps**
```
acl multimedia url_regex -i \.png$ \.jpg$
acl bar proxy_auth luffybelikapalTI4
delay_pools 1
delay_class 1 1
delay_parameters 1 1250/3200
delay_access 1 allow multimedia bar
delay_access 1 deny ALL
```

## **Extra**
tambahkan http_access deny ALL untuk menutup file pada /etc/squid/squid.conf







