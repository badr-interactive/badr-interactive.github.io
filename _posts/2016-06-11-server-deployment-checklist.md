---
layout: post
title: Server Deployment Checklist
category: 'Deployment'
---

* TOC
{:toc}

Saat melakukan deployment server, hendaknya langkah-langkah di bawah ini dapat menjadi referensi agar tidak terperangkap berjam-jam karena kesalahan yang sama.

## 1. Periksa minimum server requirement untuk deployment aplikasi (versi PHP, MySQL)

Untuk developer Laravel, bisa menggunakan **vagrant** dengan mengatur spesifikasi vagrant seperti server target deployment.

## 2. Periksa domain, IP server, dan DNS server

Saat SSH tidak dapat dilakukan, cek terlebih dahulu domain dan IP address, apakah sama atau tidak. Server yang baru dimigrasi ke provider hosting lain memiliki kemungkinan DNS server belum di-point ke lokasi yang benar. Command seperti **ping**, **whois**, sangat berguna untuk memastikan keselarasan domain dan server.

<!-- break -->

## 3. Jika ingin akses SSH, kontak provider hosting

Pastikan akses SSH **telah diaktifkan** oleh provider hosting agar developer bisa melakukan SSH.

## 4. Akses SSH server remote: hanya public key yang diperlukan

Jika kita telah melakukan generate SSH key di komputer lokal, umumnya server remote hanya memerlukan **public key** yang kita generate agar kita bisa terhubung via SSH.

## 5. Penggunaan file config SSH untuk mengurangi pengetikan username, port, key yang digunakan

Setiap kali akses SSH, kita tidak perlu selalu mengetik username, hostname, port, key yang digunakan. Cukup masukkan konfigurasi tersebut di dalam file .ssh/config, maka setiap kali kita akses SSH bahkan bisa ada auto-complete hostname.

## 6. Nama binary PHP bisa tidak standar

Provider hosting yang memberikan keleluasaan kepada developer untuk menggunakan beragam versi PHP, kadang memberikan kesulitan tersendiri kepada developer. Selalu periksa terlebih dahulu versi PHP yang mana yang sedang aktif. Versi PHP untuk command line (php-cli) kadang bisa berbeda dengan versi PHP untuk web (php-cgi). Hal ini bisa menimbulkan masalah tersendiri saat memanggil command **artisan** untuk aplikasi Laravel.

## 7. Pencarian lokasi binary PHP

Untuk server dengan nama binary PHP yang berbeda-beda, umumnya bisa ditemukan di **/usr/bin/**.

## 8. Jika tidak standar, efek ke composer.json pada aplikasi Laravel

Nama binary php yang tidak standar dapat membuat developer tidak dapat menjalankan script composer.json untuk aplikasi laravel. Ubah terlebih dahulu bagian yang mengandung binary php menjadi nama binary php dengan versi pilihan anda.

## 9. Periksa ketersediaan binary composer

Tanyakan kepada provider hosting terlebih dahulu apakah binary composer telah disediakan, sebelum melakukan instalasi composer sendiri.

## 10. Jangan edit composer.phar

Masih selaras dengan nama binary php yang berbeda, saya pernah mencoba untuk melakukan edit pada composer.phar agar nama binary php hasil instalasi composer dapat selaras dengan yang sedang digunakan di server, namun hal ini justru dapat break sign archive dari composer.phar tersebut. (PHAR: PHP Archive) Pada akhirnya composer.phar tersebut tidak dapat digunakan untuk melakukan instalasi composer.

## 11. Jika composer update tidak bisa, disable scripts, terutama sebelum folder vendor ada.

Jika anda tidak dapat melakukan composer update, lakukan disable terhadap eksekusi bagian **scripts** di dalam composer.json dengan menggunakan option --no-scripts. (pada kasus saya, hal ini terjadi karena belum ada folder vendor)



Demikian, semoga infonya bermanfaat.
