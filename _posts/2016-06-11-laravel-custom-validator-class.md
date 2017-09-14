---
layout: post
title: Laravel Custom Validator Class
category: 'Web Development'
---

* TOC
{:toc}

Pernah menggunakan validasi pada Laravel dan bingung ketika fungsi validasi yang anda inginkan tidak ada di [Available Validation Rules](https://laravel.com/docs/5.2/validation#available-validation-rules)? Jangan khawatir, anda dapat implementasikan fungsi validasi anda tersebut sendiri!

Di dokumentasi Laravel, bagian [Custom Validation Rules](https://laravel.com/docs/5.2/validation#custom-validation-rules), telah dibahas cara untuk menambah fungsi validasi sendiri dengan menggunakan fungsi `Validator::extend()` di fungsi `boot()` pada AppServiceProvider. Namun apa yang terjadi jika anda harus mengimplementasikan banyak custom validation rules? Tentunya deklarasi fungsi tersebut akan banyak dan tidak teratur. Selain itu, posisi deklarasi fungsi yang banyak tersebut saya nilai tidak tepat mengingat lokasinya terletak di AppServiceProvider (service provider untuk sebuah aplikasi), sedangkan ini adalah custom validation rules. Lalu bagaimana caranya?

<!-- break -->

Untuk memisahkan custom validation rules menjadi class terpisah, deklarasikan sebuah class seperti berikut:

~~~php
class OrderValidator extends Validator
{

    private $orderCustomMessages = [
        "sufficient_balance" => "Message for sufficient_balance is here"
    ];

    public function __construct(TranslatorInterface $translator,
            array $data, array $rules, array $messages,
            array $customAttributes)
    {
        parent::__construct($translator, $data,
            $rules, $messages, $customAttributes);

        $this->setCustomMessages($this->orderCustomMessages);
    }

    /**
     * Validate if we have sufficient balance.
     *
     * @param $attribute
     * @param $value
     * @return bool
     */
    public function validateSufficientBalance($attribute, $value) {

        return true
    }
}
~~~

Class tersebut harus melakukan extend terhadap class Validator, kemudian deklarasikan fungsi constructor seperti di atas (mengikuti parent class). Untuk nama fungsi, khusus harus `validateNamaFungsiCustom()`. Cara menggunakan fungsi tersebut di validasi adalah dengan memanggil `nama_fungsi_custom`. Pada contoh di atas, nama fungsinya adalah `validateSufficientBalance()`, maka penggunaannya adalah `sufficient_balance`.

Untuk custom message, anda dapat mengisi sesuai keinginan anda, jika eksekusi terhadap fungsi validasi tersebut salah. Isi key dari array custom message tersebut adalah nama penggunaan fungsi validasi. Pada contoh di atas, maka key-nya adalah `sufficient_balance`.

Terakhir, daftarkan resolver validator ke fungsi `boot()` pada AppServiceProvider.

~~~php

class AppServiceProvider extends ServiceProvider
{
    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        Validator::resolver(function($translator, $data, $rules, $messages, $customAttributes) {
            return new OrderValidator($translator, $data, $rules, $messages, $customAttributes);
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

Semoga bermanfaat.
