---
layout: post
title: Membuat Custom Blade Directive
category: 'Web Development'
---

Sebagai pengguna framework Laravel, tentunya kita sangat mengenal Blade Template Engine. Salah satu keuntungan yang kita dapatkan dalam menggunakan Blade dibandingkan hanya penggunakan plain PHP adalah kita dapat menggunakan `Blade Directive` seperti misalnya `@section`, `@if`, `@foreach`, `@inject` dan banyak directive lainnya untuk menggantikan kode PHP yang akan kita masukan kedalam halaman `view` yang kita buat sehingga membuat kode program kita menjadi lebih rapih dan nyaman dilihat.

Dalam artikel ini saya akan membahas tentang bagaimana membuat `Blade Directive` baru sesuai dengan kebutuhan kita seperti misalnya `@checkuser`, `@isadmin` atau `@dateformat`. Untuk dapat mendefinisikan `Blade Directive` baru kita dapat menggunakan fitur `Blade::directive()` yang telah disediakan oleh Laravel. Sebagai contoh, kita ingin membuat sebauh directive bernama `@datetime` yang secara otomatis akan mengkonversikan forma tanggal database `YYYY-mm-dd` menjadi format mudah dibaca oleh user seperti `dd/mm/YYYY`. Untuk dapat melakukan hal tersebut kita perlu menambahkan beberapa baris kode pada class `App\Providers\AppServiceProvider` seperti berikut ini:

<!-- break -->

~~~php
namespace App\Providers;

use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        Blade::directive('datetime', function($expression) {
            return "<?php echo with{$expression}->format('m/d/Y H:i'); ?>";
        });
    }

    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        //
    }
}
~~~

Jika melihat contoh kode diatas, kita mengetahui bahwa `Blade Directive` hanyalah sebuah pengganti dari `PHP experssion` yang kita definisikan. Apabila kita pernah menggunakan directive `@if` dan `@endif` maka directive tersebut hanyalah pengganti dari `<?php if($experssion) {?>` dan `<?php } ?>` atau `<?php if($expession) : ?>` dan `<?php endif; ?>` (kalau menggunakan php control structure alternative syntax). Oleh karena itu, kita juga dapat membuat directive seperti `@if` dan `@endif` seperti misalnya `@admin` dan `@endadmin` untuk melakukan pengecekan apakah user tersebut memiliki role admin atau tidak. Untuk dapat membuat directive seperti itu kita cukup menambahkan kode didalam method `boot()` seperti dibawah ini:

~~~php
/**
 * Bootstrap any application services.
 *
 * @return void
 */
public function boot()
{
    Blade::directive('admin', function($expression) {
        return "<?php if (Auth::user()->role === 'admin') { ?>";
    });

    Blade::directive('endadmin', function($expression) {
        return "<?php } ?>";
    });
}
~~~

Yup, begitulah. Selamat mencoba :)
