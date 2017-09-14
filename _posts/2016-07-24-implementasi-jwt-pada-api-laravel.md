---
author: Wahyu Rismawan
layout: post
title: Implementasi JWT pada API berbasis Laravel Framework
category: 'Web Development'
---

* TOC
{:toc}

## Pendahuluan

Definisi non-formal dan sederhananya, JWT adalah token dengan format JSON Object, yang berisi informasi-informasi penting yang dikemas secara aman menggunakan algoritma tertentu.

JWT terdiri dari tiga buah bagian, yaitu `header`, `payload` dan `signature` yang dipisahkan oleh titik. Formatnya sebagai berikut

~~~
xxxheaderxxx.yyypayloadyyy.zzzsignaturezzz
~~~

**Header** memiliki attribute alg (algoritma yang digunakan untuk signature) dan `"typ"` (tipe token).

	{
	    "alg": "HS256",
	    "typ": "JWT"
	}

JSON tersebut di encode dengan `Base64Url`, dan menjadi bagian pertama dari JWT.

**Payload** terdiri dari attribute `iss` (issuer), `iat` (expiration time), `sub` (subject), dan lain-lain.

Contoh dari payload seperti berikut

	{
	  "sub": "1234567890",
	  "name": "Badr",
	  "admin": true,
	  "exp": 1300819380
	}

<!-- break -->

JSON tersebut di encode dengan `Base64Url`, dan menjadi bagian kedua dari JWT.

**Signature**

Signature adalah gabungan dari `header`, `payload`, `secret` key yang di sign dengan sebuah algoritma yang sudah ditetapkan. Misalkan kita menggunakan algoritma `HMAC SHA256`, maka signature akan dibuat dengan cara berikut:

~~~
HMACSHA256(
    base64UrlEncode(header) + "." +
    base64UrlEncode(payload),
    secret
)
~~~

Seperti yang sudah kita tahu, JWT terdiri dari `header`, `payload` dan `signature`, maka contoh JWT adalah sebagai berikut:

~~~
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.
eyJzdWIiOjIsImlzcyI6Imh0dHA6XC9cL2xvY2.
OOFAd4unowQw8LN-5MWumL8ASzZk63emDRip-TwZLCc
~~~

Secara umum cara kerja dan penggunaan JWT bisa dilihat di diagram berikut:

![image]({{ site.baseurl }}/public/images/web/jwt/jwt_flow.png){:width="100%"}

## Instalasi

Pada tutorial ini, instalasi package jwt-auth menggunakan composer.

Langkah pertama dengan mengubah file composer.json, dan tambahkan "tymon/jwt-auth" pada "require":

	"require": {
	    "tymon/jwt-auth": "0.5.*"

	}

Setelah itu jalankan `composer update` di terminal / command prompot

## Konfigurasi

Setelah selesai melakukan update composer untuk menginstall `jwt-auth` ke dalam project kita, langkah selanjutnya adalah melakukan beberapa konfigurasi berikut:

**Menambahkan service provider di file app.php pada array `provider`**

	Tymon\JWTAuth\Providers\JWTAuthServiceProvider:class

**Membuat alias untuk `JWTAuth facade` dan `JWTFactory facade` dengan menambahkan kode baris dibawah ini pada file `app.php`, array `'aliases'`**

	'JWTAuth' => Tymon\JWTAuth\Facades\JWTAuth::class,
	'JWTFactory' => Tymon\JWTAuth\Facades\JWTFactory::class

**Publish konfigurasi yang sudah dibuat dengan menggunakan command berikut**

	php artisan vendor:publish --provider="Tymon\JWTAuth\Providers\JWTAuthServiceProvider"

jika publish berhasil maka akan muncul message:

	Copied File [/vendor/tymon/jwt-auth/src/config/config.php] To [/config/jwt.php]
	Publishing complete for tag []!

**Ubah `JWT_SECRET` pada file `config/jwt.php`. defaultnya adalah string 'changeme'.**

Jalankan command berikut untuk men-generate jwt-auth secret key

	php artisan jwt:generate

**Mendaftarkan middleware** 

Beberapa middleware yang sudah disediakan package jwt-auth, perlu didaftarkan di Kernel.php agar bisa digunakan. Caranya dengan menambahkan beberapa baris kode dibawah ini pada array `$middlewareGroups`. Misalkan di group dengan nama `'jwt'`

	'jwt' => [
	    'jwt.auth' => \Tymon\JWTAuth\Middleware\GetUserFromToken::class,
	    'jwt.refresh' => \Tymon\JWTAuth\Middleware\RefreshToken::class,
	]

