


#### Anggota :
#### 05111840000029	Khofifah Nurlaela
#### 05111840000053	Yulia Niza


### 1. Membuat Domain http://semeruc04.pw 

- Lakukan perintah pada *MALANG*. Isikan seperti berikut:

  ```
   nano /etc/bind/named.conf.local
  ```

- Isikan configurasi domain **semeruc04.pw** sesuai dengan syntax berikut:

  ```
  zone "semeruc04.pw" {
  	type master;
  	file "/etc/bind/jarkom/semeruc04.pw";
  };
  ```

- Buat folder **jarkom** di dalam **/etc/bind**

  ```
  mkdir /etc/bind/jarkom
  ```

- Copykan file **db.local** pada path **/etc/bind** ke dalam folder **jarkom** yang baru saja dibuat dan ubah namanya menjadi **semeruc04.pw**

  ```
  cp /etc/bind/db.local /etc/bind/jarkom/semeruc04.pw
  ```

- Kemudian buka file **semeruc04.pw** dan edit seperti gambar berikut dengan IP *MALANG* :

  ```
  nano /etc/bind/jarkom/semeruc04.pw
  ```

- Restart bind9 dengan perintah 

  ```
  service bind9 restart
  ```

- Pada client *GRESIK* dan *SIDOARJO* arahkan nameserver menuju IP *MALANG* dengan mengedit file _resolv.conf_ dengan mengetikkan perintah 

	```
	nano /etc/resolv.conf
	```

- Untuk mencoba koneksi DNS, lakukan ping domain **semeruc04.pw** dengan melakukan  perintah berikut pada client *GRESIK* dan *SIDOARJO*

  ```
  ping semeruc04.pw
  ```
