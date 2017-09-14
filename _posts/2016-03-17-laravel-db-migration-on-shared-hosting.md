---
layout: post
title: Migrasi Database Laravel di Shared Hosting
category: 'Deployment'
author: yahyaman
---

* TOC
{:toc}

Salah satu fitur yang sering digunakan di dalam Laravel adalah __Database Migration__[^migrations].
Simply say, fitur ini adalah seperti _version control_ untuk database. Hal ini sangat bermanfaat
salah satunya saat suatu aplikasi dikerjakan oleh banyak orang. Jika ada satu orang melakukan perubahan,
maka developer lain dapat dengan mudah melakukan perubahan yang sama secara otomatis.

Saat masa development, kita biasanya menggunakan komputer sendiri sebagai database server `(localhost)`.
Namun, ada kalanya kita diminta untuk mendeploy saat masa development agar bisa dites oleh
klien. Jika hal ini terjadi secara berkala (misal tiap selesai sprint), bakal repot banget untuk
me-redeploy struktur database jika tidak menggunakan _migration_.

Nah, di sini akan coba dijelaskan salah satu cara mudah untuk melakukan _database migration_ yang
dimiliki Laravel pada _shared hosting_ yang umumnya digunakan oleh klien.

<!-- break -->

## Persyaratan

* Laravel >= 5.0
* cPanel hosting dengan fitur __Remote MySQL&reg;__

Cara cek: di cPanel terdapat menu seperti gambar di bawah ini
![cpanel-remote-mysql]({{ site.baseurl }}/public/images/web/remote-mysql-01.png)

## Persiapan

### 1. Izinkan Remote Access MySQL

Masuk ke menu __Remote MySQL&reg;__, dan masukkan [Public IP](http://id.lmgtfy.com/?q=my+ip) kita ke dalam daftar yang diizinkan.
Contohnya bisa lihat gambar di bawah:
![cpanel-remote-mysql]({{ site.baseurl }}/public/images/web/remote-mysql-02.png)

Ini dibutuhkan agar kita dapat mengakses _database server_ yang ada di hosting.

### 2. Persiapkan Database

Pastikan sudah membuat database yang akan digunakan. Harusnya cincai lah ya bagian yang ini :p.
Setidaknya siapkan informasi sebagai berikut:

* Hostname : alamat server databasenya, biasanya sama dengan alamat/domain dari cPanel-nya.
* Database name : nama database yang akan digunakan
* Database username : username yang diizinkan mengakses database
* Database password : passwordnya

### 3. Update .env

Tambahkan informasi koneksi database tadi ke dalam file `.env` aplikasi kita. Contohnya sebagai berikut:

    #live server
    LIVE_DB_HOST=srv10.niagahoster.com
    LIVE_DB_DATABASE=db_nyari_murid
    LIVE_DB_USERNAME=nyari_murid
    LIVE_DB_PASSWORD=password

### 4. Update Database Config

Nah sekarang kita buat konfigurasi koneksi database baru di dalam Laravel. Buka `config/database.php`,
cari bagian _Database Connections_ dan tambahkan konfigurasi baru, seperti contoh kode di bawah ini:

~~~php
'connections' => [
        ...

        /* koneksi baru */
        'live_mysql' => [
            'driver'    => 'mysql',
            'host'      => env('LIVE_DB_HOST', 'localhost'),
            'database'  => env('LIVE_DB_DATABASE', 'forge'),
            'username'  => env('LIVE_DB_USERNAME', 'forge'),
            'password'  => env('LIVE_DB_PASSWORD', ''),
            'charset'   => 'utf8',
            'collation' => 'utf8_unicode_ci',
            'prefix'    => '',
            'strict'    => false,
        ],

        ...
    ],
~~~

## Eksekusi

Sip, persiapan sudah selesai, tinggal jalankan _command_ untuk melakukan migrasi[^runmigrate]. Karena
kita ingin mengupdate database yang ada di live server, jangan lupa tambahkan option seperti di bawah ini:

`> php artisan migrate --database=live_mysql`

Taraa~~
database yang ada di live server pun (mestinya) sudah terupdate dengan struktur terbaru.
Oya, semua nama variabel seperti `LIVE_DB_HOST` atau `live_mysql` bisa diganti-ganti sesuka hati,
yang penting bener aja nge-refer nya.

## Referensi

[^migrations]: [Dokumentasi Migrations](https://laravel.com/docs/5.0/migrations)
[^runmigrate]: [Menjalankan _migration_](https://laravel.com/docs/5.0/migrations#running-migrations)