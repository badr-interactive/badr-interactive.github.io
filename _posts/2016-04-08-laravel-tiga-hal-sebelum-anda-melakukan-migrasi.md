---
layout: post
title: 3 Hal Sebelum Anda Melakukan Migrasi
category: 'Web Development'
---

Tercerahkan oleh Database Versioning, setelah sekian lama import-export SQL dan kirim lewat e-mail, google drive, dan sejenisnya?
Selamat datang di fitur migrasi. Mungkin pada awal saya memakai migrasi, sempet terpikir juga: buat apa ya struktur database dibuat kodenya?
Toh bisa kan buat lewat phpMyAdmin atau sejenisnya, lebih cepet pake GUI lagi.

Walau belakangan tobat sih, karena pakai migrasi kegunaannya banyak.
Pertama kali pakai migrasi, cukup frustasi juga karena kadang apa yang kita kira mudah,
ternyata lumayan ada rintangannya juga. Dalam surat ini saya bermaksud mengurangi
frustasi teman-teman sekalian. Iya apa semua siap? \*gaya disko

* TOC
{:toc}

## 1. Dropping Foreign Key

Biasanya aktivitas ini akan dilakukan ketika mengisi fungsi **down()** dari sebuah class migrasi.

~~~php
<?php
class UpdateAssignment extends Migration {

    public function up() {

        Schema::table('assignment_file', function(Blueprint $table) {
            $table->foreign('assignment_id')->references('id')->on('assignment')->onUpdate('CASCADE')->onDelete('CASCADE');
        });

    }

    public function down() {

        Schema::table('assignment_file', function(Blueprint $table) {
            $table->dropForeign('assignment_id');
        });

    }
}
?>
~~~

<!-- break -->

Kelihatan normal kan? Apa ada sesuatu yang aneh dari contoh kode di atas?

Jika dicoba rollback dari migrasi di atas, akan terjadi error bahwa index foreign key tidak ditemukan.
Mengapa hal tersebut terjadi? Karena parameter untuk fungsi dropForeign adalah **nama key**, bukan **nama kolom**.
Secara naluriah, kita akan langsung menambahkan **nama kolom** saat rollback penambahan foreign key.
Tapi kenyataannya tidak demikian.

~~~php
<?php
class UpdateAssignment extends Migration {

    public function up() {

        Schema::table('assignment_file', function(Blueprint $table) {
            $table->foreign('assignment_id', 'file_to_assignment_key')->references('id')->on('assignment')->onUpdate('CASCADE')->onDelete('CASCADE');
        });

    }

    public function down() {

        Schema::table('assignment_file', function(Blueprint $table) {
            $table->dropForeign('file_to_assignment_key');
        });

    }
}
?>
~~~

Sebaiknya anda selalu memberikan nama key setiap
menambahkan foreign key, agar foreign key anda dapat langsung di rollback dengan nama
key yang telah anda definisikan sebelumnya. (contoh di atas menggunakan nama **file_to_assignment_key**)
Jika tidak didefinisikan, maka Laravel akan mendefinisikan nama key default, umumnya berdasarkan
kedua nama tabel yang saling memiliki relasi.

Pemberian nama default ini kadang memberikan kita permasalahan baru.

## 2. Foreign Key Default Naming

Ingat ingat!
Foreign key default naming berbeda untuk **Laravel** dan **phpMyAdmin**!

Katakanlah kita ingin membiarkan migrasi Laravel memberikan nama untuk foreign key
yang telah kita buat. (*Katakan kita sudah terlalu pusing, deadline dekat, dsb*)
Kemudian untuk nama foreign key di rollback, kita lihat dulu nama key tersebut,
setelah foreign key terbuat (setelah di migrasi) baru kita tambahkan ke rollback.
Bisa begitu juga sih caranya. (*Pertama kali dulu begitu*) Tetapi hal ini bisa menimbulkan
efek samping.

~~~php
<?php
class UpdateKegantengan extends Migration {

    public function up() {

        Schema::table('tabel_tingkat_kegantengan_anak_badr', function(Blueprint $table) {
            $table->foreign('level_kegantengan_anak_anak_badr')->references('id')->on('definisi_tingkat_kegantengan_anak_anak_badr')->onUpdate('CASCADE')->onDelete('CASCADE');
        });

    }

    public function down() {

        Schema::table('tabel_tingkat_kegantengan_anak_badr', function(Blueprint $table) {
            $table->dropForeign('perkiraan_nama_key_default');
        });

    }
}
?>
~~~

Apakah kira-kira nama foreign key default yang akan digenerate oleh laravel?
Hasilnya adalah **tabel_tingkat_kegantengan_anak_badr_level_kegantengan_anak_anak_badr_foreign**,
yang seringkali terlihat lengkap di phpMyAdmin, namun ternyata telah dipotong (truncate)
oleh database server, dalam hal ini MySQL. (umumnya MySQL membatasi nama key hanya sampai 64 karakter)

Akibatnya, rollback kita akan gagal, meski kita telah menyertakan nama lengkap dari foreign key,
karena nama foreign key yang sebenarnya telah di-truncate.

## 3. Foreign Key on Auto Increment Column

Dalam pembuatan tabel, kita sering menggunakan ID auto-increment. Kemudian dari tabel lain,
kita melakukan referensi foreign key dari tabel lain ke tabel kita yang baru di buat ini.
Di migrasi, kadang pembuatan referensi foreign key ini tidak selalu berjalan mulus.

~~~php
<?php
class UpdateAssignment extends Migration {

    public function up() {

        Schema::table('assignment_file', function(Blueprint $table) {
            $table->foreign('assignment_id', 'file_to_assignment_key')->references('id')->on('assignment')->onUpdate('CASCADE')->onDelete('CASCADE');
        });

    }

    public function down() {

        Schema::table('assignment_file', function(Blueprint $table) {
            $table->dropForeign('file_to_assignment_key');
        });

    }
}
?>
~~~

Saat kita melakukan migrasi, tiba-tiba muncul error **can't create foreign key** atau sejenisnya.
Hal yang harus dilakukan pertama kali adalah memeriksa, apakah kolom yang kita referensikan
memiliki **tipe data yang sama** dengan kolom di tabel kita. Pada migrasi Laravel kita membuat kolom
auto-increment seperti ini:

~~~php
public function up() {

    Schema::table('assignment', function(Blueprint $table) {
        $table->increment("id");
    });

}
~~~

Namun setelah dilihat ke struktur database, ternyata kolom auto-increment tersebut
memiliki tipe **unsigned integer (10)**, bukan **integer (11)**. Hal ini seringkali menimbulkan error.
Obat dari masalah ini mudah: **samakan tipe kedua kolom yang akan saling memiliki referensi tersebut**.

~~~php
public function up() {

    Schema::table('assignment_file', function(Blueprint $table) {

        // ganti dari integer() menjadi unsignedInteger()
        $table->unsignedInteger('assignment_id');
    });

}
~~~

Untuk menyelesaikan masalah ini, anda dapat mengganti penggunaan incremental field pada tabel assignment dengan fungsi generate integer biasa, ditambah dengan flag auto increment.

~~~php
public function up() {

    Schema::table('assignment', function(Blueprint $table) {
        $table->integer("id", true);
    });

}
~~~

Demikian, semoga infonya bermanfaat.
