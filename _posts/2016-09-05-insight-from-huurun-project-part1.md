---
author: Muhammad Alif
layout: post
title: "Insight From Huurun Project : Playing with mindblown JavaScript Code"
category: 'Developer Insight'
---

* TOC
{:toc}


Pada tulisan ini saya akan berbagi pengalaman saya melanjutkan kodingan javascript yang dibuat oleh seorang freelancer dari tempat yang jauh di sebrang lautan sana untuk sebuah project bernama Huurun. Jadi di Huurun itu kan ada fitur bikin parfum yang direpresentasikan dalam bentuk radar chart. Buat yang gak tahu apa itu radar chart mungkin bisa lihat di google atau buat kalian yang suka maen PES, radar chart itu kurang lebih sama seperti graph skill pemain yang biasanya berbentuk segi-6.

Ok mungkin beberapa dari kalian penasaran seimba apa kodingan yang dibikin oleh si freelancer ini. Seperti biasa yang namanya Asians emang agak mindblown gaya ngodingnya. Berikut saya lampirkan sedikit potongan kodingannya

~~~
 	function _t(t) {
        return 1 === t.length ? function(e, n) {
            t(null == e ? n : null)
        } : t
    }

    function At(t) {
        var e = t.responseType;
        return e && "text" !== e ? t.response : t.responseText
    }

    function Dt(t, e, n) {
        var r = arguments.length;
        2 > r && (e = 0), 3 > r && (n = Date.now());
        var i = n + e,
            o = {
                c: t,
                t: i,
                n: null
            };
        return ss ? ss.n = o : as = o, ss = o, us || (ls = clearTimeout(ls), us = 1, cs($t)), o
    }
~~~

Gimana gan? Keren kan kodingannya. Bahkan developer sekelas Mark Zuckerberg juga pasti bakal kebingungan baca kodingannya. 
Kebanyakan nama fungsi dan variabelnya dibikin pake satu atau dua huruf dan yang paling pa rah adalah tidak ada dokumentasinya! Alhasil developer yang ngelanjutinpun akhirnya jadi berasa ummii yang baru belajar baca tulis kodingan. Jadi insight pertama yang saya dapat adalah **#1 Creating a code documentation becomes mandatory if your code is unreadable**. Fungsi dokumentasi di sini adalah harusnya jadi seperti terjemahan di Alquran. Bayangkan jika Anda adalah orang yang tidak bisa baca huruf Arab, maka yang Anda pertama kali cari untuk memahaminya tentu saja dengan membaca terjemahannya. Begitu pula dengan kodingan. Kalo ente gak bisa bikin kode yang readable, maka buatlah terjemahannya (baca 'dokumentasinya') 

Nah tantangan berikutnya adalah ternyata kodingan yang dia kasih itu ternyata masih kutuan alias ngebug! Jadi ya mau gak mau demi bertahan hidup agar bisa makan besok akhirnya harus dicari deh kutunya. Sempat kepikiran untuk bikin ulang kodingannya tapi mengingat kode js ini adalah core utama dari huurun, kayaknya akan sangat sulit untuk bikin ulang kodenya. Selain itu kode lamanya nanti jadi mubazir kan, dan mubazir itu temannya setan. So Insight kedua adalh **#2 Reuse, reuse and reuse any code which can be reused!**. Kenapa harus reuse? Karena dengan reuse kodingan yang udah ada berarti sama saja dengan menghargai hasil kerja keras developer sebelumnya, selain itu juga dapat menghemat waktu yang telah dihabiskan oleh developer sebelumnya. Jadi, jika masih bisa dilanjutkan, kenapa tidak?

Jadi apa yang saya lakukan untuk memperbaiki bugnya? Hal pertama yang harus saya lakukan adalah mencari di mana letak bugnya. Alhasil karena saya hampir pasrah untuk mempelajari kodingannya, lebih baik saya debug dengan cara yang lain. Akhirnya saya bikin console.log (var_dump) di semua fungsi yang kira-kira merupakan fungsi corenya. Perlahan tapi pasti akhirnya setelah melakukan hal tersebut akhirnya saya jadi bisa membaca jalan pikiran kodenya melalui console di chrolme. So Insight ke 3 adalah **#3 If you are desperate in finding a bug, try to var dump in some fishy part of the code!** Kenapa harus var dump? Karena dengan cara ini akan sangat membantu kita memahami bagaimana kode tersebut bekerja.

