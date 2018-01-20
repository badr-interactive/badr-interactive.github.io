---
layout: post
title: SOP Development (Programmer)
category: 'Internal'
author: salman
---

* TOC
{:toc}

## Penggunaan Bahasa

1. Penamaan variable, class, method dan dokumentasi pada kode program secara default menggunakan bahasa Inggris.

2. Penamaan tabel dan kolom pada database secara default menggunakan bahasa Inggris tanpa menggunakan prefix / suffix. Aturan penamaan sebagai berikut:
    * **Plural noun** (misal: users, products dan classes) untuk nama tabel
    * **Singular noun** (misal: name, email dan password) untuk nama kolom / atribut.

3. Penggunaan bahasa pada tampilan antar muka aplikasi (misal: navigasi, tabel, tombol dan dialog) secara default menggunakan bahasa Indonesia.

4. Setiap aturan penggunaan bahasa yang tercantum pada poin ‘a’ sampai ‘c’ di atas dapat disesuaikan dengan permintaan dari product owner / client.

5. Setiap aturan penggunaan bahasa yang tercantum pada poin ‘a’ sampai ‘d’ di atas harus diterapkan secara konsisten dan menyeluruh.

<!-- break -->

## Konvensi Penulisan kode

1. Standar gaya penulisan kode yang digunakan untuk bahasa pemrograman **PHP** adalah [PSR-2](http://www.php-fig.org/psr/psr-2/).

2. Standar gaya penulisan kode yang digunakan untuk bahasa pemrograman **JAVA / Android** adalah [Google Java Style](https://google.github.io/styleguide/javaguide.html).

3. Standar gaya penulisan kode yang digunakan untuk bahasa pemrograman **iOS / Swift** adalah Dayat’s Style.

## Version Control System (VCS)

1. Setiap kode yang dibuat dengan tujuan untuk pengerjaan proyek komersial dan internal PT. Badr Interactive wajib disimpan ke dalam Repository Gitlab Badr Interactive yang beralamat di http://gitlab.badr.co.id.

2. Setiap repository kode proyek komersial dan internal PT. Badr Interactive yang tersimpan di server Gitlab Badr Interactive wajib dimasukkan kedalam group **Badr Interactive** yang terletak di alamat http://gitlab.badr.co.id/groups/badr-interactive.

3. Setiap repository kode proyek komersial dan internal PT. Badr Interactive sekurang-kurangnya memiliki 2 (dua) buah branch yang diantaranya adalah:
    * **Master**: Branch ‘master’ pada git ditujukan untuk ‘stable code’ yang siap diberikan atau direview oleh product owner / client.
    * **Develop**: Branch ‘develop’ pada git ditujukan untuk kegiatan pengembangan sehari-hari dan tidak direkomendasikan untuk diberikan ke orang lain selain programmer.

4. Apabila ditemukan **defect** atau terjadi **failure** pada kode yang berada di branch **master** maka perlu diberlakukan mekanisme **hotfix** dengan cara membuat branch **hotfix** yang bersumber dari **HEAD** branch **master**.

5. Setiap branch **hotfix** yang telah berhasil memperbaiki defect atau failure pada branch **master** harus digabungkan kembali dengan branch **master** melalui mekanisme **merge** dari branch **hotfix** ke branch **master**.

6. Setelah selesai melakukan **hotfix** pada branch **master**, branch **develop** harus mendapatkan perubahan terakhir pada branch **master** dengan cara melakukan proses **merge** dari branch **master** ke branch **develop**.

## Version Number

1. Setiap aplikasi yang dibuat untuk keperluan proyek komersial dan internal PT. Badr Interactive diharuskan memiliki version number.

2. Format dari version number yang digunakan oleh PT. Badr Interactive adalah format dari Semantic Versioning 2.0.0 (SemVer 2.0.0) dengan pola X.Y.Z yang memiliki arti:
    * **X (Major Version)**: Merupakan versi yang diberikan kepada aplikasi pada saat melakukan perubahan yang berdampak pada arsitektur, framework ataupun core dari aplikasi yang bersangkutan.
    * **Y (Minor Version)**: Merupakan versi yang diberikan kepada aplikasi pada saat melakukan penambahan, pengubahan atau penghapusan sebuah fitur.
    * **Z (Patch Version)**: Merupakan versi yang diberikan kepada aplikasi pada saat melakukan perbaikan (bug fixing) pada sebuah fitur.

3. Setiap aplikasi yang dibuat dimulai dengan versi **0.1.0** dan akan berubah menjadi versi **1.0.0** ketika sudah siap diberikan kepada product owner / client dan masuk ke fase production untuk yang pertama kalinya.

4. Kenaikan versi hanya terjadi apabila project manager sudah menyepakati bahwa aplikasi yang dibuat sudah layak untuk di-release / dipresentasikan kepada product owner / client.

5. Setiap terjadi kenaikan versi pada sebuah aplikasi, developer diharuskan untuk membuat ‘annotated tagging’ dengan nama sesuai dengan nomor revisi terakhir dengan format **vX.Y.Z** (misal v1.1.4) pada HEAD milik branch ‘master’.

5. Setiap terjadi kenaikan versi pada sebuah aplikasi, developer diharuskan untuk membuat changelog perubahan yang terjadi yang disimpan didalam berkas bernama “changelog” atau “changelog.txt” yang berisi tentang perubahan apa saja yang terjadi dari versi sebelumnya seperti contoh dibawah ini:

        Changelog:

        Ver. 1.2.1
        Perbaikan bug saat mengirim email notifikasi pada modul salary untuk HR Manager.
        Security fix SQL Injection pada halaman login.

        Ver. 1.2.0
        Penambahan fitur export to excel pada modul laporan keuangan
        Penambahan modul salary untuk HR Manager.

        Ver. 1.9.24
        Security fix: Menganggulangi CSRF dengan cara mengimplementasikan CSRF token pada setiap request bertipe POST.

## Penjaminan Mutu

1. Setiap developer diharuskan untuk menjalankan mekanisme software testing pada bagian kode yang dibuatnya sampai pada tahap developer tersebut memiliki kepercayaan diri bahwa bagian kode dibuatnya dapat bekerja sesuai dengan ekspektasi.

2. Setiap developer diharuskan untuk mendokumentasikan kegiatan software testing yang dilakukannya untuk kemudian dijadikan sebagai bukti bahwa bagian kode yang dibuat sudah lulus dari proses software testing.

## Dokumentasi

1. Setiap developer diharuskan untuk membuat kode yang mudah dipelajari oleh pengembang lain.

2. Setiap developer diharuskan untuk membuat kode yang mudah dipelajari oleh pengembang lain.

3. Setiap method yang dibuat harus memiliki dokumentasi yang sekurang-kurangnya menjelaskan hal - hal berikut ini:
    * Tujuan utama dari method yang dibuat.
    * Parameter yang dibutuhkan.
    * Return value yang akan diberikan.

4. Format penulisan dokumentasi program harus konsisten dan seragam dalam satu aplikasi yang sama.

5. Format penulisan dokumentasi program harus konsisten dan seragam dalam satu aplikasi yang sama.

6. Penulisan dokumentasi untuk method yang memiliki nama dan fungsi yang sederhana dan jelas seperti misalnya (`getAddress`, `getName` dan `getEmail`) bersifat opsional. Akan tetapi, jika method yang dibuat “terlihat” sederhana dan jelas namun memiliki logic atau prosedur yang tidak biasa, maka penulisan dokumentasi menjadi hal yang diharuskan.
