---
layout: page
title: Contribute
sitemap: false
---

Untuk dapat berkontribusi (mengubah atau menambahkan artikel baru), anda dapat melakukan forking project ini dengan cara:

1. Kunjungi project ini di gitlab badr interactive dengan alamat: [https://gitlab.badr.co.id/badr-interactive/myelin](https://gitlab.badr.co.id/badr-interactive/myelin).

2. Klik tombol `Fork` seperti yang terlihat pada gambar di bawah ini:
![image]({{ site.baseurl }}/public/images/fork-project.png){:width="100%"}

3. Setelah melakukan `Fork`, maka project `myelin` ini akan terduplikasi kedalam akun anda. Misal, apabila anda memiliki
akun dengan nama `yahya` maka anda akan memiliki repository baru dari project ini dengan alamat `https://gitlab.badr.co.id/yahya/myelin`

4. Clone repository `myelin` yang sudah anda miliki dengan menggunakan perintah `git clone https://gitlab.badr.co.id/yahya/myelin.git` dari terminal anda. Berikut ini adalah struktur direktori dari project `myelin` yang akan anda dapatkan setelah melakukan `git clone`:
![image]({{ site.baseurl }}/public/images/directory-structure.png)

5. Untuk menambahkan tulisan baru, buat berkas dengan nama `yyyy-mm-dd-judul-tulisan.md` di dalam direktori `_posts`.
6. Push ke repository dengan menggunakan perintah `git push origin master`
7. Klik menu `Merge Request` yang berada di bagaian sidebar menu gitlab.
![image]({{ site.baseurl }}/public/images/merge-request.png)

8. Klik tombol `New Merge Request` yang berada di sebelah kanan atas halaman.
![image]({{ site.baseurl }}/public/images/merge-request-2.png){:width="100%"}

9. Pilih branch `master` di bagian `source branch` dan `target branch`.
![image]({{ site.baseurl }}/public/images/merge-request-3.png){:width="100%"}

10. Klik tombol `Compare branches and continue`.
11. Isi penjelasan tentang perubahan yang dibuat.
![image]({{ site.baseurl }}/public/images/merge-request-4.png){:width="100%"}

12. Kemudian klik tombol `Submit Merge Request`.
![image]({{ site.baseurl }}/public/images/merge-request-5.png){:width="100%"}
