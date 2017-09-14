---
author: Muhammad Alif
layout: post
title: "Insight From Huurun Project : Pengalaman Melakukan Pair Programming"
category: 'Developer Insight'
---

* TOC
{:toc}

Hai para myelinians, pada tulisan kali ini awak (baca saya) mau berbagi kembali insight yang didapat dari ngerjain project Huurun. Kalo kemaren kita udah membahas 'unek-unek' dari ngelanjutin kodingan 'you know who', maka sekarang saya akan share pengalaman yang 'enak-enak' dari ngoding bareng dengan menggunakan konsep `Pair Programming`.

Jadi pada project huurun ini, pertama kalinya saya menerapkan pair programming bareng 'Oom'. Karena pair programming bareng 'Oom' mau tidak mau saya jadi harus menyesuaikan ritme kode saya dengannya. Saya juga pertama kalinya memakai starter kit imba yang dibuat ama 'Oom'. Walaupun awalnya terasa sulit, namun pada akhirnya saya dapat menyesuaikan ritme kode saya dengan kode yang dibuat sama 'Oom'. Berikut beberapa insight dan pelajaran yang saya dapat dari ngoding bareng 'Oom'.

## Work with two monitors : Increase your productivity
Jadi pada project huurun inilah pertama kalinya saya mencoba untuk ngoding dengan 2 monitor. Tentunya karena terinspirasi dari 'Oom'. Awalnya saya merasa sedikit kesulitan saat memakai dua layar, karena pikiran kita jadi terpecah pada dua layar. Tapi akhirnya saya bisa beradaptasi dengan menggunakan dua monitor.

Ok, lalu kenapa ngoding dengan dua monitor bisa meningkatkan produktifitas? Padahal bukannya kita malah jadi tidak fokus jika ada dua layar? Sebenarnya ini tergantung dari penggunanya. Jika satu monitor dipakai buat ngoding dan monitor satunya dipakai buat nonton anime (ex: Yu Gi Oh) ya jelas pas gak akan fokus. Tapi kalau satu monitor dipakai buat text editor buat ngoding dan satu lagi dipakai buat nampilin hasil kodingan kita di browser, maka ini akan sangat menghemat waktu programmer! Kalo biasanya saya setelah ngoding harus minimize halaman kode lalu buka browser, sekarang saya tidak perlu lagi untuk minimize text editor saya, cukup dengan pindahkan kursor ke layar kedua maka hasil dari kode saya akan terlihat di browser. Jika hal ini dilakukan berulang maka akan banyak sekali waktu yang bisa dihemat.

## Unit Testing, Create a code to test your code
Selain ngoding dengan 2 monitor, pada project huurun ini juga saya pertama kalinya menerapkan unit testing. Walaupun ngoding bareng 'Oom' hanya berlangsung selama 2-3 hari, namun ternyata sudah cukup untuk menularkan ilmunya kepada saya, termasuk penggunaan unit testing ini. Ketika saya pertama kali diajari tentang unit testing, sempat terpikir dalam benak saya 'Ini penting gak sih?' atau 'Kok agak you dont say gitu yah', sampai 'Ah buat apa bikin unit testing, buang-buang waktu aja'. Tapi karena ini bagian dari SOP developer mau gak mau akhirnya saya ikutin aja apa kata 'Oom'. Nah setelah saya benar-benar hands on dan menerapkan unit testing, efeknya itu baru terasa. Pikiran-pikiran negatif saya sebelumnya jadi hilang seketika. Sekarang saya jadi berpikir 'Kok keren ya' atau 'Kok enna ya'. Jadi seperti ada kepuasan tersendiri saat kita berhasil bikin code yang dipakai untuk mengetest code utama kita. Seperti yin dan yang atau hitam dan putih yang saling menjaga dan melengkapi satu sama lain. Walaupun kelihatannya buang-buang waktu, tapi sebenarnya jika dibandingkan dengan waktu testing yang dihabiskan untuk mengetest app kita, maka akan sepadan bahkan lebih menghemat waktu. Efeknya juga terasa pas review meeting bersama klien. Kode yang dihasilkan jadi bugless dibanding saat tidak menggunakan unit testing. Apakah ini sebuah kebetulan? Kurasa tidak..

## Create Modular code with Form Request, Event & Listener
Di project huurun ini juga pertama kalinya saya menerapkan yang namanya Form Request & Event. Jadi apa kegunaan dari Form Request & Event ini? Intinya adalah kita jadi memisahkan kode-kode kita yang kebanyakan di controller ke class atau file yang terpisah. Form Request berguna untuk melakukan validasi request terlebih dahulu sebelum dihandle oleh controller. Sedangkan event berguna untuk menyimpan event-event tertentu yang perlu handling khusus, seperti kirim email atau notifikasi. Dengan cara ini, kode yang kita bikin akan menjadi lebih modular. Kalo mau tahu lebih lengkapnya bisa dilihat di [sini](https://laravel.com/docs/5.3/validation#form-request-validation) dan di [sini](https://laravel.com/docs/5.3/events).

Lalu, apa pentingnya membuat kode yang modular? Bukankah file kode yang dibikin jadi banyak? Jadi makin sulit didebug dong?
Tidak! Justru dengan membuat kode yang lebih modular, akan lebih mudah bagi kita untuk melakukan debug dan maintenance. Selain itu dengan membuat kode yang modular, kode yang kita buat akan lebih rapih dan terstruktur. Biasanya kan kita bikin validasi form dan kirim email semuanya di controller. Akibatnya kodingan di controller kita akan jadi gemuk alias banyak dan jika ada suatu masalah di validasi form atau di send emailnya, kita harus nyari2 dulu di controllernya di mana letak kode yang dimaksud. Lain halnya jika kita terapkan Form Request dan Event tadi. Walaupun file yang dibuat jadi lebih banyak, akan tetapi jika suatu saat ada bug atau ada yang perlu diubah maka kita tidak perlu membongkar2 kode di controller lagi. Karena kita sudah tahu harus ngeceknya di mana. Dan yang lebih penting adalah dengan membuatnya menjadi modular maka akan lebih gampang untuk dibuat unit testnya untuk masing2 implementasi kode tersebut.


Oke mungkin segitu dulu yang bisa awak share kali ini. Sampai jumpa di next project yak !!
*
Kalau ada sumur di ladang, bolehlah kita menumpang mandi.
Kalau ada project yang panjang, bolehlah kita berbagi lagi.
*

Thank You !!
