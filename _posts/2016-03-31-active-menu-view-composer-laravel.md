---
layout: post
title: Membuat Active Menu dengan View Composer di Laravel
category: 'Web Development'
author: yahyaman
---

* TOC
{:toc}

Dalam sebuah aplikasi web, biasanya yang bertipe _web admin_, umum kita jumpai sidebar berisi menu-menu.
Daftar menu tersebut biasanya memiliki state `active` atau biasa alias tidak aktif, menunjukkan si user
sedang berada di halaman apa. Berbagai template _web admin_ biasanya sudah mempunyai styling sendiri seperti
contoh di bawah ini:

![template-1]({{ site.baseurl }}/public/images/web/active-menu-01.png)
![template-2]({{ site.baseurl }}/public/images/web/active-menu-02.png)

Di atas adalah template favorit anak Badr, yaitu __SB-Admin__ dan __AdminLTE__, sebagai contoh
styling _active menu_. Biasanya, style tersebut didapat dengan menambahkan CSS class `.active` pada elemen
_menu item_ atau butir navigasi. Secara _back-end_, kita harus memastikan ketika view tersebut mau dilukis alias render,
mana _menu item_ yang harus diberi class `.active`.

Di Laravel, biasanya kita mengirim data atau variabel yang dibutuhkan View dari fungsi yang ada di
Controller. Masalahnya, data menu yang aktif itu kita butuhkan untuk setiap halaman yang diminta.
Solusi naifnya, ya kita mesti ngirim paramater seperti `active_menu => 'dashboard'` pada setiap fungsi yang ada.
Nah, ada beberapa cara lain yang lebih cerdas untuk ini, salah satunya dengan __View Composer__.

<!-- break -->

## Konsep

__View Composer__ ialah sebuah _callback_ pada Laravel yang dipanggil ketika sebuah view akan dilukis
(render)[^doc52]. Nah, fitur ini dapat kita gunakan untuk berbagi data yang sama pada beberapa view.
So, kita akan membuat composer untuk view yang memuat sidebar menu tersebut dan mengirim data menu
mana yang sekarang lagi diakses.

Masalah berikutnya, darimana kita tahu menu yang lagi diakses? paling gampang sih dari pengecekan
_route_ alias rute yang lagi diakses. Misal `site.com/users` biasanya jadi prefix untuk semua halaman yang
berkaitan dengan user. Tapi ada cara lain yang lebih asik nih, mau tau gak? yuk lanjutin bacanya :D

## Langkah-langkah

1. Buat class `ActiveMenuComposer.php` seperti contoh di bawah. Oya, berkas class ini bisa ditaruh di direktori
mana aja, yang penting namespace-nya bener. Dalam contoh ini berkas ditaruh di `app/Http/Composer/`.

        <?php
        namespace App\Http\Composer;

        use View;
        use Illuminate\Http\Request;

        class ActiveMenuComposer
        {
            protected $request;

            /**
             * ActiveMenuComposer constructor.
             * @param Request $request
             */
            public function __construct(Request $request)
            {
                $this->request = $request;
            }

            public function compose(View $view)
            {

            }
        }

2. Selanjutnya, kita kaitkan informasi menu yang aktif dengan rute aplikasi di `app/Http/routes.php`
sebagai berikut:

        Route::group(['prefix' => 'users', 'active_menu' => 'user'], function() {
                //user related area
                Route::get('{id}', 'UserController@detail');
                Route::get('{id}/edit', 'UserController@editForm');
                Route::post('{id}/edit', 'UserController@editSave');
            }
        );

    Contoh pembuatan rute di atas memanfaatkan custom _route parameter_ yang akan dikirim sebagai
    atribut dalam objek `Illuminate\Routing\Route`. Nah, objek `Route` tersebut bisa didapat dari
    instance `Illuminate\Http\Request` yang sudah kita _inject_ alias masukkan di constructor view
    composer di langkah sebelumnya.[^request]