Nah selain dari beberapa insight di atas, ada beberapa pengetahuan juga yang saya dapat dari membaca prasasti (baca 'kodingan') yang dibikin ama orang China tersebut. Berikut adalah beberapa ilmu yang bisa kita coba terapkan di kodingan javascript kita:

**#4 Comma operator, create one line code expression in javascript**

Jadi di javascript itu ternyata ada operator yang pakai tanda koma. Tujuannya biar kode kita terlihat keren aja, yang harusnya dibikin 5 baris jadi cuma satu baris. Tapi ya gitu deh jadinya susah bacanya udah kayak uler kodingannya panjang banget di satu baris. Untuk ilustrasinya mungkin bisa dilihat pada kode berikut.

~~~

function foo(){
	var a = 5, b = 10, c = 5;

	return b = b * 10, a = a + 5, c = a * b, c; 
}

~~~

Jadi fungsi koma di sini adalah sebagai separator agar operasi-operasi di atas dapat dilakukan secara sequential dan terakhir mereturn variabel terakhir yang ada pada line tersebut. So, kode di atas nantinya akan menjalankan operasi ``` b = b * 10 // b = 100 ```, lalu menjalankan ``` a = a + 5 // a = 10 ```, selanjutnya ``` c = a * b // c = 1000 ``` terakhir mereturn nilai c. Gimana, gampang kan? Dengan ini kode kalian akan terlihat lebih keren dan minimalis (tapi susah dibaca).

Penjelasan lebih lanjut bisa dilihat di [sini](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Comma_Operator)

**#5 Short-Circuit Evaluation, create awesome logic in one line code**

Nah metode ini adalah cara lain agar kodingan kamu terlihat lebih keren. Kalo biasanya kamu bikin logic dengan menggunakan if else, maka di sini kamu hanya menggunakan dua operator yaitu && (and) dan || (or). Dengan cara ini, kode yang biasanya butuh 5 - 10 baris bisa kamu buat dengan hanya satu baris saja! Penggunaannya juga sering dikombinasikan dengan comma separator di atas.
Untuk ilustrasinya mungkin bisa dilihat pada kode berikut.

~~~

function hello() {
	alert("Hello world");
}

function doIt(flag) {
	if (flag) {
		hello();
	}
}

function doItInOneLine(flag) {
	return flag && hello();
}

function doIt2(flag) {
	if (!flag) {
		hello();
	}
}

function doItInOneLine2(flag) {
	return flag || hello();
}

~~~

Bisa dilihat dari contoh kode di atas. antara fungsi ```doIt()``` dan ```doItInOneLine()``` sebenarnya akan melakukan operasi yang sama, yaitu jika flag bernilai true, maka fungsi ```hello()``` akan dipanggil. Cuma bedanya fungsi ```doIt()``` memerlukan tiga baris tetapi lebih mudah dimengerti sedangkan fungsi ```doItInOneLine()``` memerlukan pemahaman lebih mendalam mengapa bisa bekerja. Untuk memahaminya kita harus tahu cara kerja lazy evaluation dari operator && (and), di mana jika statement pertama sudah true, maka statement kedua tidak akan dijalankan. Hal yang sama juga terjadi antara ```doIt2()``` dan ```doItInOneLine2()``` yang memakai operator || (or), di mana jika operator pertama sudah true, maka operator ke dua tidak akan dijalankan.

Nah dengan cara ini kodingan kalian akan terlihat lebih keren dan minimalis, walaupun akan membuat developer lain yang membaca akan sedikit mengernyitkan dahinya.

Penjelasan lebih lanjut bisa dilihat di [sini](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Logical_Operators#Short-Circuit_Evaluation)

Oke mungkin segitu dulu yang bisa awak share kali ini. Sampai jumpa di tulisan awak berikutnya yak !!

Thank You !!
