---
layout: post
title: Add SSL Self-Signed Certificate in Volley(Android)
category: 'Android Development'
---

## Introduction

Untuk para developer mobile, pasti sudah tidak asing dengan webservice atau biasa di sebut API. Apa itu API? penjelasannya bisa dilihat di [sini](https://wirasetiawan29.wordpress.com/2014/08/17/apa-itu-api/). Tapi, di artikel ini saya tidak membahas tentang API. Di artikel ini saya akan coba menjelaskan bagaimana cara add *certificate* ke dalam *http url connection*. Hal ini dibutuhkan ketika kita mengakses suatu API yang membutuhkan *ssl certificate* untuk sekuritasnya. Di sini saya akan menjelaskan menggunakan library volley pada platform mobile **Android**.   


## Apa itu *SSL Certificate*?

SSL singkatan dari *Secured Socket Layer*. SSL Certificate ini berfungsi untuk mengamankan transaksi data melalui http url. Biasanya situs yang membutuhkan security menggunakan 'https://'.  Transmisi data seperti informasi kartu kredit, nama pengguna dan password account, semua informasi sensitif lain harus diamankan untuk mencegah eavesdropping, pencurian data pada saat proses transaksi online, dan lain-lain. Sertifikat SSL dapat mengamankan data tidak hanya diaplikasikan melalui situs web saja tetapi dalam transmisi email pun dapat dijamin dengan SSL ini. Berikut juga dengan API, karena API ini adalah jembatan antara server dengan aplikasi frontend dalam transmit data. Lebih lengkapnya bisa baca artikel [berikut](http://klien.indositehost.com/knowledgebase.php?action=displayarticle&id=13).

<!-- break -->

## Cara menambahkan *SSL Certificate* menggunakan *Volley*

Sebelum kita menambahkan ssl certificate ke dalam volley, kita harus mempunyai file certificate yang akan kita add. Salah satu caranya adalah dengan mendownload dari web yang menggunakan certificate tersebut melalui browser. Berikut contoh mendownload certificate menggunakan browser ** google chrome** :

1. Biasanya web yang menggunakan certificate, pada chrome akan terlihat warna merah dan ada icon gembok di sebelah kiri urlnya seperti pada gambar.
![download_ssl]({{ site.baseurl }}/public/images/android/add_SSLCertificate/download_ssl1.png){:width="100%"}

2. Klik icon gembok warna merah tersebut, maka akan muncul menu seperti pada gambar, lalu klik link 'detail'.
![download_ssl]({{ site.baseurl }}/public/images/android/add_SSLCertificate/download_ssl2.png){:width="100%"}

3. Akan muncul dialog seperti pada gambar di bawah ini. Lalu klik 'View Certificate'
![download_ssl]({{ site.baseurl }}/public/images/android/add_SSLCertificate/download_ssl3.png){:width="80%"}

4. Setelah muncul dialog pada gambar di bawah ini, klik tombol 'export', lalu save file dalam format '.cer'.
![download_ssl]({{ site.baseurl }}/public/images/android/add_SSLCertificate/download_ssl4.png){:width="80%"}

5. Lalu, buka project android anda menggunakan android studio. Buat folder 'raw'(jika belum ada) di dalam folder 'res'. Certificate siap untuk ditambahkan pada volley.


Defaultnya, pada pemrograman java itu kita bisa menambahkan SSL Certificate di class *HttpsUrlConnection* dengan script :

~~~
httpsURLConnection.setSSLSocketFactory(SSLSocketFactory);
httpsURLConnection.setHostnameVerifier(HostnameVerifier);
~~~


Begitu juga dengan Volley, karena volley base pemrogrammannya menggunakan java. Hanya saja, dalam volley, script tersebut dimasukkan di dalam class HurlStack :

~~~
HurlStack hurlStack = new HurlStack() {
    @Override
    protected HttpURLConnection createConnection(URL url) throws IOException {
        HttpsURLConnection httpsURLConnection = (HttpsURLConnection) super.createConnection(url);
        try {
            httpsURLConnection.setSSLSocketFactory(getSSLSocketFactory(context));
            httpsURLConnection.setHostnameVerifier(getHostnameVerifier());
        } catch (Exception e) {
            e.printStackTrace();
        }
        return httpsURLConnection;
    }
};
~~~


Di mana kita memasukkan certificatenya? Ya, kita masukkan certificate yang sudah kita download tadi ke dalam fungsi `getSSLSocketFactory(context)` seperti berikut :

~~~
public static SSLSocketFactory getSSLSocketFactory(Context ctx)
    throws CertificateException, KeyStoreException, IOException, NoSuchAlgorithmException, KeyManagementException

    CertificateFactory cf = CertificateFactory.getInstance("X.509");

    Certificate cert = readCert(ctx, R.raw.[nama file .cer]);

    String keyStoreType = KeyStore.getDefaultType();
    KeyStore keyStore = KeyStore.getInstance(keyStoreType);
    keyStore.load(null, null);
    keyStore.setCertificateEntry("ca", cert);

    String tmfAlgorithm = TrustManagerFactory.getDefaultAlgorithm();
    TrustManagerFactory tmf = TrustManagerFactory.getInstance(tmfAlgorithm);
    tmf.init(keyStore);

    TrustManager[] wrappedTrustManagers = getWrappedTrustManagers(tmf.getTrustManagers());

    SSLContext sslContext = SSLContext.getInstance("TLS");
    sslContext.init(null, wrappedTrustManagers, null);

    return sslContext.getSocketFactory();
}
~~~


Jangan lupa untuk set Hostname verifier nya juga.

~~~
public static HostnameVerifier getHostnameVerifier() {
    return new HostnameVerifier() {
        @Override
        public boolean verify(String hostname, SSLSession session) {
            //return true;
            HostnameVerifier hv = HttpsURLConnection.getDefaultHostnameVerifier();
            return hv.verify("localhost", session);
        }
    };
}
~~~

Setelah itu, add ```HurlStack``` pada request queque volley yang telah anda buat seperti ini ```Volley.newRequestQueue(mcontext, hurlStack);```.
Selesai... Ya, sudah selesai lah kita add certificate pada Volley.

Terima kasih sudah menyimak artikel ini, semoga bermanfaat... :D
