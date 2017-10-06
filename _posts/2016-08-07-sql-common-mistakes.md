---
layout: post
title: SQL Common Mistakes
category: 'Database'
---

* TOC
{:toc}

Seringkali orang berpikir saat aplikasi berjalan lambat, banyak yang menyalahkan bahasa pemrograman yang digunakan, framework, dan hal lainnya yang sebetulnya tidak mereka cek **sebenar-benarnya** dimana letak kesalahannya.
Kali ini Saya ingin menjelaskan mengenai beberapa _common mistakes_ yang biasa dilakukan saat kita melakukan query ke database.
Hal umum seperti ini terjadi saat kita berurusan dengan _data yang banyak_, umumnya ketika membuat pengolahan **laporan** dari data - data yang sudah ada.

<!-- break -->

Contoh benchmark dan environment yang saya lakukan adalah sebagai berikut :
```
CPU Quad Core
RAM 4GB
HDD 5400 RPM
Apache 2.4
PHP 5.6
MariaDB 10.1
Row data : 6M+
```

1. Penggunaan sintaks dasar mysql ketika membuat laporan berperiode (mingguan, bulanan, tahunan, dsb) seperti ```WEEKOFYEAR```, ```MONTH```, ```YEAR```. 
Salah : ```SELECT `beberapafield` FROM `namatabel` WHERE MONTH(tanggal) = '07' AND YEAR(tanggl) = '2016'``` 
Execution Time : 60 - 300 seconds = 1 - 5 minutes.
Benar : ```SELECT `beberapafield` FROM `namatabel` WHERE tanggal BETWEEN '2016-07-01' AND '2016-07-31'```
Execution Time : 0.xxx - 2 seconds.
Jika membuat query pencarian pada tanggal, carilah menggunakan field tanggal daripada mengkonversi dalam bentuk minggu, bulan ataupun tahun.

2. Kesalahan membuat _index_ pada tabel, ada 2 cara yang saya ketahui saat membuat index pada tabel :
	a. Index pada satu per satu nama column. Contoh : ```INDEX `namaindex` (`namafield`)```
	b. Index gabungan dari beberapa column. Contoh : ```INDEX `namaindex` (`field1`,`field2`,`field3`)```
Kedua hal tersebut bisa jadi benar saat melakukan optimasi pada tabel, namun pada dasarnya pembuatan index mengacu pada kebutuhan saat melakukan query ke tabel tersebut.
Field atau kolom yang diindex, harusnya adalah field yang sering diakses oleh query saat melakukan fungsi **Aggregate, Pencarian, Grouping** dan query lainnya yang berinteraksi melakukan pencarian data pada tabel tersebut.
Contoh : ```SELECT SUM(field1), * FROM tabel WHERE field2 = 'A' AND field3 = 'B' GROUP BY field4``` jika **field1,field2,field3,field4** sering digunakan dalam pencarian akses ke tabel tersebut, indexlah field tersebut agar query teroptimasi dan eksekusinya lebih cepat saat dijalankan.

Tulisan ini dibuat berdasarkan pengalaman yang saya temukan saat membuat laporan dengan environment yang telah disebutkan di atas, saat ini saya pun masih belajar bagaimana _best practices_ dalam mengatasi kasus - kasus di atas.
Demikian, semoga bermanfaat. :)