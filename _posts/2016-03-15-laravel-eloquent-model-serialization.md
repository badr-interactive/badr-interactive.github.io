---
layout: post
title: Laravel Eloquent Model Serialization using Accessor and Mutator
category: 'Web Development'
---

* TOC
{:toc}

Untuk kamu-kamu para laravelian (copy dari yahya :p), pastinya sudah tidak asing lagi dengan yang namanya accessor dan mutator yang ada di [eloquent modelnya laravel](https://laravel.com/docs/5.1/eloquent-mutators). Cara pakenya bisa diliat di bawah ini

Contoh accessor :

~~~php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    /**
     * Get the user's first name.
     *
     *
     */
    public function getFirstNameAttribute($value)
    {
        return ucfirst($value);
    }
}
?>
~~~

<!-- break -->

Contoh mutator:

~~~php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    /**
     * Set the user's first name.
     *
     * @param  string  $value
     * @return string
     */
    public function setFirstNameAttribute($value)
    {
        $this->attributes['first_name'] = strtolower($value);
    }
}
?>
~~~

Khusus untuk accessor, kita bahkan bisa membuat accessor untuk field yang memang belum ada di datatabase:

~~~php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    /**
     * Get the user's first name.
     *
     *
     */
    public function getFullNameAttribute($value)
    {
        return $this->first_name . " " . $this->last_name;
    }
}
?>
~~~

Nah sekarang kita akan sedikit berpikir out of the box. Bagaimana jika yang menjadi parameter dari accessor dan mutator tersebut adalah sebuah Eloquent Model? Bukan string ataupun integer karena sudah terlalu mainstream.

( Disclaimer : Ini bukan best practice, hanya untuk mereka yang mau sedikit rock and roll di laravel )

Hal ini akan berguna jika ada model yang sifatnya volatile alias datanya suka berubah ubah dan kamu mau nyimpen datanya di satu tabel, padahal kamu <strike>males</strike> gak ada waktu untuk bikin tabel relasinya.

Kalo kamu mau nyobain, bisa lihat contoh kode di bawah

Mutator :

~~~php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Order extends Model
{
    /**
     * Get the user's first name.
     *
     *
     */
    public function setPromoAttribute($promo)
    {
        if($promo)
            $this->attributes['promo'] = $promo->toJson();
    }
}
?>
~~~

Kode di atas akan menserialize model eloquent Promo menjadi bentuk json dan menyimpannya di database. Sampai sini masih cukup mudah.

~~~php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Order extends Model
{

    public function getPromoAttribute($value)
    {
        if(!$value)
            return null;

        $promo = new Promo;
        return $promo->fill(json_decode($value,true));
    }
}
?>
~~~

Nah untuk bagian accessornya yang agak tricky. Jadi pengennya pas kita panggil attribute promo, kita pengen cast dia jadi Eloquent Model Promo. Alhasil kita gunakanlah yang namanya laravel hydrate alias method fillnya laravel. Cara pakenya bisa dilihat di [sini](https://laracasts.com/discuss/channels/tips/hydrators-in-laravel-5) atau kalau mau lihat dokumentasinya bisa dilihat di [sini](https://laravel.com/api/5.0/Illuminate/Database/Eloquent/Model.html#method_fill). Jadi singkatnya kita bikin instance baru dari Promo terus kita fill deh dengan data yang sudah diserialize di db sebelumnya. Gampang kan?

( Notes : Metode ini belum pernah dicoba untuk menyimpan eloquent yang ada relasinya. Kalau ada yang nemu caranya boleh lah dishare )

Selamat Mencoba !!
