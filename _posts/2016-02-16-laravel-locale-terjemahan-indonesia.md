---
layout: post
title: Laravel Locale Message Terjemahan Bahasa Indonesia
category: 'Web Development'
author: yahyaman
---

* TOC
{:toc}

Untuk kamu-kamu para laravelian (maksa :p), mungkin sudah tak asing dengan fitur Localization yang ada di dalam Laravel.
Penggunaannya pun gampang banget, tinggal panggil `trans($key)` di view tempat sebuah string itu diperlukan,
dan tadaa muncul lah string yang dimaksud, sesuai dengan setting-an bahasa di dalam config/app.php. Untuk cara
pakai yang lengkap silakan merujuk ke [dokumentasinya](https://laravel.com/docs/5.0/localization).

Secara default, Laravel menyediakan beberapa file pada folder `resource/lang/en` yang digunakan untuk beberapa
keperluan umum, contohnya pesan validasi error ketika submit form. File yang tersedia bisa bervariasi tergantung versi
laravel yang dipakai, namun umumnya ada file `validation.php` yang berisi pesan error ketika memvalidasi form.
Nah berhubung baru ada yang versi bahasa Inggris, saya ingin berbagi terjemahan bahasa Indonesia-nya.

Cara pakainya cukup mudah:

1. Set `locale` aplikasi ke `id`
2. Buat folder `id` di `resource/lang` atau gampangnya copy folder `en` terus rename jadi `id`
3. Copy text di bawah yang sesuai dengan nama filenya ke file yang ada di folder `id` tadi
4. Like this post (eh gak ada ya? :p)

<!-- break -->

## validation.php

Copy paste text di bawah ini sesuai kebutuhan:

~~~php
<?php

return array(

    /*
    |--------------------------------------------------------------------------
    | Validation Language Lines
    |--------------------------------------------------------------------------
    |
    | The following language lines contain the default error messages used by
    | the validator class. Some of these rules have multiple versions such
    | as the size rules. Feel free to tweak each of these messages here.
    |
    */

    'accepted'             => ':attribute harus diterima.',
    'active_url'           => 'Kolom :attribute bukan URL yang valid.',
    'after'                => 'Kolom :attribute harus tanggal setelah :date.',
    'alpha'                => 'Kolom :attribute hanya boleh memuat huruf.',
    'alpha_dash'           => 'Kolom :attribute hanya boleh memuat huruf, angka, dan strip.',
    'alpha_num'            => 'Kolom :attribute hanya boleh memuat huruf dan angka.',
    'array'                => 'Kolom :attribute harus pilihan lebih dari satu.',
    'before'               => 'Kolom :attribute harus tanggal sebelum :date.',
    'between'              => array(
        'numeric' => 'Kolom :attribute harus antara :min dan :max.',
        'file'    => 'Kolom :attribute harus antara :min dan :max kilobytes.',
        'string'  => 'Kolom :attribute harus antara :min dan :max karakter.',
        'array'   => 'Kolom :attribute harus berukuran antara :min dan :max.',
    ),
    'boolean'              => 'Kolom :attribute harus true atau false.',
    'confirmed'            => 'Kolom konfirmasi :attribute tidak cocok.',
    'date'                 => 'Kolom :attribute bukan tanggal yang benar.',
    'date_format'          => 'Kolom :attribute tidak cocok dengan format tanggal :format.',
    'different'            => 'Kolom :attribute dan :other harus berbeda.',
    'digits'               => 'Kolom :attribute harus :digits digit.',
    'digits_between'       => 'Kolom :attribute harus antara :min dan :max digit.',
    'email'                => 'Kolom :attribute harus alamat email yang valid.',
    'exists'               => 'Kolom :attribute yang dipilih tidak valid.',
    'filled'               => 'Kolom :attribute harus diisi.',
    'image'                => 'Kolom :attribute harus sebuah berkas gambar.',
    'in'                   => 'Kolom :attribute tidak valid.',
    'integer'              => 'Kolom :attribute harus angka bulat.',
    'ip'                   => 'Kolom :attribute harus alamat IP yang valid.',
    'json'                 => 'Kolom :attribute harus string JSON yang valid.',
    'max'                  => array(
        'numeric' => 'Kolom :attribute tidak boleh lebih besar dari :max.',
        'file'    => 'Kolom :attribute tidak boleh lebih besar dari :max kilobytes.',
        'string'  => 'Kolom :attribute tidak boleh lebih besar dari :max characters.',
        'array'   => 'Kolom :attribute tidak boleh lebih banyak dari :max items.',
    ),
    'mimes'                => 'Kolom :attribute harus berkas dengan tipe: :values.',
    'min'                  => array(
        'numeric' => 'Kolom :attribute paling kecil bernilai :min.',
        'file'    => 'Kolom :attribute paling kecil berukuran :min kilobytes.',
        'string'  => 'Kolom :attribute paling sedikit :min karakter.',
        'array'   => 'Kolom :attribute harus punya paling sedikit :min item.',
    ),
    'not_in'               => 'Kolom :attribute yang dipilih tidak valid.',
    'numeric'              => 'Kolom :attribute harus berupa angka.',
    'regex'                => 'Format :attribute tidak valid.',
    'required'             => 'Kolom :attribute harus diisi.',
    'required_if'          => 'Kolom :attribute dibutuhkan jika :other berisi :value.',
    'required_unless'      => 'Kolom :attribute dibutuhkan kecuali jika :other berisi :values.',
    'required_with'        => 'Kolom :attribute dibutuhkan jika :values ada.',
    'required_with_all'    => 'Kolom :attribute dibutuhkan jika :values ada.',
    'required_without'     => 'Kolom :attribute dibutuhkan jika :values tidak ada.',
    'required_without_all' => 'Kolom :attribute dibutuhkan jika semua :values tidak ada.',
    'same'                 => 'Kolom :attribute dan :other harus sama.',
    'size'                 => array(
        'numeric' => 'Kolom :attribute harus sama dengan :size.',
        'file'    => 'Kolom :attribute harus berukuran :size kilobytes.',
        'string'  => 'Kolom :attribute harus sebanyak :size karakter.',
        'array'   => 'Kolom :attribute harus berisi tepat :size item.',
    ),
    'string'               => 'Kolom :attribute harus berupa isian teks.',
    'timezone'             => 'Kolom :attribute harus berupa zona waktu yang valid.',
    'unique'               => 'Kolom :attribute sudah terdaftar di sistem.',
    'url'                  => 'Format :attribute tidak valid.',

);
~~~

## password.php

~~~php
<?php

return [

    /*
    |--------------------------------------------------------------------------
    | Password Reminder Language Lines
    |--------------------------------------------------------------------------
    |
    | The following language lines are the default lines which match reasons
    | that are given by the password broker for a password update attempt
    | has failed, such as for an invalid token or invalid new password.
    |
    */

    'password' => 'Kata sandi harus memiliki minimal 6 karakter dan sama persis dengan konfirmasi kata sandi.',
    'reset' => 'Kata sandi Anda telah diubah!',
    'sent' => 'Kami telah mengirim email berisi tautan untuk mengatur ulang kata sandi Anda!',
    'token' => 'Token untuk mengubah kata sandi tidak valid.',

];

~~~