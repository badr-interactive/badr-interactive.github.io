---
layout: post
title: CocoaPods - iOS Dependency Manager
author: Riyan N. Hidayat
category: 'IOS Development'
---

* TOC
{:toc}

CocoaPods adalah salah satu dependency manager di platform iOS, counterpart dari Gradle yang merupakan dependency manager Android. Di dalam repository official CocoaPods ada banyak library yang bisa dimanfaatkan untuk mempercepat development aplikasi.

## Installasi CocoaPods di MacBook
Jalankan perintah ini di `terminal` untuk melakukan installasi CocoaPods di MacBook:

    sudo gem install cocoapods


## Penggunaan CocoaPods dalam Project
Untuk dapat menggunakan CocoaPods sebagai dependency manager lakukan langkah-langkah berikut ini:

1. Create New Project iOS - `AwesomeApp`.
2. Buka direktori tempat project tersebut berada di `terminal`.
3. Jalankan perintah berikut di direktori tersebut:

        pod init --verbose

<!-- break -->

4. Buka file Podfile. Contoh isi awal Podfile sbb:

        # Uncomment this line to define a global platform for your project
        # platform :ios, '8.0'
        # Uncomment this line if you're using Swift
        # use_frameworks!

        target 'AwesomeApp' do

        end

    Uncomment line kedua dan ganti versi target iOS sesuai dengan target yang diinginkan. Contoh:

        platform :ios, '8.0'

    atau

        platform :ios, '7.1'

    Uncomment line keempat karena `AwesomeApp` menggunakan Bahasa `Swift`.

        use_frameworks!

    Di dalam scope `target 'AwesomeApp'` masukkan library-library yang akan di pakai di project. Contoh:

        target 'AwesomeApp' do
            pod 'AFNetworking'
            pod 'ReactiveCocoa'
        end

5. Jalankan perintah berikut untuk melakukan installasi library-library tersebut ke dalam project kita.

        pod install --verbose

6. Setelah perintah tersebut selesai dijalankan CocoaPods akan membuat file `xcworkspace` yang merupakan workspace project yang berisikan project `AwesomeProject` dan project `Pod` yang berisi library-library yang telah di masukkan ke `Podfile`. Buka file tersebut untuk melanjutkan proses development.

## Penambahan Library
Kadang ketika di tengah-tengah development kita baru menyadari bahwa kita membutuhkan library lain dalam project kita. Untuk itu kita perlu melakukan `update` terhadap dependency library yang kita pakai. Langkah-langkahnya adalah sebagai berikut.

1. Tambahkan library ke dalam scope `target 'AwesomeApp' di `Podfile`.

        target 'AwesomeApp' do
            pod 'AFNetworking'
            pod 'ReactiveCocoa'
            pod 'SVProgressHUD'
        end

2. Jalankan perintah:

        pod update --verbose

## Penggunaan Library dalam Project
Untuk dapat menggunakan library dalam project hanyalah sesederhana menambahkan statement `import` di `Swift` file yang menggunakan library tersebut.

    import ReactiveCocoa
