# Laporan Resmi Modul 3 

## Soal 1

**Membuat topologi berdasarkan gambar dibawah**

  <img src="Img/topologi.png" width="600px">

- Membuat switch
  ```
  # Switch
  uml_switch -unix switch1 > /dev/null < /dev/null &
  uml_switch -unix switch2 > /dev/null < /dev/null &
  uml_switch -unix switch3 > /dev/null < /dev/null &
  ```

- Menjadikan SURABAYA sebagai router
  ```
  # Router
  xterm -T SURABAYA -e linux ubd0=SURABAYA,jarkom umid=SURABAYA eth0=tuntap,,,10.151.70.29 eth1=daemon,,,switch1 eth2=daemon,,,switch3 eth3=daemon,,,switch2 mem=256M &
  ```
- Menjadikan MALANG, MOJOKERTO, dan TUBAN sebagai server
  ```
  # Server
  xterm -T MALANG -e linux ubd0=MALANG,jarkom umid=MALANG eth0=daemon,,,switch2 mem=160M &
  xterm -T MOJOKERTO -e linux ubd0=MOJOKERTO,jarkom umid=MOJOKERTO eth0=daemon,,,switch2 mem=128M &
  xterm -T TUBAN -e linux ubd0=TUBAN,jarkom umid=TUBAN eth0=daemon,,,switch2 mem=128M &
  ```
- Menjadikan GRESIK,SIDOARJO,BANYUWANGI, dan MADIUN sebagai client
  ```
  # Klien
  xterm -T SIDOARJO -e linux ubd0=SIDOARJO,jarkom umid=SIDOARJO eth0=daemon,,,switch1 mem=64M &
  xterm -T GRESIK -e linux ubd0=GRESIK,jarkom umid=GRESIK eth0=daemon,,,switch1 mem=64M &
  xterm -T BANYUWANGI -e linux ubd0=BANYUWANGI,jarkom umid=BANYUWANGI eth0=daemon,,,switch1 mem=64M &
  xterm -T MADIUN -e linux ubd0=MADIUN,jarkom umid=MADIUN eth0=daemon,,,switch1 mem=64M &
  ```
- Masukan semua sintaks diatas kedalam topologi.sh dengan menggunakan perintah nano ```topologi.sh```
  
  <img src="Img/soal1-1.png" width="600px">

- Bash topologi.sh dan lakukan setting sysctl pada SURABAYA 

  Jalankan perintah ```nano /etc/sysctl.conf```, hilangkan tanda pagar pada ```net.ipv4.    ip_forward=1```.

  <img src="Img/soal1-2.png" width="600px">

  Jalankan perintah ```sysctl -p``` untuk mengaktifkan perubahan.

  <img src="Img/soal1-3.png" width="600px">

- Atur interfaces pada setiap uml

  **UML SURABAYA**
  ```
  auto lo
  iface lo inet loopback

  auto eth0
  iface eth0 inet static
  address 10.151.70.30
  netmask 255.255.255.252
  gateway 10.151.70.29

  auto eth1
  iface eth1 inet static
  address 192.168.0.1
  netmask 255.255.255.248

  auto eth2
  iface eth2 inet static
  address 192.168.1.1
  netmask 255.255.255.0

  auto eth3
  iface eth3 inet static
  address 10.151.71.57
  netmask 255.255.255.0
  ```
  <img src="Img/soal1-4.png" width="600px">

  **UML TUBAN**
  ```
  auto lo
  iface lo inet loopback

  auto eth0
  iface eth0 inet static
  address 10.151.71.60
  netmask 255.255.255.248
  gateway 10.151.71.57
  ```
  <img src="Img/soal1-5.png" width="600px">

  **UML MOJOKERTO**
  ```
  auto lo
  iface lo inet loopback

  auto eth0
  iface eth0 inet static
  address 10.151.71.59
  netmask 255.255.255.248
  gateway 10.151.71.57
  ```
  <img src="Img/soal1-6.png" width="600px">

  **UML MALANG**
  ```
  auto lo
  iface lo inet loopback

  auto eth0
  iface eth0 inet static
  address 10.151.71.58
  netmask 255.255.255.248
  gateway 10.151.71.57
  ```
  <img src="Img/soal1-7.png" width="600px">

  **UML GRESIK**
  ```
  auto lo
  iface lo inet loopback

  auto eth0
  iface eth0 inet dhcp
  ```
  <img src="Img/soal1-8.png" width="600px">

  **UML SIDOARJO**
  ```
  auto lo
  iface lo inet loopback

  auto eth0
  iface eth0 inet dhcp
  ```
  <img src="Img/soal1-9.png" width="600px">

  **UML BANYUWANGI**
  ```
  auto lo
  iface lo inet loopback

  auto eth0
  iface eth0 inet dhcp
  ```
  <img src="Img/soal1-10.png" width="600px">

  **UML MADIUN**
  ```
  auto lo
  iface lo inet loopback

  auto eth0
  iface eth0 inet dhcp
  ```
  <img src="Img/soal1-11.png" width="600px">

