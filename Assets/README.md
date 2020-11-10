# ini ntar digabung readme nya sama no 1-7

### 8. Domain http://semeruyyy.pw memiliki DocumentRoot pada /var/www/semeruyyy.pw. Awalnya web dapat diakses menggunakan alamat http://semeruyyy.pw/index.php/home.

### 9. diaktifkan mod rewrite agar urlnya menjadi http://semeruyyy.pw/home.
### 10.  Web http://penanjakan.semeruyyy.pw akan digunakan untuk menyimpan assets file yang memiliki DocumentRoot pada /var/www/penanjakan.semeruyyy.pw dan memiliki struktur folder ...
### 11. Pada folder /public dibolehkan directory listing namun untuk folder yang berada di dalamnya tidak dibolehkan.
### 12. Untuk mengatasi HTTP Error code 404, disediakan file 404.html pada folder /errors untuk mengganti error default 404 dari Apache. 
### 13. Untuk mengakses file assets javascript awalnya harus menggunakan url http://penanjakan.semeruyyy.pw/public/javascripts. selesai. ### 14. http://naik.gunung.semeruyyy.pw sudah bisa diakses hanya dengan menggunakan port 8888. DocumentRoot web berada pada /var/www/naik.gunung.semeruyyy.pw.
### 15. web http://naik.gunung.semeruyyy.pw agar diberi autentikasi password dengan username “semeru” dan password “kuynaikgunung”
### 16. mengunjungi IP PROBOLINGGO akan dialihkan secara otomatis ke http://semeruyyy.pw.
### 17. request gambar yang memiliki substring “semeru” akan diarahkan menuju semeru.jpg.


## H. Mari Berimajinasi
### A. Setting Domain pada Apache
Kamu dan Vinsen adalah satu kelompok dalam mata kuliah Jaringan Komputer. Mereka diperintahkan oleh asisten untuk membuat website dengan domain __jarkom2020.com__, dan diberikan akses ke server yang bisa digunakan sebagai tempat host untuk websitenya. Tapi karena sesuatu dan lain hal, Vinsen tidak bisa membantumu mengerjakan perintah dari asisten. Beruntungnya, Vinsen meninggalkan catatan untuk Kamu ikuti agar Kamu dapat menyelesaikan perintah dari asisten.

Ayo bantu Kamu dengan mengonfigurasi server sesuai petunjuk yang diberikan Vinsen:

#### A.1 Pindah ke _directory_ `/etc/apache2/sites-available`
Copy file __default__ menjadi file __jarkom2020.com__. Jangan lupa untuk menambahkan `.conf` jika apache2 versi 2.4.x

<img src="Gambar/19.png" width="500">