3. Tambahkan parameter active menu di view sesuai dengan _route parameter_ yang tadi didefinisikan.

        public function compose(View $view)
        {
            $routeAction = $this->request->route()->getAction();

            if (array_key_exists('active_menu', $routeAction)) {
                $view->with('active_menu', $routeAction['active_menu']);
            } else {
                $view->with('active_menu', 'default');
            }
        }

    Fungsi `getAction()` pada objek `Route` akan mengembalikan array berisi _route parameter_ yang
    kita definisikan tadi[^route]. Isinya lebih banyak dari yang kita tulis sih, tapi yang kita cek cuma yang
    kita bikin aja yaitu `active_menu`.

4. Jangan lupa daftarkan view composer kita untuk view yang terkait di sebuah _Service Provider_.
 Kita bisa buat provider khusus untuk mendaftarkan view composer, atau gunakan saja
`app\Providers\AppServiceProvider.php`. Yang penting isi fungsi `boot()` seperti berikut:

        public function boot()
        {
            view()->composer('layouts.admin.sidebar', 'App\Http\Composer\ActiveMenuComposer');
        }

    Kode di atas mendaftarkan view composer `ActiveMenuComposer` yang kita buat untuk view
    `layouts.admin.sidebar` yang memuat sidebar menu. View-nya silakan sesuaikan aja.

5. Ambil data `active_menu` yang sudah ditambahkan oleh view composer. Di view `layouts.admin.sidebar`,
kita dapat mengaksesnya seperti biasa, contohnya seperti ini:
{% raw %}
        <li class="{{ $active_menu == 'user' ? 'active' : '' }}">
            <a href="{{ url('admin/users') }}">
                <i class="fa fa-users"></i> <span>Anggota</span>
            </a>
        </li>
{% endraw %}

Selesai!

## Suplemen

* Satu view composer bisa digunakan untuk banyak view. Parameter pertama ketika mendaftarkannya diubah jadi
array:

        view()->composer(['layouts.admin.sidebar', 'layouts.admin.header'],
            'App\Http\Composer\ActiveMenuComposer');

* Atau bisa juga pakai wildcard (*):

        view()->composer(['layouts.master.sidebar', 'layouts.admin.*'],
            'App\Http\Composer\ActiveMenuComposer');

* Contoh di atas sudah dicoba di Laravel 5.2 dan berjalan baik. Menurut laman dokumentasinya[^doc50],
hal yang sama dapat dilakukan di versi 5.1 juga. Untuk versi 5.0, cara mendaftarkan view composer-nya
sedikit berbeda:

        //mendaftar satu composer
        View::composer('layouts.admin.sidebar', 'App\Http\Composer\ActiveMenuComposer');
        //mendaftar banyak composer
        View::composers([
            'App\Http\ViewComposers\AdminComposer' => ['admin.index', 'admin.profile'],
            'App\Http\ViewComposers\UserComposer' => 'user',
            'App\Http\ViewComposers\ProductComposer' => 'product'
        ]);

## Penutup

Seperti class Controller di dalam Laravel, kita juga bisa mengguakan Eloquent model dan service lain
di dalam view composer. Bisa di-inject melalui constructor ataupun dengan facade. Hal ini berguna jika
kita ingin berbagi sebuah data untuk banyak view sekaligus. Misal data daftar kota dan provinsi di database
diperlukan di setiap halaman pencarian atau form pendaftaran. Daripada mesti ngambil terus kirim data
yang sama di setiap fungsi yang membutuhkan, mending bikin sekali terus daftarin. Beres.

Yak segini dulu dah untuk kali ini. Seperti dibilang di awal, ada banyak cara lain untuk implementasi
active menu, googling aja kalau penasaran. Ini juga dapetnya berawal dari gugling, hehe.
Nah kalau udah punya metode lain yang mungkin lebih keren, jangan lupa bagi-bagi di sini! :D

## Referensi

[^doc52]: [Dokumentasi terbaru](https://laravel.com/docs/5.2/views#view-composers)
[^request]: [Dokumentasi API Request](https://laravel.com/api/5.2/Illuminate/Http/Request.html#method_route)
[^route]: [Dokumentasi API Route](https://laravel.com/api/5.2/Illuminate/Routing/Route.html#method_getAction)
[^doc50]: [Dokumentasi 5.0](https://laravel.com/docs/5.0/views#view-composers)
