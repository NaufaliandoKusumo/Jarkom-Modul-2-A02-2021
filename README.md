# Jarkom-Modul-2-A02-2021

#### 1. EniesLobby akan dijadikan sebagai DNS Master, Water7 akan dijadikan DNS Slave, dan Skypie akan digunakan sebagai Web Server. Terdapat 2 Client yaitu Loguetown, dan Alabasta. Semua node terhubung pada router Foosha, sehingga dapat mengakses internet.
**Membuat Topologi**<br/>
Tambahkan beberapa node ethernet switch dan ubuntu, lalu buat hubungan antar node dan nama-nama dari node dan Merubah nama-nama dari node dengan `Change hostname`.<br/>
![create-topology-1](https://i.postimg.cc/Gth3jhhJ/image.png)<br/>
Lalu setting network masing-masing node dengan fitur `Edit network configuration` dengan settingan:
  - Foosha
  ```
  auto eth0
  iface eth0 inet dhcp
  auto eth1
  iface eth1 inet static
  	address 10.0.1.1
  	netmask 255.255.255.0
  auto eth2
  iface eth2 inet static
  	address 10.0.2.1
  	netmask 255.255.255.0
  ```
  - Loguetown
  ```
  auto eth0
  iface eth0 inet static
  	address 10.0.1.2
  	netmask 255.255.255.0
  	gateway 10.0.1.1
  ```
  - Alabasta
  ```
  auto eth0
  iface eth0 inet static
  	address 10.0.1.3
  	netmask 255.255.255.0
  	gateway 10.0.1.1
  ```
  - EniesLobby
  ```
  auto eth0
  iface eth0 inet static
  	address 10.0.2.2
  	netmask 255.255.255.0
  	gateway 10.0.2.1
  ```
  - Water7
  ```
  auto eth0
  iface eth0 inet static
  	address 10.0.2.3
  	netmask 255.255.255.0
  	gateway 10.0.2.1
  ```
  - Skypie
  ```
  auto eth0
  iface eth0 inet static
    address 10.0.2.4
    netmask 255.255.255.0
    gateway 10.0.2.1
  ```
Ketik `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.181.0.0/16`, lalu command `cat /etc/resolv.conf` pada router `Foosha`.<br/>
Ketik `echo nameserver 192.168.122.1 > /etc/resolv.conf` pada node ubuntu yang lain.<br/>
Restart semua node. Untuk mencoba bahwa node dapat mengakses internet, ketik `ping google.com`. Berikut hasilnya yang dicoba pada `Loguetown`.<br/>
![image1](https://i.postimg.cc/Y9tLJ7Cj/image.png)

#### 2. Luffy ingin menghubungi Franky yang berada di EniesLobby dengan denden mushi. Kalian diminta Luffy untuk membuat website utama dengan mengakses franky.yyy.com dengan alias www.franky.yyy.com pada folder kaizoku. 

#### 3. Setelah itu buat subdomain super.franky.yyy.com dengan alias www.super.franky.yyy.com yang diatur DNS nya di EniesLobby dan mengarah ke Skypie. 

#### 4. Buat juga reverse domain untuk domain utama. 

#### 5. Supaya tetap bisa menghubungi Franky jika server EniesLobby rusak, maka buat Water7 sebagai DNS Slave untuk domain utama. 

#### 6. Setelah itu terdapat subdomain mecha.franky.yyy.com dengan alias www.mecha.franky.yyy.com yang didelegasikan dari EniesLobby ke Water7 dengan IP menuju ke Skypie dalam folder sunnygo. 

#### 7. Untuk memperlancar komunikasi Luffy dan rekannya, dibuatkan subdomain melalui Water7 dengan nama general.mecha.franky.yyy.com dengan alias www.general.mecha.franky.yyy.com yang mengarah ke Skypie. 
