---
layout: post
title: Simple Access Restriction using Laravel Middleware
category: 'Web Development'
author: yahyaman
---

* TOC
{:toc}

Dalam framework Laravel, __Middleware__ adalah salah satu fitur yang banyak gunanya.
Secara konsep, middleware adalah sebuah _layer_ dalam setiap _http request_ dari klien, bisa sebelum atau sesudah
Controller dijalankan. Jadi kira-kira gambarannya seperti ini: `request > before-middleware > controller > after-middleware > response`.
Penggunaan paling umum biasanya digunakan untuk _access restriction_ alias pembatasan akses untuk sebuah resource/route,
bisa role-based ataupun pengecekan lainnya.

Ada sedikit perbedaan middleware di Laravel 5 dan 5.1, yaitu kemampuan middleware untuk menerima paramater[^release]. Di laravel 5,
middleware tidak bisa menerima argumen tambahan yang kita definisikan sendiri. Jadi akan sedikit ribet kalau misal
kita mau bikin role-based restriction, karena kita harus bikin middleware untuk setiap role yang ada.
Nah tentu akan lebih fleksibel kalau kita bisa mengirim argumen tambahan, so kita pakenya yang 5.1 ke atas aja ya! :D

<!-- break -->

## Konsep

Ada dua pendekatan yang bisa kita gunakan untuk pembatasan akses, yaitu:

* __Blacklist__: artinya secara default akses dibuka, tapi khusus untuk role tertentu dilarang untuk mengakses.
* __Whitelist__: sebaliknya, secara default akses ditutup, tapi khusus untuk role tertentu baru boleh mengakses.

Mau pilih mana? terserah, kedua-duanya juga boleh. Sekarang kita bikin aja dulu semuanya.

## Persiapan

1. Model user yang digunakan ialah `App\User`. Diasumsikan model tersebut hanya bisa memiliki satu role saja sebagai atribut.
Jadi kita bisa akses user role-nya dengan cara `user->role` dan akan mengembalikan string
(misal _'admin', 'manager'_).

2. Selanjutnya kita buat Class middleware-nya di direktori `app/Http/Hiddleware`. Biar gampang, pake aja
`artisan command`[^command]:
    * __Blacklist__: `php artisan make:middleware ForbidAccess`.
    * __Whitelist__: `php artisan make:middleware AllowAccess`.

3. Tambahkan argumen `$roles` pada method `handle` di kode yang digenerate, jadinya seperti berikut:

        <?php

        namespace App\Http\Middleware;

        use Closure;

        class AllowAccess //dan atau BlockAccess
        {
            /**
             * Handle an incoming request.
             *
             * @param  \Illuminate\Http\Request  $request
             * @param  \Closure  $next
             * @return mixed
             */
            public function handle($request, Closure $next, $roles)
            {
                return $next($request);
            }
        }


4. Daftarkan middleware kita di `app/Http/kernel.php` sebagai route middleware[^register]:

        protected $routeMiddleware = [
            // ...laravel default middlewares...
            //custom middleware
            'forbid' => \App\Http\Middleware\ForbidAccess::class,
            'allow' => \App\Http\Middleware\AllowAccess::class,
        ];

## Authorization

### Basic

Untuk __whitelist__, pengecekan sederhana kita bisa gunakan struktur kontrol `if` sebagai berikut:

~~~~php
public function handle($request, Closure $next, $roles)
{
    $user = $request->user();
    if (!is_null($user) && strcmp($user->role, $roles) == 0) {
        return $next($request);
    }
    throw new AccessDeniedHttpException;
}
~~~~

Fungsi php `strcmp` digunakan untuk membandingkan dua buah string secara _binary-safe_[^strcmp]. Dari kode di atas kita bisa lihat:
kecuali klien adalah User yang memiliki role `$roles`, maka semua akses akan direspon sebagai `Access Denied (HTTP 403)`.

Untuk pendekatan __blacklist__, pengecekannya serupa tapi tak sama:

~~~~php
public function handle($request, Closure $next, $roles)
{
    $user = $request->user();
    if (is_null($user) || strcmp($user->role, $roles) == 0) {
        throw new AccessDeniedHttpException;
    }
    return $next($request);
}
~~~~

Yang dilarang mengakses hanya User dengan role `$roles`, lainnya boleh. Nah contoh di atas hanya bisa dipakai dengan
satu argumen `$roles` aja. Gimana kalau mau ngeblok atau ngasih akses ke lebih dari satu role untuk resource yang sama?

