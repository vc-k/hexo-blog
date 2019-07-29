---
title: Cargo, Rust dilinin çok fonksiyonlu paket yöneticisi
date: 2019-07-23 21:38:24
tags: [rust, cargo]
---

Cargo denen komut satırı arayüzü (command line interface), hem bir paket yönetici hem de derleme aracıdır. Eğer bu dilde ilerlemek istiyorsanız, ```rustc``` yerine bu aracı hemen kullanmaya başlamalısınız. Bu yazıda derleme konusuna değineceğim ancak cargo ile proje gereksinimlerinin de otomatik olarak kurulduğunu söylemem gerek.

<!-- more -->

Eğer rust için gerekli kurulumları ```rustup``` yardımı ile kurduysanız cargo bu kurulum aşamasında yüklenmiş olmalıdır. Hemen kontrol edelim.

```bash
➜  ~ cargo --version
cargo 1.34.0 (6789d8a0a 2019-04-01)
```

## Cargo ile yeni proje yaratma
```bash
➜  ~ cargo new projem
     Created binary (application) `projem` package
```
Bu kadar :).

Bu komut öncelikle ```projem``` adında yeni bir klasör yaratır. Şimdi de ne yaptığına biraz daha detaylı bakalım.

```bash
➜  ~ cd projem
➜  projem git:(master) ✗ tree -a
.
├── .git
│   ├── HEAD
│   ├── config
│   ├── description
│   ├── hooks
│   │   └── README.sample
│   ├── info
│   │   └── exclude
│   ├── objects
│   │   ├── info
│   │   └── pack
│   └── refs
│       ├── heads
│       └── tags
├── .gitignore
├── Cargo.toml
└── src
    └── main.rs

1 directory, 2 files
```

Benim ilk dikkatimi çeken, proje için git başlangıcı yapmış.
Git klasörünü ve gitignore dosyasını incelemeyeceğim. 

Diğer yaratılan şeyler ise ```Cargo.toml``` dosyası ve ```src``` klasörü içerisinde ```main.rs``` dosyası. Bu dosyaların ne olduğuna teker teker bakalım.

```bash
➜  projem git:(master) ✗ cat Cargo.toml
[package]
name = "projem"
version = "0.1.0"
authors = ["vc-k"]
edition = "2018"

[dependencies]
```

```Cargo.toml``` dosyasının içine bakınca basit bir konfig dosyası olduğunu görüyoruz. ```[package]``` basşlıgında proje hakkındaki bilgiler, ```[dependencies]``` başlığı altında ise şimdilik hiçbir şey bulunmamakta. İlerleyen yazılarda herhangi bir pakete bağımlılığımız olduğunda buraların yeşilleneceğini söyleyebilirim. Paket demişken, paket ya da kütüphanenin rust'daki karşılığı ```crate``` olarak geçmektedir.

Son olarak cargo'nun yarattığı ```main.rs``` dosyasına bakalım.

```bash
➜  projem git:(master) ✗ cat src/main.rs
fn main() {
    println!("Hello, world!");
}
```

Bu dosya bizi hello world ile karşıladı. Hello world, işin yarısı diye düşünürsek üzerimizden baya bir yükü kaldırmış.

## Cargo ile projemizi derleyelim
Aşağıda yazmış olduğum komut satırı ile bir çırpıda derleme işlemini tamamlıyoruz.

```bash
➜  projem git:(master) ✗ cargo build
   Compiling projem v0.1.0 (/Users/veli/projem)
    Finished dev [unoptimized + debuginfo] target(s) in 4.09s
```

Fark ettiğiniz üzere bu şekilde uygulamayı, optimizasyonlar olmadan ve hata-ayıklama modunda derlemiş olduk.

Canlı ortam için derlemek için bu komuta sadece bir argüman geçmemiz gerekmektedir.

```bash
➜  projem git:(master) ✗ cargo build --release
   Compiling projem v0.1.0 (/Users/veli/projem)
    Finished release [optimized] target(s) in 0.39s
```

Derleme işlemi sonrasında, ```target``` adında yeni bir klasör yaratılır. Bu klasör içerisinde derlenen hedefler için klasörler yer almaktadır.

```bash
➜  projem git:(master) ✗ ll
total 16
-rw-r--r--  1 veli  staff   138B Jul 23 22:36 Cargo.lock
-rw-r--r--  1 veli  staff   122B Jul 23 22:08 Cargo.toml
drwxr-xr-x  3 veli  staff    96B Jul 23 22:08 src
drwxr-xr-x  5 veli  staff   160B Jul 23 22:37 target

➜  projem git:(master) ✗ cd target
➜  target git:(master) ✗ ll
total 0
drwxr-xr-x@ 12 veli  staff   384B Jul 23 22:36 debug
drwxr-xr-x@ 11 veli  staff   352B Jul 23 22:37 release
```

Derlenen kodu çalıştırmak için herhangi bir klasör içerisinde yer alan çalıştırılabilir (executable) dosyayı çağırabiliriz.

```bash
➜  target git:(master) ✗ ./debug/projem
Hello, world!
```

Bu kadar uğraşa ilk defa görenler için girdim oysa ki derleme işini yapan ve ardından uygulamayı koşturan bir komutumuz daha var.

```bash
➜  projem git:(master) ✗ cargo run
    Finished dev [unoptimized + debuginfo] target(s) in 0.15s
     Running `target/debug/projem`
Hello, world!
```

Ayrıca kodunuzu derlenip derlenemeyeceğini kontol etmek isterseniz ```cargo check``` komutunu çalıştırabilirsiniz. Ancak bu komut kodunuzu derleyip koşturulabilir (bak bunu sevdim) çıktıyı üretmeyecektir. 

Bunu öğrendiğimde rust doküman sayfasında yer alan soruyu ben de sordum, muhtemelen siz de sormuş olabilirsiniz. 

Kodumu derlemek varken, neden sadece derlenip derlenemeyeceğini kontrol edeyim? 

Cevap basit, projeniz karmaşıklığı arttıkça projenizin derlenme süresi ciddi derecede artacaktır. Her küçük değişiklik için kodunuzu yeniden derleyecek olursanız geliştirme sürecinde çok vakit harcamanız kaçınılmaz olacaktır. Hello world hariç.

Sonuç olarak ```cargo``` sizin için yaratılmış bir nimet. Kullanmazsanız yazık olur. Alışınca ki alışması çok kolay, çok sevceceksiniz.

Aklınıza takılan sorular için yorum yazabilirsiniz. Çok yoğun olmadıkça 24 saat içerisinde iyi kötü bir cevap yazarım.