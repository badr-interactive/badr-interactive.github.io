---
layout: post
title: Hal yang Wajib Diketahui Terkait Penggunaan Eloquent di Laravel
category: 'Web Development'
---

* TOC
{:toc}

## Introduction

Eloquent merupakan Object Relational Mapping (ORM) engine yang digunakan oleh Laravel. Sedangkan ORM itu sendiri merupakan sebuah teknik dalam pemrograman object-oriented yang ditujukan untuk mengkonversi type sistem dari database kedalam type sistem yang dimiliki oleh bahasa pemrograman berorientasi object tersebut (misal: Java, .Net, PHP) sehingga seolah-olah kita memiliki virtual database di dalam aplikasi kita[^orm]. Berikut ini adalah merupakan kasus-kasus yang perlu kamu ketahui terkait penggunaan Eloquent di laravel.

## Selalu Manfaatkan Dependency Injection Laravel

Kebanyakan para pengguna laravel selalu menggunakan fitur `Facade` yang disediakan laravel. `Facade` adalah sebuah teknik dalam pemrograman berorientasi objek yang digunakan untuk membungkus detail implementasi / API pada sebuah library untuk menyederhanakan penggunaan dari library tersebut. Contoh dari penggunaan `Facade` pada objek Eloquent adalah sebagai berikut:

~~~php
public function detail(Request $request, $id)
{
    $product = Product::find($id); //Facade
    return response()->view('product.detail', ['product' => $product]);
}
~~~

<!-- break -->

Pada laravel, setiap `Facade` menyediakan static method yang dapat langsung dipanggil tanpa harus membuat instance terlebih dahulu seperti misalnya `Product::find()`, `Auth::user()`, `Product::where()->first()`, dan masih banyak lagi. Hal ini membuat para pengguna laravel terlena dengan fitur `Facade` yang dimiliki Laravel yang (menurut saya) dapat menyebabkan dilupakannya salah satu perinsip dalam OOP yaitu `Dependency Inversion`. Sehingga untuk banyak alasan yang tidak akan dibahas pada artikel ini, kita akan membiasakan diri menggunakan fitur `Dependency Injection`[^di] dengan melakukan typehinting[^typehint] pada saat ingin menggunakan objek Eloquent di Controller. Berikut adalah contoh melakukan typehinting untuk memanggil objeck Eloquent di Controller:

~~~php
//typehinting objek Product pada constructor
function __construct(Product $product)
{
    $this->product = $product;
}

public function detail(Request $request, $id)
{
    $product = $this->product->find($id);
    return response()->view('product.detail', ['product' => $product]);
}
~~~

Dengan melakukan typehinting seperti diatas, secara otomatis laravel akan membuat instance untuk objek Product di dalam constructor. Mekanisme ini dapat terjadi dikarenakan Laravel sudah memiliki mekanisme `Dependency Injection` melalui typehinting di constructor.

## Method `find()` Menggunakan Parameter Primary Key

Method `find()` merupakan method pencarian yang agak berbeda dengan method lainnya. Biasanya method-method pencarian pada Eloquent menggunakan parameter `['attribute_name' => 'value']`, akan tetapi khusus untuk method `find()` hanya menerima satu buah nilai yaitu primary key pada tabel yang bersangkutan (defaultya adalah attribute `id`).

~~~php
public function details(Request $request, $id)
{
    $product = $this->product->find($id);
    return response()->view('product.details', ['product' => $product]);
}
~~~

Untuk tabel yang tidak menggunakan attribute bernama `id`, Eloquent menyediakan mekanisme untuk mendefinisikan nama attribute primary key pada tabel dengan menambahkan `protected $primaryKey = '$attributeName'`.

~~~php
public class Product extends Model
{
    /*
    * Mendefinisikan secara eksplisit bahwa table products
    * memiliki primary key dengan nama 'product_id'
    */
    protected $primaryKey = 'product_id'
}
~~~

## Method `firstOrNew()` Cocok Untuk Mekanisme Add/Edit Data

Hampir dalam setiap pengembangan aplikasi web kita akan menemukan kasus dimana user harus bisa menambah data kedalam database ataupun mengupdate data dari database seperti contoh dibawah ini:

~~~php
public function add(Request $request)
{
    $this->product->name = $request->name;
    $this->product->description = $request->description;
    $this->product->price = $request->price;
    $this->product->save();

    return redirect()->back();
}

public function edit(Request $request, $id)
{
    $product = $this->product->find($id);
    $product->name = $request->name;
    $product->description = $request->description;
    $product->price = $request->price;
    $product->save();

    return redirect()->back();
}
~~~

Jika dilihat secara seksama dua buah fungsi diatas memiliki kesamaan yaitu sama-sama memberikan nilai ke setiap attribute `product` dan menggunakan method `save()` untuk menyimpan perubahananya.

Oleh karena itu, kedua method tersebut seharusnya bisa di refactor untuk mengurangi redudancy pada kode yang dibuat. Salah satu caranya adalah dengan menggunakan method `firstOrNew()`. Method `firstOrNew()` merupakan method pada Eloquent yang berfungsi untuk mencari data di dalam database sesuai dengan kriteria yang diberikan dan akan mengambil baris pertama seperti method `first()` apabila data telah ditemukan. Akan tetapi, jika data tidak ditemukan didalam database maka method ini akan membuat instance dari model yang bersangkutan seperti misalnya `Product`.

Berikut ini adalah versi yang sudah direfactor dengan memanfaatkan fitur `firstOrNew()`:

~~~php
public function save(Request $request)
{
    $productId = $request->id;
    $product = $this->product->firstOrNew(['product_id' => $productId]);
    $product->name = $request->name;
    $product->description = $request->description;
    $product->price = $request->price;
    $product->save();

    return redirect()->back();
}
~~~

## Always check empty object on eloquent

Selalu lakukan checking pada object yang diretrieve oleh fungsi Eloquent, e.g : method `find()`. Perlu diketahui bahwa method `find()` ini jika tidak menemukan object yang diinginkan maka akan return null. Sehingga, kita perlu untuk mengecek hasil returnnya sebelum memanggil attribute atau function dari object tersebut.

Contoh salah :

~~~php

echo User::find($user_id)->name // // will cause error if user is not found
echo Auth::user()->id; // will cause error if user is not login

$user = User::find($user_id);
$user->getFirstName(); // will cause error if user is not found

~~~

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

## Check empty on get() eloquent function

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

## firstOrNew() vs createOrUpdate()

Nah, Kalo di poin sebelumnya kita membahas `firstOrNew()` nya laravel, di sini kita akan bandingkan dengan kakak seperguruannya, 
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

[^orm]: [https://en.wikipedia.org/wiki/Object-relational_mapping](https://en.wikipedia.org/wiki/Object-relational_mapping)

[^di]: Dependency injection adalah sebuah design pattern pada pemrograman berorientasi objek dalam menerapkan teknik `inversion of control` untuk meresolve setiap dependency pada sebuah class. [wikipedia](https://en.wikipedia.org/wiki/Dependency_injection).

[^typehint]: Typehint adalah sebuah teknik dalam pemrograman yang ditujukan untuk mengidenfikasi tipe data dari sebuah variable. [sitepoint](http://www.sitepoint.com/type-hinting-in-php/)