#### A.2 Buka file _jarkom2020.com_
+ Tambahkan
	```
	ServerName jarkom2020.com
	ServerAlias www.jarkom2020.com
	```
	Menurut [dokumentasi apache2.2](https://httpd.apache.org/docs/2.2/mod/core.html):
	+ `ServerName` adalah "_Hostname and port that the server uses to identify itself_"
	+ `ServerAlias` adalah "_Alternate names for a host used when matching requests to name-virtual host_"
+ Ubah _DocumentRoot_ menjadi `/var/www/jarkom2020.com`

<img src="Gambar/20.png" width="500">

#### A.3 Aktifkan konfigurasi _jarkom2020.com_
Gunakan perintah `a2ensite jarkom2020.com`

#### A.4 Restart apache
Gunakan perintah `service apache2 restart`

<img src="Gambar/21.png" width="500">

#### A.5 Pindah ke _directory_ `/var/www`
Kemudian buatlah sebuah _directory_ baru di dalam `var/www` dengan nama __jarkom2020.com__

<img src="Gambar/22.png" width="500">

#### A.6 Masuk ke _directory_ `/var/www/jarkom2020.com` dan buat file _index.php_
Isi file __index.php__ tersebut dengan
```
<?php
    echo "Kabupaten Mojokerto adalah sebuah kabupaten di Provinsi Jawa Timur, Indonesia. Kabupaten yang secara resmi didirikan pada tanggal 9 Mei 1293 ini merupakan wilayah tertua ke-10 di Provinsi Jawa Timur.";
?>
```

<img src="Gambar/23.png" width="500">

#### A.7 Ganti DNS laptop/komputer sesuai IP Malang masing-masing
+ __Pada Windows__
	+ Sudah dijelaskan sebelumnya. 
	
+ __Pada Linux__
	+ Ubah file __/etc/resolv.conf__ dengan perintah `sudo nano /etc/resolv.conf`
	+ _Comment_ DNS yang sedang aktif dan tambahkan `nameserver <IP Malang>`
	+ Simpan hasil perubahannya
<img src="Gambar/36.png">

#### A.8 Buka browser dan akses _jarkom2020.com_

<img src="Gambar/24.png">

### B. Directory Listing
Di dalam _directory_ `/var/www/jarkom2020.com` diberikan struktur _directory_ sebagai berikut.
```
/var/www/jarkom2020.com/
├── assets/
│   └── javascript/
├── data/
└── download/
    └── img/
```
Perintah berikutnya dari asisten adalah untuk membuat beberapa direktori, __/assets__, __/data__, dan __/download__. Direktori __/download__ harus dapat menampilkan daftar file yang ada dalam direktori tersebut, sedangkan direktori __/assets__ tidak boleh menampilkan isi direktori tersebut.

Ayo bantu Kamu yang kebingungan membaca penjelasan dari Vinsen agar dapat mengerjakan perintah asisten. 

#### B.1 Buat _directory-directory_ yang diperlukan oleh website jarkom2020.com milik Rachma
Gunakan perintah-perintah berikut ini:
```
mkdir /var/www/jarkom2020.com/data
mkdir /var/www/jarkom2020.com/download
mkdir /var/www/jarkom2020.com/download/img
mkdir /var/www/jarkom2020.com/assets
mkdir /var/www/jarkom2020.com/assets/javascript
```

<img src="Gambar/25.png" width="500">

#### B.2 Aktifkan Directory Listing untuk /download
+ Pindah ke _directory_ `/etc/apache2/sites-available` kemudian buka file ___jarkom2020.com___ dan tambahkan
	```
	<Directory /var/www/jarkom2020.com/download>
	    Options +Indexes
	</Directory>
	```
	jangan lupa untuk menyimpan perubahan tersebut agar _directory_  ___download___ menampilkan isi _directory_-nya.
	
	<img src="Gambar/26.png" width="500">
	
	
+ Restart apache dengan perintah `service apache2 restart`
+ Buka browser dan akses http://jarkom2020.com/download

<img src="Gambar/27.png">

__Keterangan__:
Untuk mengatur _directory_ pada sebuah web, menggunakan
```
<Directory /x> ... </Directory>
```
Contoh untuk mengatur `/var/www/jarkom2020.com/download`
```
<Directory /var/www/jarkom2020.com/download>
	
</Directory>
```

#### B.3 Matikan Directory Listing untuk /assets
+ Pindah ke _directory_ `/etc/apache2/sites-available` kemudian buka file ___jarkom2020.com___ dan tambahkan
	```
	<Directory /var/www/jarkom2020.com/assets>
	    Options -Indexes
	</Directory>
	```
	jangan lupa untuk menyimpan perubahan tersebut agar _directory_  ___assets___ tidak menampilkan isi _directory_-nya.
	
	<img src="Gambar/28.png" width="500">
	
+ Restart apache dengan perintah `service apache2 restart`
+ Buka browser dan akses http://jarkom2020.com/assets

<img src="Gambar/29.png">

### C. Directory Alias
Karena URL __http://[IP Mojokerto]/assets/javascript__ dirasa terlalu panjang, maka Kamu mencoba membuat _directory alias_ menjadi __http://[IP Mojokerto]/assets/js__ agar lebih terlihat _simple_.

Berikut adalah langkah-langkah pengerjaan yang diberikan Vinsen:

+ Pindah ke _directory_ `/etc/apache2/sites-available` kemudian buka file ___jarkom2020.com___ dan tambahkan
	```    
	<Directory /var/www/jarkom2020.com/assets/javascript>
	    Options +Indexes
	</Directory>
	
	Alias "/assets/js" "/var/www/jarkom2020.com/assets/javascript"
	```

	jangan lupa untuk menyimpan perubahan tersebut agar _directory_  ___assets/javascript___ dapat menampilkan isi _directory_-nya saat pengguna mengakses __http://[IP Mojokerto]/assets/js__.
	
	<img src="Gambar/30.png" width="500">
	
+ Restart apache dengan perintah `service apache2 restart`
+ Pindah ke folder __/var/www/jarkom2020.com/assets/javascript__ dan buat file __app.js__ dengan perintah `touch app.js`
+ Buka browser dan akses http://jarkom2020.com/assets/js

<img src="Gambar/31.png">

### D. Module Rewrite
#### D.1 Aktifkan Module Rewrite
Perintah asisten berikutnya adalah menyalakan _module rewrite_ agar penulisan URL menjadi lebih rapi dan tanpa perlu menuliskan ekstensi _.php_ ketika mengakses laman.

+ Jalankan perintah `a2enmod rewrite` untuk mengaktifkan _module rewrite_.
	
+ Restart apache dengan perintah `service apache2 restart`

	<img src="Gambar/32.png" width="500">

Biasanya semua konfigurasi terhadap sebuah website diatur pada file di _directory_ __/etc/apache2/sites-available__. Namun terkadang ada sebuah kasus bahwa   hak akses root untuk mengedit file konfigurasi yang berada di folder __/etc/apache2/sites-available__ tidak dimiliki, atau kita tidak ingin user lain untuk mengedit file konfigurasi yang berada di _directory_ __/etc/apache2/sites-available__.

Untuk mengatasi masalah tersebut, buat file __.htaccess__ pada _directory_ yang akan diatur.

Contohnya adalah seperti kasus di atas, dimana kita ingin mengatur _mod rewrite_ dari __[http://jarkom2020.com](http://jarkom2020.com)__ agar saat mengakses file php kita tidak perlu menuliskan ekstensinya. Maka yang yang perlu kita lakukan adalah
+ Pindah ke _directory_ `/var/www/jarkom2020.com` dan buat file __.htaccess__ dengan isi file
	```
	RewriteEngine On
	RewriteCond %{REQUEST_FILENAME} !-d
	RewriteRule ^([^\.]+)$ $1.php [NC,L]
	```

	<img src="Gambar/33.png" width="500">

	__Keterangan__:
	+ `RewriteEngine On` = untuk flag bahwa menggunakan module rewrite
	+ `RewriteCond %{REQUEST_FILENAME} !-d` = aturan tidak akan jalan ketika yang diakses adalah _directory_ (d)
	+ `RewriteRule ^([^\.]+)$ $1.php [NC,L]` = $1 adalah parameter input yang akan dicari oleh webserver
	* Lebih detailnya [klik disini](https://httpd.apache.org/docs/2.4/rewrite/flags.html)
+ Buat file _about.php_ di dalam _directory_ `/var/www/jarkom2020.com/` dengan isi
	```
	<?php
		echo "Ini adalah halaman About";
	?>
	```
+ Pindah ke _directory_ `/etc/apache2/sites-available` kemudian buka file ___jarkom2020.com___ dan tambahkan
	```
	<Directory /var/www/jarkom2020.com>
	    Options +FollowSymLinks -Multiviews
	    AllowOverride All
	</Directory>
	```
	dan jangan lupa untuk menyimpan perubahan tersebut.
	
	<img src="Gambar/34.png" width="500">
	
	__Keterangan__:
	+ `AllowOverride All` ditambahkan agar  konfigurasi __.htaccess__ dapat berjalan.
	+ `+FollowSymLinks` ditambahkan agar konfigurasi __mod_rewrite__ dapat berjalan.
	+ `-Multiviews` ditambahkan agar konfigurasi __mod_negotiation__ tidak dapat berjalan. __mod_negotiation__ bisa '_rewrite_' _requests_ sehingga menimpa dan mengganggu __mod_rewrite__.

+ Restart apache dengan perintah `service apache2 restart`
+ Buka browser dan akses __http://jarkom2020.com/aboutus__

	<img src="Gambar/35.png">
	
<!-- ### E. Otorisasi
Pada web http:jarkom2020.com terdapat path __/data__ yang tidak boleh dibuka sembarang orang. Rachma ingin __/data__ hanya boleh diakses oleh pengguna yang memiliki IP 10.151.252.0/255.255.252.0

Maka yang diinstruksikan Ifin agar _directory_ __/data__ milik Rachma tetap aman adalah
+ Pindah ke _directory_ `/etc/apache2/sites-available` kemudian buka file ___jarkom2020.com___ dan tambahkan
	```
	<Directory /var/www/jarkom2020.com/data>
	    Options +Indexes
	    Order deny,allow
	    Deny from all
	    Allow from 10.151.252.0/255.255.252.0
	</Directory>
	```
	jangan lupa untuk menyimpan perubahan tersebut.
	
	__Keterangan__:
	+ `Order deny, allow` merupakan urutan hak akses. Terdapat dua jenis tipe order yaitu:
		+ `deny,allow`: Bagian _Deny_ harus di-_declare_ terlebih dahulu sebelum _Allow_
		+ `allow,deny`: Bagian _Allow_ harus di-_declare_ terlebih dahulu sebelum _Deny_
	+ `Deny from all`  berarti semua pengguna ditolak
	+ `Allow from 10.151.252.0/255.255.252.0` berarti apabila pengguna memiliki IP NID 10.151.252.0./22, ia diperbolehkan untuk mengakses halaman.
	+ Info lebih lanjut [klik disini](https://httpd.apache.org/docs/2.4/mod/mod_access_compat.html)	+ Restart apache dengan perintah `service apache2 restart`
+ Buka browser dan akses __http://jarkom2020.com/data__
Saat pengguna tidak memiliki __IP NID 10.151.252.0/22__ maka akan muncul halaman seperti di bawah ini

	IMG HERE

Sedangkan saat pengguna  memiliki __IP NID 10.151.252.0/22__ maka halaman yang muncul adalah seperti di bawah ini

	IMG HERE

-->

## I. Latihan
<!-- #### TBA -->
1. Download halaman jatim di 10.151.36.202/jatim.zip
2. Buat domain baru dengan nama __jatim.yyy.id__ untuk membuka halaman tersebut.
3. Atur agar jika kalian mengetikkan __jatim.yyy.id__, Web jatim dapat terbuka. 
### Catatan
+ Untuk download halaman web, gunakan perintah `wget 10.151.36.202/jatim.zip`
+ Kemudian unzip file tersebut. Jika muncul error seperti `unzip: command not found` maka install unzip terlebih dahulu menggunakan command `apt-get install unzip`.
+ Buat directory hasil unzip file tersebut menjadi _DocumentRoot_ web
+ Untuk nomor 2 dan 3, '__yyy__' diisi dengan nama kelompok. Contoh: __jatim.a04.id__


## Selamat belajar dan tetap semangat!
<p align="center">
	<img src="Gambar/klee.png" width="50%">
</p>

<!-- 
	<img src="Gambar/36.png" width="500">
	<img src="Gambar/37.png">
	<img src="Gambar/38.png" width="500">
	<img src="Gambar/39.png" width="500">
	<img src="Gambar/40.png" width="500">
	<img src="Gambar/41.png">
	<img src="Gambar/42.png" width="500">
	<img src="Gambar/43.png">
	<img src="Gambar/44.png">
 -->
