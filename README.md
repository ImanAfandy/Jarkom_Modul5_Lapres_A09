# Jarkom_Modul4_Lapres_A09

Kelompok A09:

    Iman Afandy (05111740000129)
    Nodas Uziel Putra Serpara (5111840007007)
    
 # A. Topologi 
 
 <img src="topologi.png" width="600">
 
 Isi topologi.sh : 
 
``````````` 
 # Switch
uml_switch -unix switch1 > /dev/null < /dev/null &
uml_switch -unix switch2 > /dev/null < /dev/null &
uml_switch -unix switch3 > /dev/null < /dev/null &
uml_switch -unix switch4 > /dev/null < /dev/null &
uml_switch -unix switch5 > /dev/null < /dev/null &
uml_switch -unix switch6 > /dev/null < /dev/null &

# Router
xterm -T SURABAYA -e linux ubd0=SURABAYA,jarkom umid=SURABAYA eth0=tuntap,,,10.151.78.33 eth1=daemon,,,switch5 eth2=daemon,,,switch3 mem=96M &
xterm -T KEDIRI -e linux ubd0=KEDIRI,jarkom umid=KEDIRI eth0=daemon,,,switch1 eth1=daemon,,,switch6 eth2=daemon,,,switch5  mem=96M &
xterm -T BATU -e linux ubd0=BATU,jarkom umid=BATU eth0=daemon,,,switch3 eth1=daemon,,,switch4 eth2=daemon,,,switch2  mem=96M &

# Server
xterm -T MALANG -e linux ubd0=MALANG,jarkom umid=MALANG eth0=daemon,,,switch2 mem=128M &
xterm -T MOJOKERTO -e linux ubd0=MOJOKERTO,jarkom umid=MOJOKERTO eth0=daemon,,,switch2 mem=128M &
xterm -T MADIUN -e linux ubd0=MADIUN,jarkom umid=MADIUN eth0=daemon,,,switch1 mem=128M &
xterm -T PROBOLINGGO -e linux ubd0=PROBOLINGGO,jarkom umid=PROBOLINGGO eth0=daemon,,,switch1 mem=128M &

# Klien
xterm -T GRESIK -e linux ubd0=GRESIK,jarkom umid=GRESIK eth0=daemon,,,switch6 mem=96M &
xterm -T SIDOARJO -e linux ubd0=SIDOARJO,jarkom umid=SIDOARJO eth0=daemon,,,switch4 mem=96M &


`````````````
# B. Subnetting


 <img src="subnetting.png" width="600">
 
Isi /etc/network/interfaces : 


<h2> SURABAYA </h2> 

````
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 10.151.72.42
netmask 255.255.255.252
gateway 10.151.72.41

auto eth1
iface eth1 inet static
address 192.168.2.1
netmask 255.255.255.252

auto eth2
iface eth2 inet static
address 192.168.5.1
netmask 255.255.255.252

`````

<h2> KEDIRI </h2>

````
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.2.1
netmask 255.255.255.248

auto eth1
iface eth1 inet static
address 192.168.0.1
netmask 255.255.255.0

auto eth2
iface eth2 inet static
address 192.168.1.2
netmask 255.255.255.252
gateway 192.168.1.1
````

<h2>BATU</h2>

````

auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.5.2
netmask 255.255.255.252
gateway 192.168.5.1

auto eth1
iface eth1 inet static
address 192.168.4.1
netmask 255.255.255.0

auto eth2
iface eth2 inet static
address 10.151.73.81
netmask 255.255.255.248

`````

<h2> MALANG </h2>

```` 
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 10.151.73.82
netmask 255.255.255.248
gateway 10.151.73.81

`````

<h2> MOJOKERTO </h2>

```
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 10.151.73.83
netmask 255.255.255.248
gateway 10.151.73.81

`````

<h2> MADIUN </h2>

`````
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.1.2
netmask 255.255.255.248
gateway 192.168.1.1

``````

<h2> PROBOLINGGO </h2>

``````

auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 192.168.1.3
netmask 255.255.255.248
gateway 192.168.1.1

````````

# C. Routing

`````
route add -net 10.151.73.80  netmask 255.255.255.248 gw 192.168.5.2
route add -net 192.168.4.0 netmask 255.255.255.0 gw 192.168.5.2
route add -net 192.168.0.0 netmask 255.255.254.0 gw 192.168.2.2
`````


# D. DHCP Server-Relay 

Karena MOJOKERTO mnejadi DHCP Server, perlu diinstallkan dengan perintah apt-get install isc-dhcp-server, sedangkan KEDIRI dan
 BATU yang akan menjadi DHCP Relay, perlu diinstallkan dengan perintah apt-get install isc-dhcp-relay.

