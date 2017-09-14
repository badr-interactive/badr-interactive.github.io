---
layout: page
title: Installation
---

* TOC
{:toc}

## Prasyarat

* Ruby (>= 2.0.0)
* RubyGems
* NodeJS
* Python 2.7

## Melakukan Instalasi Ruby

### Ubuntu / Mint

Untuk mempermudah proses instalasi ruby pada linux disarankan menggunakan Ruby version manager (rvm)[^rvm] yang dapat diinstall dengan
mengikuti langkah-langkah berikut ini:

1. Install gpg2 key dengan mengetikkan perintah: `gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3`
2. Install RVM stable `\curl -sSL https://get.rvm.io | bash -s stable --ruby`
3. Apabila RVM telah terinstall sempurna maka anda akan dapat menjalankan perintah `rvm list` di terminal dan akan terlihat daftar versi ruby yang sudah pernah diinstall. (untuk instalasi rvm pertama kali, anda tidak akan melihat daftar ruby di terminal anda).
4. Berikutnya adalah melakukan instalasi ruby dengan cara mengetikkan perintah `rvm install <ruby_version>` seperti misalnya `rvm install 2.2.4` untuk melakukan instalasi ruby versi 2.2.4.
5. Set up versi default ruby yang akan digunakan dengan mengetikkan perintah `rvm alias create default ruby-2.2.4`.
6. Jalankan perintah `ruby version` untuk memastikan bahwa versi ruby yang aktif sama dengan versi ruby yang sudah kita siapkan dengan menggunakan RVM.

### Mac OSX

Secara default ruby sudah terinstall di Mac OSX, untuk memastikannya silahkan buka `terminal` dan ketikkan perintah `ruby -v` untuk melihat
versi ruby yang terinstall di dalam Mac OSX anda.

### Windows

Untuk dapat menginstall Ruby pada sistem operasi windows lakukang langkah-langkah berikut ini:

1. Download installer ruby pada tautan [ini](http://rubyinstaller.org/downloads/)
2. Install ruby dengan menggunakan installer diatas.

## Melakukan Instalasi RubyGems

### Ubuntu / Mint

Untuk dapat menginstall rubygems pada distro linux Ubuntu / Mint, cukup jalankan perintah `sudo apt-get install rubygems` pada terminal.
Apabila proses instalasi sudah berhasil, maka anda akan dapat menjalankan perintah `gem` melalui command command line atau terminal anda.

### Mac OSX

Untuk melakukan instalasi Ruby Gems pada Mac OSX silahkan lakukan langkah-langkah berikut ini:

1. Unduh rubygems dari situs: [https://rubygems.org/rubygems/rubygems-2.5.2.zip](https://rubygems.org/rubygems/rubygems-2.5.2.zip)
2. Ekstrak rubygems-2.5.2.zip dan masuk kedalam direktori rubygems dengan mengetikkan perintah `cd rubygems-2.5.2`
3. Install rubygems dengan menjalankan perintah `ruby setup.rb` (dengan menggunakan user root)
4. Jalankan perintah `gem -v` untuk memastikan bahwa rubygems sudah terinstall dengan sempurna di Mac OSX anda.

### Windows

Untuk melakukan instalasi Ruby Gems pada Windows silahkan lakukan langkah-langkah berikut ini[^jekyll-windows] :

1. Donwload RubyDevKit pada tautan [ini](http://rubyinstaller.org/downloads/) (DevKit-mingw64 atau 32).
2. Ekstrak file `DevKit-mingw64-64-4.7.2-xxx-sfx.exe` ke dalam direktori `RubyDevKit`.
3. Buka `command-line` dan masuk ke dalam direktori `RubyDevKit`.
4. Jalankan perintah `ruby dk.rb init`.
5. Jalankan perintah `ruby dk.rb install`.

## Melakukan instalasi Jekyll

Untuk dapat menginstall jekyll, silahkan jalankan perintah `gem install jekyll` pada terminal / command-line sistem operasi anda.

## Melakukan instalasi Bundler

Untuk dapat menginstall Bundler, silankan jalankan perintah `gem install bundler` pada terminal / command-line sistem operasi anda.

## Cara menjalankan Myelin

Untuk dapat menjalankan `myelin` silahkan buka terminal / command-line sistem operasi anda, masuk kedalam direktori `badr-kb` (setelah melakukan clone di gitlab) kemudian jalankan perintah `bundle install` dan `bundle exec jekyll serve`.

[^rvm]: [https://rvm.io/](https://rvm.io/)
[^jekyll-windows]: [http://jekyll-windows.juthilo.com/](http://jekyll-windows.juthilo.com/)