- Lakukan perintah ```service networking restart``` dan lakukan iptables dengan menjalankan perintah ```iptables –t nat –A POSTROUTING –o eth0 –j MASQUERADE –s 192.168.0.0/16``` pada UML SURABAYA
  
## Soal 2

**Membuat SURABAYA sebagai DHCP Relay**

- TUBAN digunakan sebagai DHCP Server, maka lakukan install DHCP di TUBAN dengan menggunakan perintah ```apt-get install isc-dhcp-server```, jangan lupa lakukan ```apt-get update``` sebelum menginstall sesuatu

- Lakukan pengatturan pada DHCP Server menggunakan perintah ```nano /etc/default/isc-dhcp-server```, masukan ```eth0`` pada interfaces

  <img src="Img/soal2-1.png" width="600px">

- SURABAYA digunakan sebagai DHCP Relay, maka lakukan install DHCP Relay di SURABAYA dengan menggunakan perintah ```apt-get install isc-dhcp-relay```, jangan lupa lakukan ```apt-get update``` sebelum menginstall sesuatu

- Lakukan pengatturan pada DHCP Relay menggunakan perintah ```nano /etc/default/isc-dhcp-relay```, masukan IP TUBAN pada Servers dan ```eth1 eth2 eth3`` pada interfaces.

  <img src="Img/soal2-2.png" width="600px">

- Kembali ke TUBAN, lakukan pengaturan pada ```dhcpd.conf``` dengan menggunakan perintah ```nano /etc/dhcp/dhcpd.conf```, tambahkan NID_DMZ dan masukan IP SURABAYA sebagai option routers agar dhcp relay bisa berjalan

  <img src="Img/soal2-3.png" width="600px">

- Ubah interfaces pada klien menjadi dhcp jika interfaces pada klien masih static, jangan lupa lakukan ```service networking restart``` setelah mengubah interfaces 

  **UML GRESIK**

  <img src="Img/soal1-8.png" width="600px">

  **UML SIDOARJO**

  <img src="Img/soal1-9.png" width="600px">

  **UML BANYUWANGI**

  <img src="Img/soal1-10.png" width="600px">

  **UML MADIUN**

  <img src="Img/soal1-11.png" width="600px">

## Soal 3

**Meminjamkan IP kepada subnet 1 dengan range IP dari 192.168.0.10 sampai 192.168.0.100 dan 192.168.0.110 sampai 192.168.0.200**

- Lakukan pengaturan ```dhcpd.conf``` pada TUBAN dengan menggunakan perintah ```nano /etc/dhcp/dhcpd.conf```, tambahkan script dibawah ini

  ```
  subnet 192.168.0.0 netmask 255.255.255.0 {
    range 192.168.0.10 192.168.0.100;
    range 192.168.0.110 192.168.0.200;
    option domain-name-servers 202.46.129.2;
    default-lease-time 300;
    max-lease-time 300;
  }
  ```
  <img src="Img/soal3-1.png" width="600px">

- Restart service ```isc-dhcp-server``` menggunakan perintah ```service isc-dhcp-server restart```, lalu cek ip pada klien yang berada di subnet 1 untuk memastikan IP berhasil dipinjamkan

  **UML GRESIK**

  <img src="Img/soal3-2.png" width="600px">

  **UML SIDOARJO**

  <img src="Img/soal3-3.png" width="600px">

## Soal 4

**Meminjamkan IP kepada subnet 3 dengan range IP dari 192.168.1.50 sampai 192.168.1.70**

- Lakukan pengaturan ```dhcpd.conf``` pada TUBAN dengan menggunakan perintah ```nano /etc/dhcp/dhcpd.conf```, tambahkan script dibawah ini

  ```
  subnet 192.168.1.0 netmask 255.255.255.0 {
    range 192.168.1.50 192.168.1.70;
    option domain-name-servers 202.46.129.2;
    default-lease-time 600;
    max-lease-time 600;
  }
  ```
  <img src="Img/soal4-1.png" width="600px">

- Restart service ```isc-dhcp-server``` menggunakan perintah ```service isc-dhcp-server restart```, lalu cek ip pada klien yang berada di subnet 3 untuk memastikan IP berhasil dipinjamkan

  **UML BANYUWANGI**

  <img src="Img/soal4-2.png" width="600px">

  **UML MADIUN**

  <img src="Img/soal4-3.png" width="600px">

## Soal 5

**Membuat client menerima DNS Malang dan DNS 202.46.129.2 dari DHCP**

- Lakukan pengaturan ```dhcpd.conf``` pada TUBAN dengan menggunakan perintah ```nano /etc/dhcp/dhcpd.conf```, tambahkan IP MALANG pada option domain-name-servers

  <img src="Img/soal5-1.png" width="600px">

