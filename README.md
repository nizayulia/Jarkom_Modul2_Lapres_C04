


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



### 3. Membuat subdomain http://www.penanjakan.semeruc04.pw

- Edit file **/etc/bind/jarkom/semeruc04.pw** lalu tambahkan subdomain untuk **semeruc04.pw** yang mengarah ke IP *MALANG*.

  ```
  nano /etc/bind/jarkom/semeruc04.pw
  ```

- Tambahkan konfigurasi seperti pada gambar ke dalam file **jarkom2020.com**.

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
  

### 1.2.D Reverse DNS (Record PTR)

Jika pada pembuatan domain sebelumnya DNS server kita bekerja menerjemahkan string domain **jarkom2020.com** kedalam alamat IP agar dapat dibuka, maka Reverse DNS atau Record PTR digunakan untuk menerjemahkan alamat IP ke alamat domain yang sudah diterjemahkan sebelumnya.

- Edit file **/etc/bind/named.conf.local** pada *MALANG*

  ```
  nano /etc/bind/named.conf.local
  ```

- Lalu tambahkan konfigurasi berikut ke dalam file **named.conf.local**

  ```
  zone "71.151.10.in-addr.arpa" {
      type master;
      file "/etc/bind/jarkom/71.151.10.in-addr.arpa";
  };
  ```

![](gambar/6.png)

- Copykan file **db.local** pada path **/etc/bind** ke dalam folder **jarkom** yang baru saja dibuat dan ubah namanya menjadi **71.151.10.in-addr.arpa**

  ```
  cp /etc/bind/db.local /etc/bind/jarkom/71.151.10.in-addr.arpa
  ```

  *Keterangan 71.151.10 adalah 3 byte pertama IP MALANG yang dibalik urutan penulisannya*

- Edit file **71.151.10.in-addr.arpa** menjadi seperti gambar di bawah ini


![konfig](gambar/7.png)

- Kemudian restart bind9 dengan perintah 

  ```
  service bind9 restart
  ```

- Untuk mengecek apakah konfigurasi sudah benar atau belum, lakukan perintah berikut pada client *GRESIK* 

  ```
  // Install package dnsutils
  // Pastikan nameserver telah dikembalikan ke settingan awal
  apt-get update
  apt-get install dnsutils
  
  //Kembalikan nameserver agar tersambung dengan MALANG
  host -t PTR "IP MALANG"
  ```

![host](gambar/8.png)

### 1.2.F Membuat DNS Slave

DNS Slave adalah DNS cadangan yang akan diakses jika server DNS utama mengalami kegagalan. Kita akan menjadikan server *MOJOKERTO* sebagai DNS slave dan server *MALANG* sebagai DNS masternya.

#### I. Konfigurasi Pada Server MALANG

