---
layout: page
title: About
---

* TOC
{:toc}

## Apa itu knowledge base?
knowledge base merupakan sebuah teknologi yang digunakan untuk menyimpan atau mendokumentasikan explicit knowledge[^explicitKnowledge]
yang dimiliki oleh seseorang dengan tujuan untuk mempermudah akses dan penyebaran knowledge yang dimiliki oleh setiap individu. Dengan menggunakan konsep ini,
diharapkan setiap individu yang berada di dalam tim development dapat bersama-sama mengumpulkan dan berbagi knowledge dengan individu yang lain dalam rangka
mengingkatkan kapasitas dan mempersingkat learning curve[^learningCurve] dari masing-masing individu.

## Mengapa hal ini penting?
Knowledge yang dimiliki oleh indvidu merupakan sebuah intangible asset[^intangibleAsset] yang sangat berharga karena untuk mendapatkannya dibutuhkan waktu dan biaya yang besar.
Sebagai contoh, untuk mendapatkan sebuah knowledge tentang bagaimana membangun sebuah aplikasi e-commerce yang baik, dibutuhkan banyak pengalaman dalam membangun
aplikasi terkait (tidak cukup hanya dengan membaca text book, artikel blog, mendengarkan podcast ataupun menonton video tutorial).
Pengalaman-pengalaman yang terakumulasi dalam setiap pengerjaan proyek pada akhirnya akan membentuk pola pikir, kebiasaan dan best practice dalam diri setiap
individu yang terlibat dalam proyek-proyek e-commerce tersebut.

Lalu, bagaimana dengan individu baru (newcomer) yang nantinya akan terlibat dalam proyek e-commerce tersebut? apakah individu tersebut harus melalui proses yang
sama dengan individu lain (mengerjakan banyak e-commerce terlebih dahulu untuk mendapatkan knowledge yang dibutuhkan)? ideal-nya ya! (itulah mengapa ada perbedaan dalam
merekrut individu yang berpengalaman dengan individu yang fresh graduate) akan tetapi cara ini membutuhkan biaya dan waktu yang tidak sedikit. Coba bayangkan apabila setiap
individu berpengalaman memiliki knowledge yang setara dengan pengerjaan proyek selama 1 tahun, maka selama itu pula seorang newcomer harus terlibat dalam pengerjaan proyek
e-commerce untuk mendapatkan knowledge yang sebanding.

Bagaimana solusinya? Mengapa kita tidak menganggap saja setiap knowledge yang dimiliki oleh individu-individu berpengalaman tersebut sebagai sebuah knowledge yang dimiliki bersama?
apabila setiap knowledge yang dimiliki oleh setiap individu dapat dikumpulkan secara kolektif[^collectiveIntelligence] dan disimpan kedalam sebuah repository knowledge untuk kemudian diakses oleh individu yang lain,
maka setiap individu yang berada di dalam tim development akan dapat memiliki knowledge yang sama (untuk kasus ini best case-nya setara dengan 1 tahun pengerjaan proyek) dan mempersingkat
learning curve masing-masing individu karena tidak harus memulai dari awal lagi. Dalam konteks pembelajaran kolektif, dengan adanya knowledge base ini diharapkan pertumbuhan Knowledge
setiap individu dapat meningkat pesat dikarenakan saling mengisi satu sama lain.

## Bagaimana cara berkontribusi?
Knowledge base ini dibuat dengan menggunakan jekyll [^jekyll] sebuah static site generator berbasiskan markdown[^markdown] dan kramdown[^kramdown] syntax. Untuk dapat menambahkan tulisan pada site ini anda cukup
meng-clone project ini di alamat: `https://server.badr.co.id:8001/salman.farisi/badr-kb.git` kemudian buat sebuah file baru di dalam
direktori `_post` degan format `yyyy-mm-dd-judul-tulisan.md`. Untuk lebih jelasnya tentang bagaimana menjalankan jekyll di laptop dan bagaimana caranya berkontribusi silahkan lihat penjelasannya
pada halaman [instalasi]({{site.baseurl}}/installation) dan [cara berkontribusi]({{site.baseurl}}/contribute).


## Referensi

[^explicitKnowledge]: [https://en.wikipedia.org/wiki/Explicit_knowledge](https://en.wikipedia.org/wiki/Explicit_knowledge)
[^intangibleAsset]: [https://en.wikipedia.org/wiki/Intangible_asset](https://en.wikipedia.org/wiki/Intangible_asset)
[^learningCurve]: [https://en.wikipedia.org/wiki/Learning_curve](https://en.wikipedia.org/wiki/Learning_curve)
[^collectiveIntelligence]: [https://en.wikipedia.org/wiki/Collective_intelligence](https://en.wikipedia.org/wiki/Collective_intelligence)
[^jekyll]: [https://jekyllrb.com/](https://jekyllrb.com/)
[^markdown]: [https://daringfireball.net/projects/markdown/](https://daringfireball.net/projects/markdown/)
[^kramdown]: [http://kramdown.gettalong.org/](http://kramdown.gettalong.org/)
