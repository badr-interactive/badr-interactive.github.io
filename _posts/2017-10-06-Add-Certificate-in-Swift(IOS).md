---
layout: post
title: Add SSL Self-Signed Certificate in Swift(IOS)
category: 'IOS Development'
---

## Introduction

Kembali lagi dengan saya yang cupu ini, hehehe...<br> 

Nah, sebelumnya kan saya pernah post tentang konfigurasi *SSL self-signed certificate* di Android menggunakan library Volley(untuk baca lengkapnya di [sini](http://engineering.badr.co.id/android%20development/2016/06/15/Add-Certificate-in-Volley.html)), kali ini saya akan share bagaimana cara konfigurasi si *SSL self-signed* di IOS dengan bahasa pemrogramman *Swift*. Yukk, langsung ajah...



## Apa itu *SSL Certificate*?

Mungkin yang sudah pernah baca artikel sebelumnya tentang konfigurasi *SSL self-signed certificate* di Android, paasti tahu apa itu *SSL Certificate*?, karena sudah dijelaskan di sana. Tapi untuk yang belum tahu, ini dia penjelasannya :<br>

SSL singkatan dari *Secured Socket Layer*. SSL Certificate ini berfungsi untuk mengamankan transaksi data melalui http url. Biasanya situs yang membutuhkan security menggunakan 'https://'.  Transmisi data seperti informasi kartu kredit, nama pengguna dan password account, semua informasi sensitif lain harus diamankan untuk mencegah eavesdropping, pencurian data pada saat proses transaksi online, dan lain-lain. Sertifikat SSL dapat mengamankan data tidak hanya diaplikasikan melalui situs web saja tetapi dalam transmisi email pun dapat dijamin dengan SSL ini. Berikut juga dengan API, karena API ini adalah jembatan antara server dengan aplikasi frontend dalam transmit data. Lebih lengkapnya bisa baca artikel [berikut](http://klien.indositehost.com/knowledgebase.php?action=displayarticle&id=13).

<!-- break -->

## Cara menambahkan *SSL Certificate* di Swift

Sebelum kita menambahkan ssl certificate ke dalam pemrogrammannya, kita harus mempunyai file certificate yang akan kita add. Salah satu caranya adalah dengan mendownload dari web yang menggunakan certificate tersebut melalui browser. Berikut contoh mendownload certificate menggunakan browser *google chrome* :

1. Biasanya web yang menggunakan certificate, pada chrome akan terlihat warna merah dan ada icon gembok di sebelah kiri urlnya seperti pada gambar.
![download_ssl]({{ site.baseurl }}/public/images/android/add_SSLCertificate/download_ssl1.png){:width="100%"}

2. Klik icon gembok warna merah tersebut, maka akan muncul menu seperti pada gambar, lalu klik link 'detail'.
![download_ssl]({{ site.baseurl }}/public/images/android/add_SSLCertificate/download_ssl2.png){:width="100%"}

3. Akan muncul dialog seperti pada gambar di bawah ini. Lalu klik 'View Certificate'
![download_ssl]({{ site.baseurl }}/public/images/android/add_SSLCertificate/download_ssl3.png){:width="80%"}

4. Setelah muncul dialog pada gambar di bawah ini, klik tombol 'export', lalu save file dalam format '.cer'.
![download_ssl]({{ site.baseurl }}/public/images/android/add_SSLCertificate/download_ssl4.png){:width="80%"}

5. Lalu, copy file .cer tersebut ke dalam folder project IOS Anda. Certificate siap untuk digunakan.


Buatlah satu class function public untuk membaca certificate tersebut dengan object *SecCertificate* sebagai nilai returnnya. Di sample ini, saya menggunakan SSl self-signed nya 'dev.badr.co.id'

~~~
func getCert()->SecCertificate{
        let rootCertPath:String = Bundle.main.path(forResource: "dev.badr.co.id", ofType: "cer")!
        let rootCertData = NSData(contentsOfFile: rootCertPath)!
        let rootCert:SecCertificate = SecCertificateCreateWithData(nil, rootCertData)!
        
        return rootCert
    }
~~~


Karena di sini saya menggunakan library Alamofire sebagai transmit data dengan server, di artikel ini saya hanya akan menjelaskan bagaimana konfigurasinya di Alamofire. Tambahkan satu function untuk konfigurasi di Alamofirenya :

~~~
var serverTrustPolicy: ServerTrustPolicy!
var serverTrustPolicies: [String: ServerTrustPolicy]!
var afManager: SessionManager! 

func configureAlamoFire() {       
    self.serverTrustPolicy = ServerTrustPolicy.pinCertificates(
        // Getting the certificate from the certificate data            
        certificates: [getCert()],
        // Choose to validate the complete certificate chain, not only the certificate itself
        validateCertificateChain: true,
        // Check that the certificate mathes the host who provided it
        validateHost: true
    )
        
    self.serverTrustPolicies = [
        CERTIFICATE_NAME : self.serverTrustPolicy!
    ]
        
    self.afManager = SessionManager(
        configuration: URLSessionConfiguration.default,
        serverTrustPolicyManager: ServerTrustPolicyManager(policies: self.serverTrustPolicies)
    )
}
~~~


Sebelum request data ke server, panggil fungsi konfigurasi tersebut :

~~~
configureAlamoFire()
self.afManager.request(urlApi, method: .post, parameters: (param as! Parameters), encoding: JSONEncoding.default, headers: getHeader()).responseJSON { response in
            switch response.result {
            case .success:
                // success handler
                break
            case .failure( _):
                //failed handler
                break
            }
        }
~~~


Selesai... Dan sekarang anda punya akses untuk request ke web dev.bar.co.id. Karena ini hanya konfigurasi di Alamofire, untuk selanjutnya nanti akan saya tambahkan untuk konfigurasi menggunakan AFnetwork dan URLSession

Terima kasih sudah menyimak artikel ini, semoga bermanfaat... :D
