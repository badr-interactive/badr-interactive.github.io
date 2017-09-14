---
layout: post
title: Melakukan Deployment Aplikasi Laravel pada CPanel
category: 'Deployment'
---

* TOC
{:toc}

## Introduction

Pada artikel sebelumnya telah dibahas tentang ["Melakukan Deployment Aplikasi Laravel pada VPS"]({{site.baseurl}}{% post_url 2016-03-09-laravel-deployment-pada-vps %}). Kali ini, akan dibahas tentang bagaimana melakukan proses deployment aplikasi berbasis laravel pada server dengan akses CPanel. Secara umum, proses deployment yang dilakukan relatif sama hanya saja yang membedakan adalah pada server CPanel kita tidak memiliki akses console sehingga tidak dapat menjalankan perintah `git clone` atau `composer install`. Untuk mengatasi hal tersebut, kita harus mengupload secara manual aplikasi laravel berikut dengan direktori `vendor`nya.

<!-- break -->

## Server Requirement

1. PHP >= 5.5.9
2. OpenSSL Extension
3. PDO PHP Extension
4. Mbstring PHP Extension
5. Tokenizer PHP Extension

## Deployment

1. Zip aplikasi laravel beserta direktori `vendor`nya (misal: `laravel.zip`).
2. Login kedalam CPanel
3. Buka `File manager` CPanel

    ![image]({{ site.baseurl }}/public/images/cpanel_home.png){:width="100%"}

4. Upload dan ekstrak `laravel.zip` kedalam direktori `/home/your_accout/` sehingga folder `laravel` tersebut akan berada di lokasi `/home/your_account/laravel`

    ![image]({{ site.baseurl }}/public/images/cpanel_filemanager.png){:width="100%"}

5. Masuk ke dalam direktori `laravel`, copy direktori `public` dan paste kedalam direktori `/home/your_account/public_html/` sehingga direktori `public` tersebut akan berada di lokasi `/home/your_account/public_html/public`.

    ![image]({{ site.baseurl }}/public/images/cpanel_public_html.png){:width="100%"}

6. Buka file `index.php` yang berada di dalam direktori `public_html/public` dan edit baris kode berikut ini:

        require __DIR__.'/../bootstrap/autoload.php';
        $app = require_once __DIR__.'/../bootstrap/app.php';

    hingga menjadi:

        require __DIR__.'/../../laravel/bootstrap/autoload.php';
        $app = require_once __DIR__.'/../../laravel/bootstrap/app.php';

7. Sampai pada tahap ini proses deployment aplikasi berbasis laravel sudah selesai dan kita dapat mengakses website yang kita buat pada alamat `http://mydomain.tld/public`.
8. Untuk menghilangkan path `public` pada URL website kita, silahkan merujuk para artikel [Menghilangkan Path "public" pada URL Aplikasi Laravel]({{site.baseurl}}{% post_url 2016-03-14-laravel-menghilangkan-public-pada-url %}).