- Restart service ```isc-dhcp-server``` menggunakan perintah ```service isc-dhcp-server restart```, lalu lakukan pengecekan pada setiap uml menggunakan perintah ```cat /etc/resolv.conf``` untuk memastikan DNS Malang dan DNS 202.46.129.2 dari DHCP sudah diterima

  **UML GRESIK**

  <img src="Img/soal5-5.png" width="600px">

  **UML SIDOARJO**

  <img src="Img/soal5-4.png" width="600px">

  **UML BANYUWANGI**

  <img src="Img/soal5-3.png" width="600px">

  **UML MADIUN**

  <img src="Img/soal5-2.png" width="600px">

## Soal 6

**Mengatur waktu peminjaman IP, client di subnet 1 mendapatkan peminjaman alamat IP selama 5 menit, sedangkan client pada subnet 3 mendapatkan peminjaman IP selama 10 menit**

- Lakukan pengaturan ```dhcpd.conf``` pada TUBAN dengan menggunakan perintah ```nano /etc/dhcp/dhcpd.conf```, ubah lease-time pada subnet 1 menjadi 300 dan lease-time pada subnet 3 menjadi 600 (waktu dikonversikan menjadi detik)

  <img src="Img/soal6-1.png" width="600px">

- Restart service ```isc-dhcp-server``` menggunakan perintah ```service isc-dhcp-server restart```.

## Soal 7

**User authentication untuk Anri**

- Pada UML Mojokerto (proxy server) install squid dan apache2-utils
  ```
  apt-get install squid
  apt-get install apache2-utils
  ```
- Setup username dan password\
  User: userta_e06\
  Password: inipassw0rdta_e06
  ```
  htpasswd -c /etc/squid/passwd userta_e06
  ```
  <img src="Img/soal7.PNG" width="600px">
  
- Ubah konfigurasi squid
  ```
  nano /etc/squid/squid.conf
  ```
  ```
  http_port 8080
  visible_hostname mojokerto

  auth_param basic program /usr/lib/squid/basic_ncsa_auth /etc/squid/passwd
  auth_param basic children 5
  auth_param basic realm Proxy
  auth_param basic credentialsttl 2 hours
  auth_param basic casesensitive on
  acl USERS proxy_auth REQUIRED
  http_access allow USERS
  ```
  
## Soal 8 dan 9

**Filtering jadwal pengaksesan**

- Mendefinisikan jadwal yang tidak restricted. Dalam soal 8 dan 9, yang tidak restricted adalah:
  + Selasa 13:00 - 18:00, 21:00 - 23:59
  + Rabu 00:00 - 09:00, 13:00 - 18:00, 21:00 - 23:59
  + Kamis 00:00 - 09:00, 21:00 - 23:59
  + Jumat 00:00 - 09:00
  
  <img src="Img/soal89.PNG" width="600px">

- Menambah konfigurasi squid
  ```
  nano /etc/squid/squid.conf
  ```
  ```
  include /etc/squid/acl.conf
  http_access allow work 
  http_access allow bimbingan1
  http_access allow bimbingan2
  http_access deny all
  ```
  
## Soal 10

**Redirect ke monta.if.its.ac.id setiap mengakses google.com**

- Mendefinisikan google.com sebagai website restricted
  ```
  nano /etc/squid/restrict-sites.acl
  ```
  ```
  google.com
  ```

- Menambah konfigurasi squid
  ```
  acl BLACKLISTS dstdomain "/etc/squid/restrict-sites.acl"
  deny_info http://monta.if.its.ac.id BLACKLISTS
  http_access deny BLACKLISTS
  ```

## Soal 11

**Mengganti error page default squid**

- Mendownload page ke directory error template dan extract isinya
  ```
  cd /usr/share/squid/errors/English

  rm ERR_ACCESS_DENIED

  wget 10.151.36.202/ERR_ACCESS_DENIED
  ```

- Menambah konfigurasi squid
  ```
  error_directory /usr/share/squid/errors/English
  ```
  
  <img src="Img/soal11_show.PNG" width="600px">

## Soal 12

**Setup DNS**

- Install bind9 di MALANG
- Setup konfigurasi domain janganlupa-ta.e06.pw
  ```
  nano /etc/bind/named.conf.local
  ```
  Isi dengan:
  ```
  zone "janganlupa-ta.e06.pw" {
    type master;
    file "/etc/bind/jarkom/janganlupa-ta.e06.pw";
  }
  ```
  
- Buat folder /etc/bind/jarkom
  ```
  mkdir /etc/bind/jarkom
  cp /etc/bind/db.local /etc/bind/jarkom/janganlupa-ta.e06.pw
  ```
  
- Edit konfigurasi bind untuk domain janganlupa-ta.e06.pw
  ```
  nano /etc/bind/jarkom/janganlupa-ta.e06.pw
  ```
  Isi dengan:
  
  <img src="Img/soal12_2.PNG" width="600px">
  
- Ubah HTTP Proxy di browser menjadi seperti berikut (IP MOJOKERTO diganti dengan janganlupa-ta.e06.pw)
  <img src="Img/soal12_3.PNG" width="600px">
  
- Browser dapat mengakses internet dengan HTTP Proxy yang sudah diubah
  <img src="Img/soal12_4.png" width="600px">