### Advance

Setidaknya ada dua cara untuk menangani akses untuk lebih dari satu role, yaitu:

1. Satu argumen dengan _delimiter_

    Ini cara yang agak primitif sih sebenernya, hehe. Jadi argumennya punya delimiter misal `'|'` (pipe)
    untuk menandai role apa aja yang dikirim. Jadi nanti ketika middleware digunakan, kita akan mengharap
    stringnya berbentuk kira-kira begini: `admin|manager|operator`. Maka pengecekannya menjadi (untuk __whitelist__):

        public function handle($request, Closure $next, $roles)
        {
            $roles = explode('|', $roles);
            $user = $request->user();
            if (!is_null($user) && in_array($user->role, $roles, true)) {
                return $next($request);
            }
            throw new AccessDeniedHttpException;
        }

    Bisa juga sih dicek dengan cara `strpos($roles, $user->role) !== false`, tapi lebih intuitif dipecah dulu terus
    dicek kayak di atas. Terserah yang bikin aplikasinya aja lah, hehe.

2. Multi argumen

    Laravel membolehkan mengirim argumen lebih dari satu ke middleware. Caranya bisa lihat di bagian berikutnya.
    Kalau mau pake cara ini, supaya banyaknya argumen bisa fleksibel, kita gak perlu mendefinisikan `$roles`.
    Nantinya bakal jadi begini:

        public function handle($request, Closure $next)
        {
            $roles = array_slice(func_get_args(), 2);
            $user = $request->user();
            if (!is_null($user) && in_array($user->role, $roles, true)) {
                return $next($request);
            }
            throw new AccessDeniedHttpException;
        }

    Bedanya cuma cara dapetin array `$roles` yang bakal dicek aja. Fungsi `func_get_args()` bakal ngasih array
    yang berisi argumen yang dikirim saat pemanggilan fungsi `handle` di atas. Dan karena udah pasti ada 2 argumen
    yang dikirim secara default, maka kita hanya mengambil argumen ke-3 dan seterusnya. Kalau gak mau di-_slice_
    juga gak apa-apa sih, terserah aja lah. :D

Semua contoh di bagian ini untuk middleware yang __whitelist__ yak, untuk yang __blacklist__ silakan disesuaikan.

## Penggunaan

Nah setelah kita definisikan semua ini itunya di atas, cara menggunakannya bisa di beberapa tempat (pilih satu aja):

* Argumen di definisi route (`app/Http/routes.php`)

        Route::get('/', ['middleware' => 'allow:aku,kamu,saya',
            'uses' => 'HomeController@welcome');
        //atau
        Route::get('/', ['middleware' => 'allow:aku|kamu|saya',
            'uses' => 'HomeController@welcome');

* Di constructor untuk controller (`app/Http/routes.php`)

       /**
         * HomeController constructor.
         */
       public function __construct()
       {
           $this->middleware('allow:aku,kamu,saya');
           //atau
           $this->middleware('allow:aku|kamu|saya');
       }

    Opsi lengkapnya (misal untuk fungsi tertentu aja di controller) silakan konsultasikan di laman dokumentasinya[^controller].

## Penutup

Yak demikian tutorial singkat lainnya dari developer biasa di pojok ruang dev besar. Contoh yang ditunjukkan di sini
hanyalah 'butiran debu' dari 'segunung' pemanfaatan middleware. :p Masih banyak hal lain yang bisa dilakukan bahkan
untuk mengembangkan contoh di atas saja. Misal role-nya berupa relasi many-to-many, cara ngeceknya beda lagi. Atau misal
variabel `$roles` mesti diambil dari database atau session, gak dikirim sebagai argumen.

Middleware juga bisa dipakai untuk membuat _breadcrumb_, cek apakah _ajax_, cek _token header_ dll. Banyak beud.
Intinya, ayo berkreasi dan berbagi!

## Referensi

[^release]: [Release Notes Laravel 5.1](https://laravel.com/docs/5.1/releases#laravel-5.1)
[^command]: [Defining middleware](https://laravel.com/docs/5.1/middleware#defining-middleware)
[^register]: [Registering middleware](https://laravel.com/docs/5.1/middleware#registering-middleware)
[^strcmp]: [PHPDoc `strcmp`](http://php.net/manual/en/function.strcmp.php)
[^controller]: [Controller middleware](https://laravel.com/docs/5.1/controllers#controller-middleware)