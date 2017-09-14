---
layout: post
title: Melakukan Deployment Aplikasi Laravel pada VPS
category: 'Deployment'
---

* TOC
{:toc}

## Introduction

Framework Laravel merupakan framework yang agak unik dalam hal deployment. Mengapa demikian? Jika kita lihat pada struktur direktori laravel maka kita akan mendapati sebuah folder dengan nama `public`. Folder `public` ini merupakan satu-satunya folder yang nantinya akan diletakan di dalam document root pada web server apache (misal: `/var/www/html`). Mengapa demikian? hal ini dilakukan untuk meningkatkan keamanan dari aplikasi web yang dibuat. Dengan meletakkan folder non-public `app, bootstrap, config, database, .env, dll` diluar document root apache, maka direktori-direktori tersebut tidak akan dapat diakses secara langsung oleh user melalui web browser mereka.

Lalu, bagaimana cara melakukan deployment aplikasi web berbasis laravel yang baik dan benar? berikut ini adalah langkah-langkah yang harus dilakukan dalam melakukan deployment aplikasi laravel pada server VPS (Virtual Private Server, Non-Cpanel) dimana kita memiliki akses terhadap root user dan terminal melalui ssh. Asumsi yang digunakan adalah bahwa kita menyimpan source code kita di dalam cloud repository seperti gitlab, github atau bitbucket.

<!-- break -->

## Server Requirement

1. PHP >= 5.5.9
2. OpenSSL Extension
3. PDO PHP Extension
4. Mbstring PHP Extension
5. Tokenizer PHP Extension
6. [Git](https://git-scm.com/downloads)
7. [Composer](https://getcomposer.org/download/)

## Deployment

1. Clone repository project laravel yang kita buat dengan mengetikkan perintah `git clone` (misal: `git clone https://server.badr.co.id:8001/salman.farisi/my-web.git`).
2. Masuk kedalam direktori project (misal: `cd my-web`).
3. Jalankan `composer install` untuk menginstall seluruh dependency yang dimiliki.
4. Pindahkan folder project `my-web` kedalam direktori `/var/www`.
5. Masuk kedalam direktori `/var/www/my-web`.
6. Copy direktori `public` ke dalam direktori document root apache `/var/www/html/`.
7. Edit file `/var/ww/html/public/index.php` dan ubah baris berikut ini:


        require __DIR__.'/../bootstrap/autoload.php';
        $app = require_once __DIR__.'/../bootstrap/app.php';

    hingga menjadi:

        require __DIR__.'/../../my-web/bootstrap/autoload.php';
        $app = require_once __DIR__.'/../../my-web/bootstrap/app.php';

8. Ubah permission direktori `/var/www/my-web` dengen mengetikkan perintah `chown -R apache:apache /var/www/my-web`.
9. Ubah permission direktori `/var/www/html/public` dengan mengetikkan perintah `chown -R apache:apache /var/www/html/public`.
10. Jangan lupa untuk mengubah konfigurasi aplikasi pada file `/var/www/my-web/.env` agar sesuai dengan konfigurasi server yang sudah disiapkan.
11. Hapus baris yang mengandung `APP_ENV=local` pada file `/var/www/my-web/.env`. Untuk menjadikan aplikasi kita masuk kedalam mode production.
12. Sampai pada tahap ini proses deployment aplikasi berbasis laravel sudah selesai dan kita dapat mengakses website yang kita buat pada alamat `http://mydomain.tld/public`.
13. Untuk menghilangkan path `public` pada URL website kita, silahkan merujuk para artikel [Menghilangkan Path "public" pada URL Aplikasi Laravel]({{site.baseurl}}{% post_url 2016-03-14-laravel-menghilangkan-public-pada-url %}).

## Penanganan Permission Denied

Terkadang ada kasus dimana kita diberikan akses user non-root dan direktori `document root` dari apache mengarah ke direktori yang dimiliki oleh user tertentu seperti misalnya `dayat`, `pulung` atau `bambolz`. Untuk memastikannya kita bisa menggunakan perintah `ls -al` untuk melihat user dan group permission dari `document root` yang kita miliki. sebagai contoh, lihat output dari peintah `ls -al` untuk direktori `/var/www/public_html` dibawah ini:

    [bambolz@dev-badr public_html]$ ls -al
    total 20
    drwxr-xr-x 4 bambolz apache 4096 Mar 29 18:55 .
    drwxr-xr-x 5 bambolz apache 4096 Mar 30 08:43 ..
    drwxr-xr-x 2 bambolz apache 4096 Mar 29 19:09 myapp
    drwxr-xr-x 2 bambolz apache 4096 Mar 29 19:04 myapp-api
    -rw-r--r-- 1 bambolz apache   36 Mar 28 14:24 index.html

Output diatas menunjukkan bahwa file / direktori yang berada di dalam `document root` kita memiliki owner bernama `bambolz` dan group `apache`. Berdasarkan informasi ini kita dapat berasumsi bahwa user `bambolz` sudah dimasukkan kedalam group `apache` sehingga user `bambolz` bisa menulis di dalam direktori tersebut tetapi user `apache`[^apacheuser] hanya bisa membaca (karena bukan owner). Untuk membuat user `apache` dapat menulis di dalam direktori tertentu (misal: direktori `storage/log` pada Laravel), kita harus menambahkan permission `write (w)` untuk `group (g)` dengan cara mengetikkan perintah:

`chmod g+w storage/log`

atau

`chmod -R g+w storage/`

Dengan menerapkan cara diatas, user `apache` (web server) sudah memiliki permission untuk menulis di dalam direktori `storage` sehingga error `permission denied` tidak akan terjadi lagi.

[^apacheuser]: Secara default apache web server berjalan dengan menggunakan user `apache` (Centos, RHEL dan turunannya) atau `www-data` (Ubuntu, Debian dan turunannya). sehingga setiap operasi yang dilakukan (misal: menulis dan membaca file) akan menggunakan user tersebut.
