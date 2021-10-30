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

#### 8. Setelah melakukan konfigurasi server, maka dilakukan konfigurasi Webserver. Pertama dengan webserver www.franky.yyy.com. Pertama, luffy membutuhkan webserver dengan DocumentRoot pada /var/www/franky.yyy.com.

**Pada Skypie**
- Install aplikasi apache, PHP, dan libapache2-mod-php7.0.

  ```
  apt-get install apache2 -y
  apt-get install php -y
  apt-get install libapache2-mod-php7.0 -y
  ```
- Pindah ke directory `/etc/apache2/sites-available`.
- Copy file `000-default.conf` menjadi file `franky.A02.com.conf`.
- Edit file `franky.A02.com.conf` seperti pada gambar berikut:

- Aktifkan konfigurasi franky.A02.com.

  ```
  a2ensite franky.A02.com
  ```
- Restart apache.

  ```
  service apache2 restart
  ```
- Download file zip dengan `wget`.

  ```
  wget https://github.com/FeinardSlim/Praktikum-Modul-2-Jarkom/raw/main/franky.zip
  ```
- Lakukan unzip.

  ```
  unzip franky.zip
  ```
- Rename folder `franky` menjadi `franky.A02.com` dan terdapat isi file seperti pada gambar berikut:

**Pada Loguetown**
- Install aplikasi lynx.

  ```
  apt-get install lynx -y
  ```
- Akses `www.franky.A02.com` dengan lynx.

#### 9. Setelah itu, Luffy juga membutuhkan agar url www.franky.yyy.com/index.php/home dapat menjadi menjadi www.franky.yyy.com/home

**Pada Skypie**
- Jalankan perintah `a2enmod rewrite` untuk mengaktifkan module rewrite.
- Restart apache dengan perintah `service apache2 restart`.
- Tambahkan file baru `.htaccess` pada folder `/var/www/franky.A02.com`, di mana file tersebut akan dimodifikasi menjadi:

- Pindah ke directory `/etc/apache2/sites-available`.
- Edit file `franky.A02.com.conf` agar file `.htaccess` dapat berjalan seperti pada gambar berikut:

- Restart apache.

  ```
  service apache2 restart
  ```

**Pada Loguetown**
- Akses `www.franky.A02.com/home` dengan lynx.

#### 10. Setelah itu, pada subdomain www.super.franky.yyy.com, Luffy membutuhkan penyimpanan aset yang memiliki DocumentRoot pada /var/www/super.franky.yyy.com

**Pada Skypie**
- Pindah ke directory `/etc/apache2/sites-available`.
- Copy file `000-default.conf` menjadi file `super.franky.A02.com.conf`.
- Edit file `super.franky.A02.com.conf` seperti pada gambar berikut:

- Aktifkan konfigurasi super.franky.A02.com.

  ```
  a2ensite super.franky.A02.com
  ```
- Restart apache.

  ```
  service apache2 restart
  ```
- Pindah ke directory `/var/www`.
- Download file zip dengan `wget`.

  ```
  wget https://github.com/FeinardSlim/Praktikum-Modul-2-Jarkom/raw/main/super.franky.zip
  ```
- Lakukan unzip.

  ```
  unzip super.franky.zip
  ```
- Rename folder `super.franky` menjadi `super.franky.A02.com` dan terdapat isi file seperti pada gambar berikut:

**Pada Loguetown**
- Akses `www.super.franky.A02.com` dengan lynx.

#### 11. Akan tetapi, pada folder /public, Luffy ingin hanya dapat melakukan directory listing saja.

**Pada Skypie**
- Pindah ke directory `/etc/apache2/sites-available`.
- Edit file `super.franky.A02.com.conf` seperti pada gambar berikut:

- Restart apache.

  ```
  service apache2 restart
  ```

**Pada Loguetown**
- Akses `www.super.franky.A02.com/public` dengan lynx.

- Akses `www.super.franky.A02.com/public/css`, `www.super.franky.A02.com/public/images`, dan `www.super.franky.A02.com/public/js` dengan lynx.

#### 12. Tidak hanya itu, Luffy juga menyiapkan error file 404.html pada folder /error untuk mengganti error kode pada apache

**Pada Skypie**
- Pindah ke directory `/etc/apache2/sites-available`.
- Edit file `super.franky.A02.com.conf` seperti pada gambar berikut:

- Restart apache.

  ```
  service apache2 restart
  ```

**Pada Loguetown**
- Akses `www.super.franky.A02.com/publoc` (terdapat typo) dengan lynx.

