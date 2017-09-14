---
layout: post
title: Membuat Floating Sidebar dengan CSS & Javascript
category: 'Web Development'
author: yahyaman
---

* TOC
{:toc}

Pernah melihat web sidebar yang mengambang alias floating? Contohnya bisa lihat
[di sini](http://huda.id/detail?q=surga&t=067106440652062c064e06460651064e0629064e&lang=id).

Jadi ketika halaman baru dibuka, sidebar-nya seperti biasa akan ikut bergulir (_scroll_) bersama halaman.
Nah ketika sidebar sudah mencapai batas atas tampilan, magically posisi sidebar-nya akan menjadi tetap
dan tidak ikut bergulir lagi mengikuti halaman webnya. Kira-kira gambarannya akan jadi seperti
gambar di bawah ini:

![floating-1]({{ site.baseurl }}/public/images/web/floating-01.png)

Pertama kali dibuka, sidebar di kiri tampil seperti biasa.

![floating-2]({{ site.baseurl }}/public/images/web/floating-02.png)

Saat discroll ke bawah, sidebar di kiri akan _floating_ di tempatnya seperti pada gambar.

Apakah itu semacam sihir atau jutsu?
Mari kita pelajari! :D

<!-- break -->

## Persiapan

1. Kita buat dulu dokumen HTML sederhana bernama `index.html`, seperti di bawah ini:

        <html>
        <head>
            <meta charset="UTF-8">
            <title>Floating Sidebar</title>
            <link rel="stylesheet" href="style.css">
        </head>
        <body>
            <div class="header"></div>
            <div class="container">
                <div class="sidebar" id="sidebar">
                    <div class="sb-menu" id="sb-menu">
                        This is sidebar menu
                    </div>
                </div>
                <div class="content">
                    This is Content!
                </div>
            </div>
            <div class="footer">
                Hello footer here!
            </div>
        </body>
        </html>

2. Terlihat di sana ada `style.css` yang harus di-link, so kita bikin juga:

        .header, .footer {
            height: 100px;
            background-color: gray;
        }

        .container {
            height: 800px;
        }

        .sidebar {
            float: left;
            width: 20%;
            background-color: yellow;
        }

        .content {
            float: left;
            width: 80%;
            height: 500px;
            background-color: orange;
        }

3. Sekarang kalau `index.html` dibuka di browser, kira-kira akan tampil seperti ini:

   ![floating-3]({{ site.baseurl }}/public/images/web/floating-03.png)

Sorry kalo style-nya norak, tujuannya supaya bisa ngedemoin maksud tulisan ini aja, hehe.

## Styling

So, dari contoh halaman di atas, kalo kita scroll ke bawah tentunya sidebar akan ikut ke-scroll,
dan ikutan menghilang ke atas. Coba scroll ke bawah sampe mentok footer, nanti bakal jadi begini:

![floating-4]({{ site.baseurl }}/public/images/web/floating-04.png)

Tuh kan si Derp sampe bingung karena sidebar-nya ngilang, hehe.

Nah gimana caranya supaya sidebar melayang? Coba kita tambah style untuk menu yang ada di dalam sidebar:

    .sb-menu {
        position: fixed;
        top: 10;
        left: 10;
    }

Wow, sekarang dia jadi melayang! Mau di-scroll kemanapun dia bakal ada di situ-situ aja.

![floating-5]({{ site.baseurl }}/public/images/web/floating-05.png)

Ternyata itu dia rahasia _styling_ nya: dengan properti CSS untuk `position` dibikin `fixed`, elemen
html akan berada di posisi yang tetap di viewport browser[^position]. Eh tapi kayak ada yang aneh ya?
itu elemen `div.content` kenapa jadi mentok ke kiri ya? Hm, ternyata `position: fixed;` ini mempunyai
efek menyamping, eh efek samping maksudnya, yaitu elemen dengan nilai itu akan 'dicopot' dari
positioning HTML biasa yang bersifat block. Artinya, `div.sidebar` dianggap kosong tak punya bentuk
(dalam kasus ini height nya nol), karena anaknya udah _floating_ di 'atas' dokumen.
Oya, maksudnya _floating_ di sini bukan properti `float` di CSS ya, itu beda lagi.

Nah supaya `div.sidebar` punya bentuk, ya udah atur aja di CSS-nya `height: 1px;`. Atau supaya lebih
fleksibel, ubah jadi `min-height: 1px;`[^height-diff]. Ngerti lah ya bedanya :p. Oya, _styling_ yang ada di atas itu
kita jadiin kelas tersendiri aja, misal `.sticky`:

    .sticky {
        position: fixed;
        top: 10;
        left: 10;
    }

Kita kan pengen sidebar yang awalnya terlihat biasa, namun setelah discroll sampe batas, berubah jadi
melayang. Nah CSS class di atas bakal kita pake di elemen kita. Gimana caranya?
kita gunakan 'senjata' andalan web developer: Javascript!

## Javascript

Oke, sekarang kita masuk ke lojiknya. Kapan elemen kita mesti ditambahin CSS class `.sticky`?
Obviously ketika scrollnya pas banget di atas elemen kita kan. Nah gimana cara 'ngedenger' (_listening_) kalo
halaman kita lagi discroll dan udah sampe mana scrollnya? Well, kalau kamu developer web sejati
hal begini mah kecil lah ya, ada library multifungsi bernama `jQuery`.

Tapi..gimana kalo pake pure javascript? Iya, gak pake jquery! Gile lu ndro!?

Hehe, emang sih jadinya bakal agak ribet, tapi asik kok. Oke langsung aja nih script-nya:

{% highlight html linenos %}
<script>
  document.addEventListener("DOMContentLoaded", function(event) {
    var sidebar = document.getElementById("sidebar");
    window.onscroll = function() {
        if (window.scrollY > (sidebar.getBoundingClientRect().top + window.scrollY)) {
            document.getElementById("sb-menu").className = "sticky";
        } else {
            document.getElementById("sb-menu").className = "";
        }
    }
  });
</script>
{% endhighlight %}

Loh, ternyata cuma 12 baris! Tanpa tag `<script></script>` nya malah jadi cuma 10 baris :D

Coba kita bedah per baris..

* __Baris 2__ : Kita masukin scriptnya ke event ketika semua DOM element sudah di-load. Mirip `$(document).ready` di
jquery gitu lah.
* __Baris 3__ : Ambil elemen `div.sidebar` untuk kita pake sebagai _anchor_ atau batas yang akan kita pakai
* __Baris 4__ : Nah sekarang kita masukin `listener` ketika halamannya discroll. Jadi setiap kali halaman
scrolling, fungsi ini yang akan dijalankan.
* __Baris 5-9__ : Di sini lojiknya. Nilai dari `window.scrollY` itu adalah seberapa banyak halaman sudah
discroll ke bawah (`y-axis`)[^scrolly]. Gampangnya itu adalah posisi top dari scrollbar yang biasanya ada di kanan browser.
Nah kemudian properti `sidebar.getBoundingClientRect().top` itu adalah jarak top sebuah elemen relatif
ke viewport[^bounding]. Untuk lebih jelasnya silakan lihat gambar di bawah:

![floating-6]({{ site.baseurl }}/public/images/web/floating-06.png)
![floating-7]({{ site.baseurl }}/public/images/web/floating-07.png)
![floating-8]({{ site.baseurl }}/public/images/web/floating-08.png)

Nah terlihat di situ kalau nilai dari `window.scrollY + sidebar.getBoundingClientRect().top` akan selalu sama.
Nilai ini adalah posisi atau jarak vertikal elemen _anchor_ kita yaitu `.sidebar` relatif ke atas halaman.
So, kita pakai nilainya untuk jadi acuan perubahan CSS class. Ketika scroll halaman sudah melewati posisi
tersebut, maka kita tambahkan class `.sticky` ke elemen `#sb-menu`. Sebaliknya, jika scroll kembali ke atas,
maka kita hapus class tersebut. Beres deh.

Ternyata sederhana ya? :D

Oya, kalau misal udah terlanjur basah pakai jQuery buat web-nya, ini versi script di atas kalau pakai jQuery:

    $(window).scroll(function {
        var window_top = $(window).scrollTop();
        var sidebar_top = $('#sidebar').offset().top;
        if (window_top > sidebar_top) {
            $('#sb-menu').addClass('sticky');
        } else {
            $('#sb-menu').removeClass('sticky');
        }
    });

Ternyata di jQuery ada nilai `$('#sidebar').offset().top` yang merupakan posisi vertikal elemen relatif
ke halaman[^jQuery], jadi gak perlu dihitung lagi. Oya versi jQuery-nya mesti yang >= 1.0 ya.

Yak sekian saja tutorial kali ini, semoga gak bingung. Karena myelin ini sifatnya _open source_, so jangan
ragu kalau mau edit post ini buat nambahin atau ngebetulin sesuatu, misal kompatibilitas lintas browser dan versi.
Semua kode di atas berjalan lancar di Windows 10 dengan browser Firefox, Waterfox & Chrome versi baru-baru ini. :D

## Referensi

[^position]: [Properti `position`](https://developer.mozilla.org/en/docs/Web/CSS/position)
[^height-diff]: [height & min-height diff](http://www.dynamicsitesolutions.com/css/height-and-min-height/)
[^scrolly]: [window.scrollY](https://developer.mozilla.org/en-US/docs/Web/API/Window/scrollY)
[^bounding]: [Element.getBoundingClientRect()](https://developer.mozilla.org/en-US/docs/Web/API/Element/getBoundingClientRect)
[^jQuery]: [jQuery offset()](https://api.jquery.com/offset/)