- Edit file **/etc/bind/named.conf.local** dan sesuaikan dengan syntax berikut

  ```
  zone "jarkom2020.com" {
      type master;
      notify yes;
      also-notify { "IP MOJOKERTO"; }; // Masukan IP MOJOKERTO tanpa tanda petik
      allow-transfer { "IP MOJOKERTO"; }; // Masukan IP MOJOKERTO tanpa tanda petik
      file "/etc/bind/jarkom/jarkom2020.com";
  };
  ```

  ![DNS](gambar/11.png)



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
  zone "jarkom2020.com" {
      type slave;
      masters { "IP MALANG"; }; // Masukan IP MALANG tanpa tanda petik
      file "/var/lib/bind/jarkom2020.com";
  };
  ```

![DNS](gambar/12.png)

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

  ![DNS](gambar/13.png)

- Lakukan ping ke jarkom2020.com pada client *GRESIK*. Jika ping berhasil maka konfigurasi DNS slave telah berhasil


![DNS](gambar/14.png)







### 1.2.H Delegasi Subdomain

Delegasi subdomain adalah pemberian wewenang atas sebuah subdomain kepada DNS baru.

#### I. Konfigurasi Pada Server *MALANG*

- Pada *MALANG*, edit file **/etc/bind/jarkom/jarkom2020.com** dan ubah menjadi seperti di bawah ini sesuai dengan pembagian IP *MALANG* kelompok masing-masing.

  ```
  nano /etc/bind/jarkom/jarkom2020.com
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
  zone "jarkom2020.com" {
      type master;
      file "/etc/bind/jarkom/jarkom2020.com";
      allow-transfer { "IP MOJOKERTO"; }; // Masukan IP MOJOKERTO tanpa tanda petik
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

- Copy **db.local** ke direktori pucang dan edit namanya menjadi **its.jarkom2020.com** 

  ```
  mkdir /etc/bind/delegasi
  cp /etc/bind/db.local /etc/bind/delegasi/its.jarkom2020.com
  ```

- Kemudian edit file **its.jarkom2020.com** menjadi seperti dibawah ini

![DNS](gambar/22.png)

- Restart bind9

  ```
  service bind9 restart
  ```

#### III. Testing

- Lakukan ping ke domain **its.jarkom2020.com** dan **integra.its.jarkom2020.com** dari client *GRESIK*

![DNS](gambar/23.png)



### 1.2.I DNS Forwarder

DNS Forwarder digunakan untuk mengarahkan DNS Server ke IP yang ingin dituju.

- Edit file **/etc/bind/named.conf.options** pada server *MALANG*
- Uncomment pada bagian ini

```
forwarders {
    8.8.8.8;
};
```
- Comment pada bagian ini

```
// dnssec-validation auto;
```
- Dan tambahkan

```
allow-query{any;};
```

![DNS](gambar/24.png)

- Harusnya jika nameserver pada file **/etc/resolv.conf** di client diubah menjadi IP MALANG maka akan di forward ke IP DNS google yaitu 8.8.8.8 dan bisa mendapatkan koneksi.
- Coba ping google.com pada GRESIK, kalau benar maka tetap bisa mendapatkan respon dari google

![DNS](gambar/25.png)



### 4. reverse domain untuk domain utama. ```Done```
### 5. DNS Server Slave pada MOJOKERTO
### 6. subdomain dengan alamat http://gunung.semeruyyy.pw yang didelegasikan pada server MOJOKERTO dan mengarah ke IP Server PROBOLINGGO.
### 7. subdomain dengan nama http://naik.gunung.semeruyyy.pw, domain ini diarahkan ke IP Server PROBOLINGGO.

### 8. Domain http://semeruyyy.pw memiliki DocumentRoot pada /var/www/semeruyyy.pw. Awalnya web dapat diakses menggunakan alamat http://semeruyyy.pw/index.php/home.

### 9. diaktifkan mod rewrite agar urlnya menjadi http://semeruyyy.pw/home.
### 10.  Web http://penanjakan.semeruyyy.pw akan digunakan untuk menyimpan assets file yang memiliki DocumentRoot pada /var/www/penanjakan.semeruyyy.pw dan memiliki struktur folder ...
### 11. Pada folder /public dibolehkan directory listing namun untuk folder yang berada di dalamnya tidak dibolehkan.
### 12. Untuk mengatasi HTTP Error code 404, disediakan file 404.html pada folder /errors untuk mengganti error default 404 dari Apache. 
### 13. Untuk mengakses file assets javascript awalnya harus menggunakan url http://penanjakan.semeruyyy.pw/public/javascripts. selesai. ### 14. http://naik.gunung.semeruyyy.pw sudah bisa diakses hanya dengan menggunakan port 8888. DocumentRoot web berada pada /var/www/naik.gunung.semeruyyy.pw.
### 15. web http://naik.gunung.semeruyyy.pw agar diberi autentikasi password dengan username “semeru” dan password “kuynaikgunung”
### 16. mengunjungi IP PROBOLINGGO akan dialihkan secara otomatis ke http://semeruyyy.pw.
### 17. request gambar yang memiliki substring “semeru” akan diarahkan menuju semeru.jpg.
