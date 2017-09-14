---
layout: post
title: Menghilangkan Path "public" pada URL Aplikasi Laravel
category: 'Deployment'
---

* TOC
{:toc}

Secara default setelah melakukan deployment website berbasiskan Laravel, kita akan dapat mengakses website kita pada alamat `http://mydomain.tld/public`. Untuk dapat menghilangkan tulisan `public` pada URL website kita tanpa harus memindahkan isi dari direktori `public` tersebut kedalam document root `public_html` adalah dengan cara membuat berkas `.htaccess` di dalam folder `public_html` dengan konten seperti dibawah ini:

~~~
<IfModule mod_rewrite.c>
   RewriteEngine On
   RewriteRule ^(.*)$ public/$1 [L]
</IfModule>
~~~

Konfigurasi `.htaccess` diatas akan menambahkan path `public` secara otomatis pada setiap request yang masuk kedalam web server kita. Dengan menggunakan cara ini kita dapat mengakses website Laravel kita dengan URL `http://mydomain.tld/` (tanpa path `public`).
