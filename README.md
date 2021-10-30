# Jarkom-Modul-2-A02-2021

#### 1. EniesLobby akan dijadikan sebagai DNS Master, Water7 akan dijadikan DNS Slave, dan Skypie akan digunakan sebagai Web Server. Terdapat 2 Client yaitu Loguetown, dan Alabasta. Semua node terhubung pada router Foosha, sehingga dapat mengakses internet.
**Membuat Topologi**<br/>
- Tambahkan beberapa node ethernet switch dan ubuntu, lalu buat hubungan antar node dan nama-nama dari node dan Merubah nama-nama dari node dengan `Change hostname`.<br/>
  ![create-topology-1](https://i.postimg.cc/Gth3jhhJ/image.png)<br/>
- Lalu setting network masing-masing node dengan fitur `Edit network configuration` dengan settingan:
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
- Ketik `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.0.0.0/16`, lalu command `cat /etc/resolv.conf` pada router `Foosha`.<br/>

- Ketik `echo nameserver 192.168.122.1 > /etc/resolv.conf` pada node ubuntu yang lain.<br/>

- Restart semua node. Untuk mencoba bahwa node dapat mengakses internet, ketik `ping google.com`. Berikut hasilnya yang dicoba pada `Loguetown`.<br/>
  ![image1](https://i.postimg.cc/Y9tLJ7Cj/image.png)

#### 2. Luffy ingin menghubungi Franky yang berada di EniesLobby dengan denden mushi. Kalian diminta Luffy untuk membuat website utama dengan mengakses franky.yyy.com dengan alias www.franky.yyy.com pada folder kaizoku. 

- Pada *EniesLobby* install aplikasi bind9.
  ```
  apt-get install bind9 -y
  ```
- Edit file `/etc/bind/named.conf.local` seperti ini:

  ![image2](https://i.postimg.cc/GhYqW96q/image.png)

- Membuat folder `kaizoku` di dalam `/etc/bind`.

  ```
  mkdir /etc/bind/kaizoku
  ```
  
- Copy file `db.local` pada path `/etc/bind` ke dalam folder `kaizoku` yang baru saja dibuat dan namanya diubah menjadi `franky.a02.com`.

  ```
  cp /etc/bind/db.local /etc/bind/kaizoku/franky.a02.com
  ```
  
- Edit file `/etc/bind/kaizoku/franky.a02.com` seperti ini:

  ![image3](https://i.postimg.cc/RZSpw4bd/image.png)
  
- Lalu restart bind9.

  ```
  service bind9 restart
  ```

- Pada *Loguetown* edit file `/etc/resolv.conf` seperti ini:

  ![image4](https://i.postimg.cc/BvY2RGpq/image.png)
  
- Mencoba dengan perintah ping domain `franky.a02.com` dan `www.franky.a02.com`.

  ![image5](https://i.postimg.cc/g067247b/image.png)


#### 3. Setelah itu buat subdomain super.franky.yyy.com dengan alias www.super.franky.yyy.com yang diatur DNS nya di EniesLobby dan mengarah ke Skypie. 

- Pada *EniesLobby* edit file `/etc/bind/kaizoku/franky.a02.com` seperti ini:

  ![image6](https://i.postimg.cc/XvwTgQkc/image.png)
  
- Lalu restart bind9.

  ```
  service bind9 restart
  ```

- Pada *Loguetown* mencoba dengan perintah ping domain `super.franky.a02.com` dan `www.super.franky.a02.com`.

  ![image7](https://i.postimg.cc/Twv5zYmv/image.png)

#### 4. Buat juga reverse domain untuk domain utama. 
- Pada *EniesLobby* edit file `/etc/bind/named.conf.local` seperti ini:

  ![image8](https://i.postimg.cc/ZRDt90L1/image.png)
  
- Copy file `db.local` pada path `/etc/bind` ke dalam folder `kaizoku` yang baru saja dibuat. 

- Lalu ubah namanya menjadi `2.0.10.in-addr.arpa`.

  ```
  cp /etc/bind/db.local /etc/bind/kaizoku/2.0.10.in-addr.arpa
  ```
  
- Edit file `/etc/bind/kaizoku/2.0.10.in-addr.arpa` seperti ini:

  ![image9](https://i.postimg.cc/L5b10k3N/image.png)
  
- Lalu restart bind9.

  ```
  service bind9 restart
  ```

- Pada *Loguetown* mengecek konfigurasi dengan perintah `host -t PTR 10.0.2.2`.

  ![image10](https://i.postimg.cc/HkCwGzCh/image.png)


#### 5. Supaya tetap bisa menghubungi Franky jika server EniesLobby rusak, maka buat Water7 sebagai DNS Slave untuk domain utama. 

- Pada *EniesLobby* edit file `/etc/bind/named.conf.local` seperti ini:

  ![image11](https://i.postimg.cc/1R7s4RRG/image.png)
  
- Lalu restart bind9.

  ```
  service bind9 restart
  ```

- Pada *Water7* install aplikasi bind9.

  ```
  apt-get install bind9 -y
  ```
  
- Edit file `/etc/bind/named.conf.local` seperti ini:

  ![image12](https://i.postimg.cc/yxcMfK9d/image.png)
  
- Lalu restart bind9.

  ```
  service bind9 restart
  ```

- Pada *Enieslobby* matikan service bind9 dahulu.

  ```
  service bind9 stop
  ```
  
- Edit file `/etc/resolv.conf` seperti ini:

  ![image13](https://i.postimg.cc/J4RYwjCK/image.png)
  
- Mengecek dengan ping domain `franky.a02.com`.

  ![image14](https://i.postimg.cc/zGVj5jtw/image.png)


#### 6. Setelah itu terdapat subdomain mecha.franky.yyy.com dengan alias www.mecha.franky.yyy.com yang didelegasikan dari EniesLobby ke Water7 dengan IP menuju ke Skypie dalam folder sunnygo. 

- Pada *EniesLobby* edit file `/etc/bind/kaizoku/franky.a02.com` seperti ini:

  ![image15](https://i.postimg.cc/jjYJ9QGS/image.png)
  
- Edit file `/etc/bind/named.conf.options` seperti berikut:

  ![image16](https://i.postimg.cc/N0VGSgy7/image.png)
  
- Lalu edit file `/etc/bind/named.conf.local` seperti ini:

  ![image17](https://i.postimg.cc/ZKTmf97z/image.png)
  
- Setelahnya restart bind9.

  ```
  service bind9 restart
  ```
  
- Pada *Water7* edit file `/etc/bind/named.conf.options` seperti berikut:

  ![image18](https://i.postimg.cc/25Y7vDjG/image.png)
  
- Edit file `/etc/bind/named.conf.local` seperti ini:

  ![image19](https://i.postimg.cc/FKQXL7J6/image.png)
  
- Buat folder `sunnygo` di dalam `/etc/bind`.

  ```
  mkdir /etc/bind/sunnygo
  ```
  
- Copy file `db.local` pada path `/etc/bind` ke dalam folder `sunnygo` yang baru saja dibuat dan ubah namanya menjadi `mecha.franky.a02.com`.

  ```
  cp /etc/bind/db.local /etc/bind/sunnygo/mecha.franky.a02.com
  ```
  
- Edit file `/etc/bind/sunnygo/mecha.franky.a02.com` seperti ini:

  ![image20](https://i.postimg.cc/J7JrGVgt/image.png)
  
- Lalu restart bind9.

  ```
  service bind9 restart
  ```

- Pada *Loguetown* mengecek dengan ping domain `mecha.franky.a02.com` dan `www.mecha.franky.a02.com`.

  ![image21](https://i.postimg.cc/yNkBKwc1/image.png)

#### 7. Untuk memperlancar komunikasi Luffy dan rekannya, dibuatkan subdomain melalui Water7 dengan nama general.mecha.franky.yyy.com dengan alias www.general.mecha.franky.yyy.com yang mengarah ke Skypie. 

- Pada *Water7* edit file `/etc/bind/sunnygo/mecha.franky.a02.com` seperti ini:

  ![image22](https://i.postimg.cc/yx6L4z3n/image.png)
  
- Restart bind9.

  ```
  service bind9 restart
  ```

- Pada *Loguetown* mengecek ping domain `general.mecha.franky.a02.com` dan `www.general.mecha.franky.a02.com`.

  ![image23](https://i.postimg.cc/ZKBr9Rmp/image.png)