Sedangkan untuk klien GRESIK dan SIDOARJO akan diedit file /etc/network/interfaces menjadi sebagai berikut:

auto lo
iface lo inet loopback

auto eth0
iface eth0 inet dhcp

Kemudian pada DHCP Server (MOJOKERTO), diedit pada file /etc/default/isc-dhcp-server, ditambahkan interface eth0 untuk INTERFACESv4.

Kemudian diedit juga pada file /etc/dhcp/dhcpd.conf sebagai berikut:

`````
subnet 10.151.73.81 netmask 255.255.255.248 {
}

subnet 192.168.4.0 netmask 255.255.255.0 {
	range 192.168.4.2 192.168.4.254;
	option routers 192.168.4.1;
	option broadcast-address 192.168.4.255;
	option domain-name-servers 10.151.73.82;
	default-lease-time 600;
	max-lease-time 600;
}

subnet 192.168.0.0 netmask 255.255.255.0 {
	range 192.168.0.2 192.168.0.254;
	option routers 192.168.0.1;
	option broadcast-address 192.168.0.255;
	option domain-name-servers 10.151.73.82;
	default-lease-time 600;
	max-lease-time 600;
}
`````


Kemudian DHCP dapat direstart dengan perintah service isc-dhcp-server restart.

Sedangkan untuk kedua DHCP Relay yaitu KEDIRI dan BATU akan diset untuk mendengarkan DHCP Server diberikan IP MOJOKERTO yaitu 10.151.73.83.


# 1. SURABAYA bisa akses keluar tanpa MASQUERADE

Ditambahkan perintah iptables sebagai berikut di SURABAYA:

```
iptables -t nat -A POSTROUTING -s 192.168.0.0/16 -o eth0 -j SNAT --to-source 10.151.72.40
```

# 2. Akses SSH di luar topologi (UML) akan di-DROP ketika mengakses server yang memiliki IP DMZ (lakukan setting di SURABAYA)

Ditambahkan perintah iptables sebagai berikut di SURABAYA:

````
iptables -N LOGGING
iptables -A FORWARD -p tcp --dport 22 -d 10.151.73.80/29 -i eth0 -j LOGGING
iptables -A LOGGING -m limit --limit 5/min -j LOG --log-prefix "iptables_FORWARD_denied: " --log-level 7
iptables -A LOGGING -j DROP
````

Diberikan beberapa syntax tambahan berupa logging untuk memenuhi soal no.7 yaitu melakukan logging untuk semua paket yang di-DROP. SSH memiliki nomor port 22.

# 3. DHCP Server dan DNS Server maksimal menerima 3 koneksi ICMP secara bersamaan, selebihnya di-DROP (disetting pada masing-masing server)

Ditambahkan perintah iptables sebagai berikut di MALANG (DNS Server) dan MOJOKERTO (DHCP Server):
````

iptables -N LOGGING
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j LOGGING
iptables -A LOGGING -m limit --limit 5/min -j LOG --log-prefix "iptables_FORWARD_denied: " --log-level 7
iptables -A LOGGING -j DROP
`````

Diberikan beberapa syntax tambahan berupa logging untuk memenuhi soal no.7 yaitu melakukan logging untuk semua paket yang di-DROP.

# 4-5. SIDOARJO dan GRESIK diberikan waktu akses untuk mengakses server MALANG:

SIDOARJO = 07:00 - 17:00 (Senin - Jumat) GRESIK = 17:00 - 07:00 (Setiap Hari) Ditambahkan perintah iptables sebagai berikut di MALANG:

````
iptables -A INPUT -s 192.168.4.0/24 -m time --timestart 07:00 --timestop 17:00 --weekdays Mon,Tue,Wed,Thu,Fri -j ACCEPT
iptables -A INPUT -s 192.168.0.0/24 -m time --timestart 17:00 --timestop 23:59 -j ACCEPT
iptables -A INPUT -s 192.168.0.0/24 -m time --timestart 00:00 --timestop 07:00 -j ACCEPT
iptables -A INPUT -s 192.168.0.0/24 -j REJECT
iptables -A INPUT -s 192.168.4.0/24 -j REJECT
`````


# 6. Ketika mengakses DNS Server akan secara bergantian didistribusikan ke PROBOLINGGO dan MADIUN pada port 80 (setting iptables di SURABAYA)

Ditambahkan perintah iptables sebagai berikut di SURABAYA:

