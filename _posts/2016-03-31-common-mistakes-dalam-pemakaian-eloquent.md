---
layout: post
title: Common Mistakes dalam Penggunaan fungsi Eloquent di Laravel (Part I)
category: 'Web Development'
---

* TOC
{:toc}

## Introduction

Kalau di forum sebelah Kang Aher sudah membahas tentang hal yang harus diketahui dari eloquent, nah di sini saya mau sedikit share tentang common mistakes dalam pemakaian eloquent based on pengalaman saya sendiri. Let's check it out

## 1) Selalu memerika object kosong setelah melakukan `find()`

Kalian pasti sudah tidak asing lagi dengan method `find()` nya eloquent. Perlu diketahui bahwa method find ini jika tidak menemukan object yang diinginkan maka akan return null. Sehingga, kita perlu untuk mengecek hasil returnnya sebelum memanggil attribute atau function dari object tersebut.

Contoh salah :

~~~php

echo User::find($user_id)->name // // will cause error if user is not found
echo Auth::user()->id; // will cause error if user is not login

$user = User::find($user_id);
$user->getFirstName(); // will cause error if user is not found

~~~

<!-- break -->

Contoh benar:

~~~php

$user = User::find($user_id);

if(!is_null($user)){ // always check first
    return $user->id;
}

// or

if(!$user){ // always check first
    return $user->id;
}

if(Auth::check()){ // always check first
    return Auth::user()->id;
}

~~~

## 2) Check empty on get() eloquent function

Nah, tadi kalian sudah mengetahui cara check empty object pada method `find()`. Bagaimana cara cek empty object kalau method yang dipakai adalah method `get()`.

Kebanyakan dari kita (termasuk saya sendiri :P) pernah melakukan kesalahan seperti ini


Contoh salah :

~~~php

$users = User::active()->get();

if(!empty($users)){
    foreach($users as $user){
        // do something
    }
}

// or

$users = User::active()->get();

if(!$users){
    foreach($users as $user){
        // do something
    }
}

~~~

Padahal method get dari eloquent laravel itu sebenarnya melakukan return collection of object. Jadi kita tidak bisa langsung pakai ! untuk mengecek apakah empty atau enggak, tapi gunakanlah fungsi `count()` dari collectionnya laravel

Contoh benar:

~~~php

$users = User::active()->get();

if( !$users->count() ){
    foreach($users as $user){
        // do something
    }
}

~~~

## 3) firstOrNew() vs createOrUpdate()

Nah, Kalo di thread sebelah Yahya sudah membahas fungsi `firstOrNew()` nya laravel, di sini kita akan bandingkan dengan kakak seperguruannya,
`createOrUpdate()`.

Perlu diketahui walaupun mirip, tetapi penggunaan keduanya berbeda. Perbedaan yang paling mendasar adalah kalau pakai `firstOrNew()` harus diakhiri dengan method `save()` , jika tidak maka tidak akan tersimpan di database. Berbeda dengan fungsi `createOrUpdate()` yang akan langsung menyimpan di database. Perlu diketahui juga bahwa `firstOrNew()` memerlukan 1 parameter -> search di db dengan where parameter tsb. Jika tidak ada maka create new instance dengan parameter tersebut. Sedangkan `createOrUpdate()` memerlukan 2 parameter -> Search di db dengan where parameter pertama. Jika ditemukan maka update value di db dengan menggunakan parameter kedua. Jika tidak ditemukan maka create di db dengan parameter kedua.

Untuk lebih jelasnya dapat dilihat di bawah.

Contoh pemakaian `firstOrNew()`

~~~php
public function save(Request $request)
{
    $productId = $request->id;
    $product = $this->product->firstOrNew(['product_id' => $productId]);
    $product->name = $request->name;
    $product->description = $request->description;
    $product->price = $request->price;
    $product->save(); // need to save here

    return $product;
}
~~~

Contoh pemakaian `createOrUpdate()`

~~~php
public function save(Request $request)
{
    $param1 = ['product_id' => $request->id]; // param to search on db
    $param2 = ['name' => $request->name, 'description' => $request->description, 'price' => $request->price]; // param to update/create
    $product = $this->product->createOrUpdate($param1, $param2); // need 2 params here

    return $product; // need not to save
}
~~~