![alt text](https://github.com/nizayulia/Jarkom_Modul2_Lapres_C04/blob/main/Assets/Picture1.PNG?raw=true)

### 2. Membuat Alias www.semeruc04.pw

- Buka file **semeruc04.pw** pada server *MALANG* dan tambahkan konfigurasi seperti pada gambar berikut:


  ```
  www	IN	CNAME	semeruc04.pw.
  ```

- Kemudian restart bind9 dengan perintah

  ```
  service bind9 restart
  ```

- Lalu cek dengan melakukan  **ping www.semeruc04.pw**

![alt text](https://github.com/nizayulia/Jarkom_Modul2_Lapres_C04/blob/main/Assets/Picture2.PNG?raw=true)


### 3. Membuat subdomain http://www.penanjakan.semeruc04.pw

- Edit file **/etc/bind/jarkom/semeruc04.pw** lalu tambahkan subdomain untuk **semeruc04.pw** yang mengarah ke IP *MALANG*.

  ```
  nano /etc/bind/jarkom/semeruc04.pw
  ```

- Tambahkan konfigurasi seperti pada gambar ke dalam file **semeruc04.pw**.

  ```
  penanjakan	IN 	A	10.151.77.42 ; IP MALANG
  ```

- Restart service bind  

  ```
  service bind9 restart
  ```

- Coba ping ke subdomain dengan perintah berikut dari client *GRESIK*

  ```
  ping penanjakan.semeruc04.pw

  ```
  ![alt text](https://github.com/nizayulia/Jarkom_Modul2_Lapres_C04/blob/main/Assets/Picture3.PNG?raw=true)

### 4. Reverse domain untuk domain utama
- Edit file **/etc/bind/named.conf.local** pada *MALANG*
  ```
  nano /etc/bind/named.conf.local
  ```
- Lalu tambahkan konfigurasi berikut ke dalam file **named.conf.local**
  ```
  zone "77.151.10.in-addr.arpa" {
      type master;
      file "/etc/bind/jarkom/77.151.10.in-addr.arpa";
  };
  ```

![alt text](https://github.com/nizayulia/Jarkom_Modul2_Lapres_C04/blob/main/Assets/dns_reserve_1.JPG?raw=true)

- Copykan file **db.local** pada path **/etc/bind** ke dalam folder **jarkom** yang baru saja dibuat dan ubah namanya menjadi **77.151.10.in-addr.arpa**
  ```
  cp /etc/bind/db.local /etc/bind/jarkom/77.151.10.in-addr.arpa
  ```
- Edit file **77.151.10.in-addr.arpa** menjadi seperti gambar di bawah ini

![alt text](https://github.com/nizayulia/Jarkom_Modul2_Lapres_C04/blob/main/Assets/dns_reserve_2.JPG?raw=true)
- Kemudian restart bind9 dengan perintah 
  ```
  service bind9 restart
  ```
- Untuk mengecek apakah konfigurasi sudah benar atau belum, lakukan perintah berikut pada client *GRESIK* 

  ```
  host -t PTR 10.151.77.42
  ```
![alt text](https://github.com/nizayulia/Jarkom_Modul2_Lapres_C04/blob/main/Assets/dns_reserve_3.JPG?raw=true)

### 5. DNS Server Slave pada MOJOKERTO
#### I. Konfigurasi Pada Server MALANG
- Edit file **/etc/bind/named.conf.local** dan sesuaikan dengan syntax berikut
  ```
  zone "semeruc04.pw" {
      type master;
      notify yes;
      also-notify { 10.151.77.43; }; // Masukan IP MOJOKERTO tanpa tanda petik
      allow-transfer { 10.151.77.43; }; // Masukan IP MOJOKERTO tanpa tanda petik
      file "/etc/bind/jarkom/semeruc04.pw";
  };
  ```
  ![alt text](https://github.com/nizayulia/Jarkom_Modul2_Lapres_C04/blob/main/Assets/dns_slave_1.JPG?raw=true)

- Lakukan restart bind9
  ```
  service bind9 restart
  ```
#### II. Konfigurasi Pada Server MOJOKERTO
- Buka *MOJOKERTO* dan update package lists dengan menjalankan command:
  ```
  apt-get update
  ```
- Setalah melakukan update silahkan install aplikasi bind9 pada *MOJOKERTO* dengan perintah:
  ```
  apt-get install bind9 -y
  ```
- Kemudian buka file **/etc/bind/named.conf.local** pada MOJOKERTO dan tambahkan syntax berikut:
  ```
  zone "semeruc04.pw" {
      type slave;
      masters { 10.151.77.42; }; // Masukan IP MALANG tanpa tanda petik
      file "/var/lib/bind/semeruc04.pw";
  };
  ```

![alt text](https://github.com/nizayulia/Jarkom_Modul2_Lapres_C04/blob/main/Assets/dns_slave_2.JPG?raw=true)

- Lakukan restart bind9
  ```
  service bind9 restart
  ```

#### III. Testing

- Pada server *MALANG* silahkan matikan service bind9
  ```
  service bind9 stop
  ```
- Pada client *GRESIK* pastikan pengaturan nameserver mengarah ke IP *MALANG* dan IP *MOJOKERTO*
  ```
  nameserver 10.151.77.42   #IP MALANG
  nameserver 10.151.77.43   #IP MOJOKERTO
  ```
- Lakukan ping ke semeruc04.pw pada client *GRESIK*. Jika ping berhasil maka konfigurasi DNS slave telah berhasil


![alt text](https://github.com/nizayulia/Jarkom_Modul2_Lapres_C04/blob/main/Assets/dns_slave_3.JPG?raw=true)

### 6. Subdomain dengan alamat http://gunung.semeruc04.pw yang didelegasikan pada server MOJOKERTO dan mengarah ke IP Server PROBOLINGGO
#### I. Konfigurasi Pada Server *MALANG*
- Pada *MALANG*, edit file */etc/bind/jarkom/semeruc04.pw* dan ubah menjadi seperti di bawah ini sesuai dengan pembagian IP *MALANG* kelompok masing-masing.
  ```
  nano /etc/bind/jarkom/semeruc04.pw
  ```
![DNS](gambar/17.png)

- Kemudian edit file **/etc/bind/named.conf.options** pada *MALANG*.

  ```
  nano /etc/bind/named.conf.options
  ```

- Kemudian comment **dnssec-validation auto;** dan tambahkan baris berikut pada **/etc/bind/named.conf.options**

  ```
  allow-query{any;};
  ```


![DNS](gambar/18.png)

- Kemudian edit file **/etc/bind/named.conf.local** menjadi seperti gambar di bawah:

  ```
  zone "semeruc04.pw" {
      type master;
      file "/etc/bind/jarkom/semeruc04.pw";
      allow-transfer { 10.151.77.43; }; // Masukan IP MOJOKERTO tanpa tanda petik
  };
  ```

![DNS](gambar/19.png)

- Setelah itu restart bind9
  ```
  service bind9 restart
  ```
#### II. Konfigurasi Pada Server *MOJOKERTO*
- Pada *MOJOKERTO* edit file **/etc/bind/named.conf.options**
  ```
  nano /etc/bind/named.conf.options
  ```
- Kemudian comment **dnssec-validation auto;** dan tambahkan baris berikut pada **/etc/bind/named.conf.options**
  ```
  allow-query{any;};
  ```

![DNS](gambar/20.png)

- Lalu edit file **/etc/bind/named.conf.local** menjadi seperti gambar di bawah:

![DNS](gambar/21.png)

- Kemudian buat direktori dengan nama **delegasi** 
- Copy **db.local** ke direktori delegasi dan edit namanya menjadi **gunung.semeruc04.pw** 

  ```
  mkdir /etc/bind/delegasi
  cp /etc/bind/db.local /etc/bind/delegasi/gunung.semeruc04.pw
  ```

- Kemudian edit file **/etc/bind/delegasi/gunung.semeruc04.pw** menjadi seperti dibawah ini

![alt text](https://github.com/nizayulia/Jarkom_Modul2_Lapres_C04/blob/main/Assets/delegasi subdomain_1.JPG?raw=true)

- Restart bind9
  ```
  service bind9 restart
  ```
#### III. Testing
- Lakukan ping ke domain **gunung.semeruc04.pw** dari client *GRESIK*

![DNS](gambar/23.png)


### 7. subdomain dengan nama http://naik.gunung.semeruc04.pw, domain ini diarahkan ke IP Server PROBOLINGGO.
#### I. Konfigurasi pada MOJOKERTO
- Edit file **/etc/bind/delegasi/gunung.semeruc04.pw** menjadi seperti dibawah ini

![alt text](https://github.com/nizayulia/Jarkom_Modul2_Lapres_C04/blob/main/Assets/delegasi sub subdomain.JPG?raw=true)

- Restart bind9
  ```
  service bind9 restart
  ```
#### III. Testing
- Lakukan ping ke domain **naik.gunung.semeruc04.pw** dari client *GRESIK*

![alt text](https://github.com/nizayulia/Jarkom_Modul2_Lapres_C04/blob/main/Assets/delegasi sub subdomain_1.JPG?raw=true)

### 8. Domain http://semeruc04.pw memiliki DocumentRoot pada /var/www/semeruc04.pw. Awalnya web dapat diakses menggunakan alamat http://semeruc04.pw/index.php/home.

- Pindah ke directory /etc/apache2/sites-available
- Copy file default menjadi file semeruc04.pw.conf
![alt text](https://github.com/nizayulia/Jarkom_Modul2_Lapres_C04/blob/main/Assets/Picture8_1.png?raw=true)
- Lalu restart apche dengan perintah
  ```
  service apache restart
  ```
- Masuk ke directoy.
- Download halaman web dengan perintah `wget 10.151.36.202/semeru.pw.zip`
- Kemudian unzip file.
- rename semeruc04.pw
![alt text](https://github.com/nizayulia/Jarkom_Modul2_Lapres_C04/blob/main/Assets/Picture8_2.png?raw=true)

### 9. diaktifkan mod rewrite agar urlnya menjadi http://semeruc04.pw/home.
- Jalankan perintah `a2enmod rewrite`
- Pindah ke directory `/var/www/semeruc04.pw` dan buat file `.htaccess` dengan isi file :

![alt text](https://github.com/nizayulia/Jarkom_Modul2_Lapres_C04/blob/main/Assets/Picture9_1.png?raw=true)

- Pindah ke directory `/etc/apache2/sites-available` kemudian buka file `semeruc04.pw`, tambahkan :

![alt text](https://github.com/nizayulia/Jarkom_Modul2_Lapres_C04/blob/main/Assets/Picture9_2.png?raw=true)

- Lalu restart apche dengan perintah
  ```
  service apache restart
  ```
- Jalankan `semeruc04.pw/home` pada browser.

![alt text](https://github.com/nizayulia/Jarkom_Modul2_Lapres_C04/blob/main/Assets/Picture9_3.png?raw=true)

### 10.  Web http://penanjakan.semeruc04.pw akan digunakan untuk menyimpan assets file yang memiliki DocumentRoot pada /var/www/penanjakan.semeruc04.pw dan memiliki struktur folder ...
-	Pindah ke directory `/etc/apache2/sites-available`
-	Nano `penanjakan.semeruc04.pw.conf`

![alt text](https://github.com/nizayulia/Jarkom_Modul2_Lapres_C04/blob/main/Assets/Picture10.png?raw=true)

- Lalu restart apche dengan perintah
  ```
  service apache restart
  ```
- Masuk ke directory `/var/www`
-	Download halaman web dengan perintah `wget 10.151.36.202/penanjakan.semeru.pw.zip`
- Unzip
- rename `penanjakan.semeruc04.pw`

### 11. Pada folder /public dibolehkan directory listing namun untuk folder yang berada di dalamnya tidak dibolehkan.
-	Pindah ke directory `/etc/apache2/sites-available`
-	Nano `penanjakan.semeruc04.pw.conf`
-	Atur `+` untuk dibolehkan dan `-` untuk tidak dibolehkan

![alt text](https://github.com/nizayulia/Jarkom_Modul2_Lapres_C04/blob/main/Assets/Picture11.png?raw=true)

### 12. Untuk mengatasi HTTP Error code 404, disediakan file 404.html pada folder /errors untuk mengganti error default 404 dari Apache. 
-	Pindah ke directory `/etc/apache2/sites-available`
-	Nano `penanjakan.semeruc04.pw.conf`
-	Tambahkan :  `ErrorDocument 404 /errors/404.html`

![alt text](https://github.com/nizayulia/Jarkom_Modul2_Lapres_C04/blob/main/Assets/Picture12.png?raw=true)

### 13. Untuk mengakses file assets javascript awalnya harus menggunakan url http://penanjakan.semeruc04.pw/public/javascripts. selesai.
-	Pindah ke directory `/etc/apache2/sites-available`
-	Nano `penanjakan.semeruc04.pw.conf`
-	Tambahkan : `Alias “/js” “/var/www/penanjakan.semeruc04.pw/public/javascripts”`

![alt text](https://github.com/nizayulia/Jarkom_Modul2_Lapres_C04/blob/main/Assets/Picture13.png?raw=true)

### 14. http://naik.gunung.semeruc04.pw sudah bisa diakses hanya dengan menggunakan port 8888. DocumentRoot web berada pada /var/www/naik.gunung.semeruc04.pw.

-	Pindah ke directory `/etc/apache2` 
-	Tambahkan `Listen 8888`

![alt text](https://github.com/nizayulia/Jarkom_Modul2_Lapres_C04/blob/main/Assets/Picture14.png?raw=true)

-	Pindah ke directory `/etc/apache2/sites-available`
-	Nano `naik.semeruc04.pw`
-	Config sbg berikut :

![alt text](https://github.com/nizayulia/Jarkom_Modul2_Lapres_C04/blob/main/Assets/Picture14_2.png?raw=true)

-	Pindah ke directory `/var/www`
-	Download halaman web dengan perintah `wget 10.151.36.202/naik.gunung.semeru.pw.zip`
-	Unzip file dan rename `naik.gunung.penanjakan.semeruc04.pw`
-	`a2ensite default-8888`
-	Jalankan : `naik.gunung.penanjakan.semeruc04.pw:8888` pada browser.

![alt text](https://github.com/nizayulia/Jarkom_Modul2_Lapres_C04/blob/main/Assets/Picture14_3.png?raw=true)

### 15. web http://naik.gunung.semeruc04.pw agar diberi autentikasi password dengan username “semeru” dan password “kuynaikgunung”
### 16. mengunjungi IP PROBOLINGGO akan dialihkan secara otomatis ke http://semeruc04.pw.
-	Pindah ke directory `/etc/apache2/sites-available`
-	Nano `000- default.conf`
-	Ubah documentRoot :

![alt text](https://github.com/nizayulia/Jarkom_Modul2_Lapres_C04/blob/main/Assets/Picture16.png?raw=true)

### 17. request gambar yang memiliki substring “semeru” akan diarahkan menuju semeru.jpg.
