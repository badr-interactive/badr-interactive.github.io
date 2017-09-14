---
layout: post
title: App Localization iOS
author: Riyan N. Hidayat
category: 'iOS'
---

* TOC
{:toc}

Localization adalah salah satu fitur yang paling penting apabila aplikasi yang kita buat memiliki target market yang beraneka ragam dan berasal dari negara yang berbeda-beda. Fitur ini memungkinkan aplikasi untuk dapat mengubah bahasa aplikasi tanpa mengubah pengaturan bahasa device. Dengan adanya fitur ini, user akan menjadi lebih nyaman ketika menggunakan bahasa nativenya sendiri dan user tidak akan terganggu karena pengaturan bahasa device tidak terpengaruh dengan pengaturan bahasa aplikasi.

<!-- break -->

## Langkah Awal
1. `Create New File - iOS - Resource - Strings File`.

    ![image]({{ site.baseurl }}/public/images/ios/localization/create_new_file.png){:width="80%"}

2. Berikan nama: `Localizable.strings`.
3. Klik `Localize` pada `File Inspector` tab `Utilities` dan pilih bahasa yang digunakan.

    ![image]({{ site.baseurl }}/public/images/ios/localization/localize.png){:width="50%"}

4. Untuk menambahkan bahasa lain, buka `Project Settings` dan tambahkan bahasa yang diinginkan.

    ![image]({{ site.baseurl }}/public/images/ios/localization/add_new_language.png){:width="80%"}

5. Setelah langkah-langkah diatas berhasil dilakukan, struktur `Project Navigator` akan menjadi sebagai berikut:

    ![image]({{ site.baseurl }}/public/images/ios/localization/localize_project_nav.png){:width="80%"}


## Penambahan Teks Localization
Untuk menambahkan text yang akan di-_localize_:

1. Buka file `Localizable.strings` sesuai dengan bahasa yang diinginkan.
2. Tambahkan sebuah baris _string mapping_.

        "<string key>" = "<string value>";

    Contoh:

        "order_detail" = "Detail Pesanan";
        "order_quantity" = "%d Boks";
        "order_vitamin" = "%@ sebanyak %d mg";

3. Jangan lupa untuk menambahkan _string mapping_ untuk semua bahasa `Localizable.strings` yang digunakan di aplikasi.


## Penggunaan Localization
Untuk memudahkan penggantian bahasa aplikasi tanpa perlu mengubah pengaturan bahasa device, gunakan library `Localize-Swift` yang dapat diunduh di [sini](https://server.badr.co.id:8001/snippets/65). Library ini berdasarkan [marmelroy/Localize-Swift](https://github.com/marmelroy/Localize-Swift) yang telah dimodifikasi oleh [Riyan N. Hidayat](https://id.linkedin.com/in/riyann68).

Setelah library tersebut berhasil di-_import_ dengan benar. Localization dapat dilakukan dengan cara sebagai berikut:

~~~
self.title = "order_detail".localized();
self.quantityLabel.text = "order_quantity".localizedWithFormat(arguments: [10]);
self.vitaminLabel.text = "order_vitamin".localizedWithFormat(arguments: "Vitamin A", 25);
~~~

## Fungsi-Fungsi Favorit
1. Set Default Language:

    Ubah variabel `LCLDefaultLanguage` di `Localize.swift`:

        let LCLDefaultLanguage = "id"

    atau:

        let LCLDefaultLanguage = "en"

2. Get Default Language:

        let lang = Localize.defaultLanguage()
        print(lang) // 'en'

3. Set Current Language:

        Localize.setCurrentLanguage("id")

4. Get Current Language:

        let lang = Localize.currentLanguage()
        print(lang) // 'id'

5. List Available Language:

        let langs = Localize.availableLanguages()
        print(langs) // ['en', 'id']

6. Display Name for Language:

        let langDisplayName = Localize.displayNameForLanguage('id')
        print(langDisplayName) // 'Bahasa Indonesia'

PS: ;)

1. Jangan lupa reload ulang/set ulang text setelah penggantian bahasa ya.
2. Nantikan artikel selanjutnya untuk Localization `attributedText`.