Penjelasan lebih lanjut dari kedua middlware tersebut ada di bagian setelah ini.

## Contoh Penggunaan

Misal, ada kebutuhan dari mobile developer seperti ini:
1. Login user, dengan melakukan request POST ke url http://contoh-jwt.com/api/login. Parameter yang dikirim adalah email dan password.
2. Mengetahui data user yang sedang login, dengan melakukan request GET ke http://contoh-jwt.com/api/user tanpa ada parameter yang dikirim.

Langkah-langkah yang perlu dilakukan sebagai berikut:

**Membuat controller baru, misal `ApiController.php` dengan command artisan**

	php artisan make:controller ApiController

**Membuat route**

	Route::group(['prefix' => 'api'] , function() {

	    Route::post('/login', 'ApiController@postLogin');

	    Route::group(['middleware' => ['jwt']], function() {
	        Route::get('/user', 'ApiController@getUser');
	    });
	});


**Menonaktifkan verifikasi csrf token untuk seluruh endpoint api, dengan menambahkan string `'api/*'` pada attribute `$except` di file `VerifyCsrfToken.php`**

	protected $except = [
	    'api/*'
	];

**Buat fungsi `postLogin()`, yang akan menerima email dan password dari user dan mengembalikan token jika berhasil melakukan authentication.**

	public function postLogin(Request $request) {
	    $email = $request->get('email');
	    $password = $request->get('password');

	    if (Auth::attempt(['email' => $email, 'password' => $password])) {

	        $user = User::where('email', $email)->first();
	        $token = JWTAuth::fromUser($user);

	        $response = [
	            "success" => true,
	            "key" => "Bearer {$token}"
	        ];

	    } else {
	        $response = [
	            "success" => false,
	            "message" => "user not found"
	        ];
	    }

	    return response()
	        ->json($response);
	}

![image]({{ site.baseurl }}/public/images/web/jwt/jwt_postman_login.png){:width="100%"}

Token bisa langsung dibuat dengan fungsi `JWTAuth::fromUser($user)`. Secara default, format token yang digunakan pada package ini adalah `Bearer TOKEN`. 

Token ini yang akan digunakan oleh user sebagai "kunci", agar mendapat akses untuk melakukan request selanjutnya. Misalkan request selanjutnya adalah mendapatkan data user dengan melakukan request ke /api/user

**Buat fungsi getUser, tanpa parameter**


	public function getUser() {
	    $response = [
	        "success" => true,
	        "data" => Auth::user()
	    ];

	    return response()
	        ->json($response);
	}


**WAIT... WHY? HOW?**

Mengapa kita bisa mendapatkan data authentication user secara langsung, tanpa parameter apapun di fungsi getUser() ?

Jika kita memperhatikan kembali `route.php`, `getUser()` akan diakses menggunakan bantuan middleware jwt, yang terdiri dari `jwt.auth` dan `jwt.refresh`.

Inilah peran middleware `jwt.auth (GetUserFromToken.php)`. Middleware tersebut yang bertugas meng-ekstrak token dan melakukan authentication secara langsung. Sehingga, setelah masuk controller, data user bisa langsung diakses dengan `Auth::user()`.

Token tersebut dikirim melalui request header, dengan default key `"Authorization"`. 

![image]({{ site.baseurl }}/public/images/web/jwt/jwt_postman_user.png){:width="100%"}

Peran middleware `jwt.refresh (RefreshToken.php)` adalah memerbarui token yang sebelumnya kita pakai. Secara default, untuk alasan keamanan, setiap request akan menggunakan token yang berbeda. Token akan diperbarui setiap melakukan request.

Token yang baru bisa didapat di http header pada response-nya. Token ini yang valid digunakan untuk melakukan request selanjutnya.

![image]({{ site.baseurl }}/public/images/web/jwt/jwt_postman_header.png){:width="100%"}

Secara default, token hanya memiliki umur selama 60 menit. Untuk melakukan konfigurasi ini, kita bisa mengubahnya pada file `config/jwt.php`

Beberapa hal yang perlu diingat: 
1. Pada saat melakukan request, umumnya token dikirim melalu http header.
2. Default key untuk pada http header untuk membawa token adalah "Authorization".
3. Default format token adalah `Bearer TOKEN`
4. Secara default, token akan selalu diperbarui setelah melakukan http request, dan bisa diakses pada response header.
5. Secara default, umur token selama 60 menit.