````
iptables -A PREROUTING -t nat -p tcp -d 10.151.73.82 --dport 80 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 192.168.1.2
iptables -t nat -A PREROUTING -p tcp -d 10.151.73.82 --dport 80 -j DNAT --to-destination 192.168.1.3
iptables -t nat -A POSTROUTING -p tcp -d 192.168.1.2 --dport 80 -j SNAT --to-source 10.151.73.82 
iptables -t nat -A POSTROUTING -p tcp -d 192.168.1.3 --dport 80 -j SNAT --to-source 10.151.73.82 

````

Pada PROBOLINGGO dan MADIUN yang merupakan Web Server harus dilakukan instalasi apache2 dengan perintah apt-get install apache2.

Kemudian pada DNS Server yaitu MALANG akan diinstall bind9 dengan perintah apt-get install bind9. Setelah itu dilakukan edit file /etc/bind/named.conf.local dan tambahkan domain baru misalkan jarkoma09.com sebagai berikut:

````
zone "jarkoma09.com" {
    type master;
    file "/etc/bind/jarkom/jarkoma09.com";
};
````


Kemudian buat folder baru:  ```mkdir /etc/bind/jarkom ```

Dan copy file db.local ke folder yang baru saja dibuat dan mengganti namanya sesuai domain yang diinginkan: cp /etc/bind/db.local /etc/bind/jarkom/jarkoma09.com Kemudian buka file semerua09.pw dengan perintah: nano /etc/bind/jarkom/semerua09.pw.

Edit pointer A menjadi ke IP yang belum digunakan yaitu 192.168.6.1 serta ganti localhost menjadi nama domain yaitu jarkoma09.com.

Setelah itu melakukan restart service bind9 dengan perintah service bind9 restart.

Untuk mengecek, misalkan pada klien SIDOARJO dijalankan nc jarkoma09.com 80 kemudian enter dan ketik apapun misalkan tes. Kemudian pada MADIUN dan PROBOLINGGO akan dicoba lihat apakah diterima dan di mana diterimanya dengan perintah tail /var/log/apache2/access.log. Kemudian lakukan lagi, dan sekarang harusnya diterima di UML yang lainnya (bergantian), berarti berhasil.

# 7. Logging semua paket yang di-DROP

Untuk soal ini konfigurasi akan digabung bersama perintah iptables DROP yaitu untuk nomor 2 dan 3 di atas.

Karena sistem UML semua iptables akan hilang tiap direstart, sebaiknya disimpan dalam file bash script misalkan iptables.sh pada tiap UML yang disetting sebagai berikut:

 <h2> SURABAYA</h2 

````	
iptables -t nat -A POSTROUTING -s 192.168.0.0/16 -o eth0 -j SNAT --to-source 10.151.72.40
iptables -N LOGGING
iptables -A FORWARD -p tcp --dport 22 -d 10.151.73.80/29 -i eth0 -j LOGGING
iptables -A LOGGING -m limit --limit 5/min -j LOG --log-prefix "iptables_FORWARD_denied: " --log-level 7
iptables -A LOGGING -j DROP
iptables -A PREROUTING -t nat -p tcp -d 192.168.6.1 --dport 80 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 192.168.1.2
iptables -t nat -A PREROUTING -p tcp -d 192.168.6.1 --dport 80 -j DNAT --to-destination 192.168.1.3
iptables -t nat -A POSTROUTING -p tcp -d 192.168.1.2 --dport 80 -j SNAT --to-source 192.168.6.1
iptables -t nat -A POSTROUTING -p tcp -d 192.168.1.3 --dport 80 -j SNAT --to-source 192.168.6.1
`````

<h2> MALANG </h2>

````
iptables -N LOGGING
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j LOGGING
iptables -A LOGGING -m limit --limit 5/min -j LOG --log-prefix "iptables_FORWARD_denied: " --log-level 7
iptables -A LOGGING -j DROP
iptables -A INPUT -s 192.168.4.0/24 -m time --timestart 07:00 --timestop 17:00 --weekdays Mon,Tue,Wed,Thu,Fri -j ACCEPT
iptables -A INPUT -s 192.168.0.0/24 -m time --timestart 17:00 --timestop 23:59 -j ACCEPT
iptables -A INPUT -s 192.168.0.0/24 -m time --timestart 00:00 --timestop 07:00 -j ACCEPT
iptables -A INPUT -s 192.168.0.0/24 -j REJECT
iptables -A INPUT -s 192.168.4.0/24 -j REJECT
`````

<h2>MOJOKERTO</h2>

````
iptables -N LOGGING
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j LOGGING
iptables -A LOGGING -m limit --limit 5/min -j LOG --log-prefix "iptables_FORWARD_denied: " --log-level 7
iptables -A LOGGING -j DROP
`````















 
