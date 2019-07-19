---
title: Rust ile hello world!
date: 2019-07-10 14:13:40
tags: [rust, hello-world]
---


## Kurulum
Rust ile bir şeyler geliştirmeye başlamadan önce Rust derleyicisini bilgisayarınıza kurmanız gereklidir. Bu işlem için bir Rust projesi olan [rustup](https://rustup.rs/) kullanılmaktadır.

Unix/Linux tabalı sistemler için kurulum aşağıdaki bash scriptini çalıştırılarak kolayca yapılmaktadır. Windows tarafında ise bir exe ile kurulum gerçekleştirilebilmekte. 

<!-- more -->
  
 
Ancak ben Unix/Linux tarafından devam edeceğim. Windows'ta bu işlerin daha da kolay olacağına inanıyorum, denemedim.
    
```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

Bu komut satırı çalıştırıldıktan sonra alttaki çıktıyı alacaksınız.

```none
info: downloading installer

Welcome to Rust!

This will download and install the official compiler for the Rust programming
language, and its package manager, Cargo.

It will add the cargo, rustc, rustup and other commands to Cargo's bin
directory, located at:

  /Users/veli/.cargo/bin

This path will then be added to your PATH environment variable by modifying the
profile files located at:

  /Users/veli/.profile
  /Users/veli/.zprofile
  /Users/veli/.bash_profile

You can uninstall at any time with rustup self uninstall and these changes will
be reverted.

Current installation options:

   default host triple: x86_64-apple-darwin
     default toolchain: stable
  modify PATH variable: yes

1) Proceed with installation (default)
2) Customize installation
3) Cancel installation
```

Burada varsayılan (default) şekilde kuruluma devam etmek için ```1```'i seçerek işlemi bitiriyoruz.

```none
Rust is installed now. Great!
```

## Hello World!

Bu ekranı gördükten sonra "Hello World!" örneğine geçebiliriz.

Önce bir klasör açıp içerisinde ```main.rs``` dosyası yaratalım.

```bash
mkdir hello_world
cd hello_world
touch main.rs
```

Sonrasında dilediğiniz bir metin düzenleyici ile ilk kodumuzu yazalım. Öncelikle ```main``` isimli bir fonksiyon tanımlamamız gerekli, çünkü bütün rust uygulamaları ```main``` fonksiyonunu çalıştırarak koşar.

```rust
fn main() {
}
```

Tanımladığımız fonksiyonun içerisinde yazdırma fonksiyonunu (yeni başlayanlar için makro demedim. Merak edenler için: [std::println!](https://doc.rust-lang.org/1.9.0/std/macro.println!.html)) çağıralım.

```rust
fn main() {
  println!("hello world!");
}
```

Dosyayı kaydettikten sonra aşağıdaki komutu çalıştırarak kodumuzu derliyoruz.
```bash
rustc main.rs
```

Derleme sonucu üretilen ```main``` adındaki binary dosyayı koşturarak ilk denememizi bitiriyoruz.

```bash
hello_world git:(master) ✗ ./main
hello world! 
```