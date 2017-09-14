---
layout: post
title: Deployment Aplikasi Laravel Menggunakan Git (Linux)
category: 'Deployment'
---

* TOC
{:toc}

Git merupakan salah satu tools yang digunakan sebagai source code version control system (vcs) dengan tujuan untuk menyimpan riwayat perubahan pada source code yang kita buat. Selain digunakan untuk menyimpan riwayat perubahan, ternyata git juga dapat digunakan untuk mempermudah proses deployment aplikasi web berbasis Laravel yang kita buat.

## Git Hooks

Git Hooks adalah kumpulan script yang secara otomatis akan dieksekusi pada saat terjadi event tertentu. Berdasarkan [dokumentasi](https://git-scm.com/book/en/v2/Customizing-Git-Git-Hooks) resmi pada website `git-scm.com`, terdapat dua jenis hooks yang disediakan oleh git yaitu `client hooks` dan `server hooks`. Client hooks adalah hooks yang ditrigger di local computer, sedangkan Server hooks merupakan hooks yang ditrigger di remote server.

<!-- break -->

Untuk keperluan deployment aplikasi Laravel kita akan menggunakan salah satu server hooks yaitu `post-receive`. `post-receive` hook merupakan hook yang ditrigger pada saat git selesai menjalankan proses update setelah kita melakukan `git push` ke remote server seperti misalnya `git push origin master`. Dengan menggunakan hook ini, kita akan membuat sebuah script sederhana yang nantinya akan kita gunakan untuk menjalankan proses deployement aplikasi web yang kita buat.

## Langkah - Langkah

1. SSH ke remote server
2. Install git
    * Ubuntu: `sudo apt-get instal git`
    * Centos: `yum isntall git` (sebagai Root)
3. Install [Composer](https://getcomposer.org/download/).
4. Buat direktori `/opt/repo` dengan menggunakan perintah `mkdir /opt/repo`.
5. Masuk kedalam direktori `/opt/repo`.
6. Jalankan perintah `git init --bare laravel.git`[^bare].
7. Buat sebuah direktori dengan nama `laravel` didalam direktori `/var/www` dengan menggunakan perintah `mkdir /var/www/laravel`.
8. Masuk kedalam direktori `laravel.git/hooks`.
9. Buat sebuah script dengan nama `post-receive` (misal: `touch post-receive`).
10. Tambahkan permission `execute` pada file `post-receive` dengan menggunakan perintah `chmod +x post-receive`.
11. Isi file `post-receive` dengan script dibawah ini[^dirname]:

        #! /bin/sh

        printf 'Checking out laravel repository into /var/www/ \n'
        git --work-tree=/var/www/laravel --git-dir=/opt/repo/laravel.git checkout -f

        printf 'Running composer install \n'
        cd /var/www/laravel
        composer install

        printf 'Changing directory permission \n'
        chown -R apache:apache /var/www/laravel
        printf 'Deployment success \n'

12. Tambahkan konfigurasi virtual host pada apache dengan megubah file `/etc/httpd/conf/httpd.conf` dan masukkan konfigurasi berikut ini dibagian paling bawah file `httpd.conf`:

        <VirtualHost *:80>
            ServerAdmin contact@alfath.co.id
            DocumentRoot /var/www/laravel/public
            ServerName localhost
            ErrorLog /var/log/laravel.log

            <Directory /var/www/laravel/public>
                Options Indexes FollowSymLinks MultiViews
                AllowOverride All
                Order allow,deny
                allow from all
            </Directory>
        </VirtualHost>

11. Tambahkan remote location baru di local repository dengan menggunakan perintah `git remote add live ssh://git@server-deployment.tld/opt/repo/laravel.git`
12. Untuk melakukan deployment aplikasi laravel ke server deployment, kita cukup menggunakan perintah `git push live master`.

## Catatan

Untuk alasan keamanan, secara default Laravel mengikutsertakan file `.env` kedalam `.gitignore` dan hal ini membuat file `.env` tidak tercatat di riwayat Git. Oleh karena itu, pada saat deployment pertama kali dengan menggunakan `git push live master`, kita perlu secara manual menambahkan file `.env` di dalam folder `/var/www/laravel`.

[^bare]: opsi `--bare` merupakan sebuah opsi pada git untuk membuat `bare repository` yang hanya berisi riwayat perubahan tanpa berkas source code.
[^dirname]: nama direktori `laravel` dapat diganti dengan nama lain sesuai dengan nama direktori yang dibuat pada langkah 5 dan 6.