#### 13.  Luffy juga meminta Nami untuk dibuatkan konfigurasi virtual host. Virtual host ini bertujuan untuk dapat mengakses file asset www.super.franky.yyy.com/public/js menjadi www.super.franky.yyy.com/js.

**Pada Skypie**
- Pindah ke directory `/etc/apache2/sites-available`.
- Edit file `super.franky.A02.com.conf` seperti pada gambar berikut:

- Restart apache.

  ```
  service apache2 restart
  ```

**Pada Loguetown**
- Akses `www.super.franky.A02.com/js` dengan lynx.

## Soal 14
Dan Luffy meminta untuk web `www.general.mecha.franky.yyy.com` hanya bisa diakses dengan port 15000 dan port 15500.

### Jawaban
**Pada Skypie**
- Pindah ke directory `/etc/apache2/sites-available`.
- Copy file `000-default.conf` menjadi file `general.mecha.franky.A02.com.conf`.
- Edit file `general.mecha.franky.A02.com.conf` 
- Edit file `/etc/apache2/ports.conf` untuk mengaktifkan port 15000 dan port 15500
- Aktifkan konfigurasi general.mecha.franky.A02.com.

  ```
  a2ensite general.mecha.franky.A02.com
  ```
- Restart apache.

  ```
  service apache2 restart
  ```
- Pindah ke directory `/var/www`.
- Download file zip menggunakan `wget`.

  ```
  wget https://github.com/FeinardSlim/Praktikum-Modul-2-Jarkom/raw/main/general.mecha.franky.zip
  ```
- Lakukan unzip.

  ```
  unzip general.mecha.franky.zip
  ```
- Rename folder `general.mecha.franky` menjadi `general.mecha.franky.A02.com`

**Pada Loguetown**
- Buka `www.general.mecha.franky.A02.com` menggunakan lynx.
- Buka `www.general.mecha.franky.A02.com:15000` menggunakan lynx.
- Buka `www.general.mecha.franky.A02.com:15500` menggunakan lynx.

## Soal 15
Dengan authentikasi username `luffy` dan password `onepiece` dan file di `/var/www/general.mecha.franky.yyy`.

### Jawaban
**Pada Skypie**
- Pindah ke directory `/etc/apache2/sites-available`.
- Edit file `general.mecha.franky.A02.com.conf`
- Jalankan perintah berikut untuk membuat akun autentikasi baru dengan username `luffy`. Kita akan diminta untuk memasukkan password baru dan confirm password tersebut diisi `onepiece`.

  ```
  htpasswd -c /etc/apache2/.htpasswd luffy
  ```
- Restart apache.

  ```
  service apache2 restart
  ```

**Pada Loguetown**
- Buka `www.general.mecha.franky.A02.com:15000` menggunakan lynx.
- 
## Soal 16
Dan setiap kali mengakses IP Skypie akan diahlikan secara otomatis ke `www.franky.yyy.com`.

### Jawaban
**Pada Skypie**
- Pindah ke directory `/etc/apache2/sites-available`.
- Edit file `000-default.conf`
- Restart apache.

  ```
  service apache2 restart
  ```

**Pada Loguetown**
- Buka `192.181.2.4` (IP Skypie) menggunakan lynx.

## Soal 17
Dikarenakan Franky juga ingin mengajak temannya untuk dapat menghubunginya melalui website `www.super.franky.yyy.com`, dan dikarenakan pengunjung web server pasti akan bingung dengan randomnya images yang ada, maka Franky juga meminta untuk mengganti request gambar yang memiliki substring `franky` akan diarahkan menuju `franky.png`.

### Jawaban
**Pada Skypie**
- Jalankan perintah `a2enmod rewrite` untuk mengaktifkan module rewrite.
- Restart apache dengan perintah `service apache2 restart`.
- Tambahkan file baru `.htaccess` pada folder `/var/www/super.franky.A02.com`
- Pindah ke directory `/etc/apache2/sites-available`.
- Edit file `super.franky.A02.com.conf` agar file `.htaccess` dapat berjalan
- Restart apache.

  ```
  service apache2 restart
  ```

**Pada Loguetown**
- Buka `www.super.franky.A02.com/public/images/franky.png` menggunakan lynx.
- Buka `www.super.franky.A02.com/public/images/eyeoffranky.jpg` menggunakan lynx.
- Buka `www.super.franky.A02.com/public/images/background-frank.jpg` menggunakan lynx.
