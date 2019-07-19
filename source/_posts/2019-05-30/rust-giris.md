---
title: Rust programlama dili
date: 2019-05-30 10:12:27
tags: [rust, ownership, borrowing]
---

Rust, bellek ve eşzamanlı çalışma güvenliği üzerine yoğunlaşmış bir sistem programlama dilidir. Bu güvenliği performanstan ödün vermeden sağlamaktadır.

Stackoverflow'un her sene gerçekleştirdiği anketlerde 4 senedir üst üste en çok sevilen dil seçilse de hala popülerlik sıralamasında 20'li sıralarda kendine yer buluyor. Ancak çok popüler olmamasının kötü bir durum olduğunu düşünmek yanlış olur.

# Neden Rust?

Ben neden Rust öğrenmeye karar verdim? Çok önceleri C, C++ ve Fortran çalışmalarım olmasına rağmen uzun zamandır yüksek seviyeli diller ile çalışmaktayım. Son bir kaç senedir de Python üzerinde çalışmalarımı gerçekletiriyorum. Ancak her zaman bir sistem programlama dilini yeteri kadar bilmememin eksikliklerini hissetmekteydim.
Açıkçası C veya C++ dillerinden birini de seçebilirdim ama son zamanlarda çaktırmadan yükselen bir dil olarak Rust bana göz kırpıyordu. C ve C++ kadar performanslı olmasına rağmen bu dillerdeki tuzaklara (pitfalls) sahip olmaması, beni cezbeden en büyük özelliğidir. Sistem programlama dillerinin zaten yüksek seviyeli dillerden daha zor olduğu bir gerçek iken bir de öğrenmeye çalışırken yazacağım kodlarda karşılaşacağım bellek hatalarının sebebini bulmaya çalışarak şevkimin kırılmasından kaçınmam gerekiyordu. Bu basit sebepler ile Rust öğrenmeye karar verdim ve yavaş yavaş çalışmaya başladım. 

Buraya yazdıklarımı zaman içerisinde dili daha iyi öğrendikçe değiştirip başlarken sahip olduğum yanlış kanılar varsa düzeltebilirim. Bunu bir not olarak eklemek istedim.
<!-- more -->


## Rust neden bellek güvenli bir dil?
Python ve Java gibi yüksek seviyeli dillerde bu güvenlik temizlikçi (Garbage Collector kısaca GC) ile sağlanmaktadır. Temizlikçi zaman zaman uygulama koşarken devreye girerek yok edilmesi gereken bellek alanlarını boşaltır. Bu sırada aslında koşan uygulamayı bir süre durdurarak performans kayıplarına sebep olur.

Öte yandan C ve C++ gibi alt seviye dillerde ise bütün bu işlerin yönetimi kodu yazan kişidedir. Yaratılan her obje işi bittikten sonra yok edilmeli aksi takdirde bellek kaçaklarının yaşanması gayet doğaldır. Bunun haricinde hatalı bellek boşaltmalar ile oluşabilecek hatalar da bulunmaktadır. Örnek vermek gerekirse, aynı bellek alanını iki kere yok etmek, ayırılmamış bellek alanını boşaltmak, daha önceden boşaltılmış bellek alanına okuma/yazma işlemi gerçekleştirmek gibi hataları sıralayabiliriz. Bellek hatalarının izini sürmek her zaman kolay bir iş değildir. Bu yüzden yer yer saç baş yoldurması olasıdır.

Rust ise güvenli bellek yönetimini iki konseptle kurgular. Nesne sahipliği ve ödünç verme mekanizması. 

## Sahiplik (Ownership)
Rust derleyicisi herhangi bir an için her bir değerin tek bir sahibinin olduğunun kontrolünü yapar bu sayede yarattığınız nesneyi bir başka objeye atadığınızda (normal atama ve başka fonksiyonu çağırırken argüman olarak atama) nesnenin sahipliği atanan objeye geçer ve nesneyi atadığınız kapsam içerisinde bir daha o değişkeni kullanmanızın bir imkanı kalmaz. Çünkü artık o nesnenin sahipliği atama yapılan kapsamda değildir.

``` rust
fn main() {
    let foo = String::from("Hello rust");
    let _bar = foo;
    
    println!("{}", foo);
}
```

Yukarıdaki kodda görüldüğü üzere yarattığımız ```foo``` değerini bir alt satırda yarattığımız ```_bar``` değişkenine atıyoruz. Gerçekleştirdiğimiz atama ```foo``` değerinin sahipliğinin ```_bar``` değişkenine aktarır. Bu nedenle son satırda ```foo```'nun değerini yazdırmayı denediğimizde aşağıdaki hata ile karşılaşırız.

```none
error[E0382]: use of moved value: `foo`
 --> src/main.rs:5:20
  |
3 |     let _bar = foo;
  |         ---- value moved here
4 |     
5 |     println!("{}", foo);
  |                    ^^^^ value used here after move
  |
  = note: move occurs because `foo` has type `std::string::String`, which does not implement the `Copy` trait
```
Hata mesajını incelersek yukarıda bahsettiğim durumu açıkça anlattığı görüyoruz. Ancak son satırda ilginç bir ifade yer alıyor. String tipindeki değişkenin ```Copy``` özelliği tanımlanmamış bu nedenle değişkenin sahibi değişmiş.

Burada küçük bir not düşmek gerekecek, ilkel veri tiplerinde ```Copy``` özelliğinin tanımlı olması sebebi ile yukarıdaki kodda foo değişkenini ```let foo = 22``` bu şekilde tanımlarsanız yukarıda gördüğümüz hata ile karşılaşmayız.

## Ödünç verme (Borrowing)
Ödünç verme mekanizması adından da anlaşılacağı üzere bir değerin bir objeye ya da fonksiyona ödünç verilmesidir. Bu sayede değeri ödünç alan nesne, bu değer ile işi bittiğinde bu değeri gerçek sahibine geri verir. Ancak ödünç verme işleminin iki kuralı vardır istenilen sayıda değiştirilemez (immutable) referans verilebilir ancak değiştirilebilir (mutable) referans ancak bir tane olabilir.

İlk önce değiştirilemez birden fazla referans kullanalım.
```rust
fn main() {
    let foo = String::from("Hello");
    let len = get_str_len(&foo);
    let cap = get_str_cap(&foo);
    
    println!("{}, {}", len, cap);
}

fn get_str_len(s: &String) -> usize{
    s.len()
}

fn get_str_cap(s: &String) -> usize{
    s.capacity()
}
```

```none
5, 5
```

```get_str_len``` fonksiyonu ile ```get_str_cap``` fonksiyonu argüman olarak verilen ```String``` tipindeki veriyi değiştirmedikleri için, ```foo``` değişkenini birden daha fazla ödünç verebildik.

Öte yandan benzer durumu değiştirilebilir referans ile deneyelim.


```rust
fn main() {
    let mut foo = String::from("Hello");
    let bar = add_world(&mut foo);
    let x = add_exclamation(&mut foo);
    
    println!("{}, {}", bar, x);
}

fn add_world(s: &mut String) -> &mut String{
    s.push_str(" world");
    return s
}

fn add_exclamation(s: &mut String) -> &mut String{
    s.push_str("!");
    return s
}
```

```none
error[E0499]: cannot borrow `foo` as mutable more than once at a time
 --> src/main.rs:4:34
  |
3 |     let bar = add_world(&mut foo);
  |                              --- first mutable borrow occurs here
4 |     let x = add_exclamation(&mut foo);
  |                                  ^^^ second mutable borrow occurs here
...
7 | }
  | - first borrow ends here

error: aborting due to previous error
```

Aldığımız hata değiştirilebilir referansın bir kereden fazla kullanılamaz kuralını anlatıyor.

İkinci fonksiyonu çağırmadan tekrar deneyelim.

```rust
fn main() {
    let mut foo = String::from("Hello");
    let bar = add_world(&mut foo);
    
    println!("bar: {}", bar);
}

fn add_world(s: &mut String) -> &mut String{
    s.push_str(" world");
    return s
}
```
```none
bar: Hello world
```

Bu sefer doğru şekilde çalıştı.

İnat edip iki fonksiyonu da çalıştırmak istiyorum yapabileceğim bir şey yok mu? 

Var! Yeni bir kapsam açarak aynı kapsam içerisinde değiştirilebilir referansı bir kere kullanırsam bu iş olur.

```rust
fn main() {
    let mut foo = String::from("Hello");
    {
        let bar = add_world(&mut foo);
    }
    let x = add_exclamation(&mut foo);
    println!("x: {}", x);
}

fn add_world(s: &mut String) -> &mut String{
    s.push_str(" world");
    return s
}

fn add_exclamation(s: &mut String) -> &mut String{
    s.push_str("!");
    return s
}
```
```none
x: Hello world!
```

Bu şekilde hata almadan istediğimizi elde etmeyi başardık. Yukarıdaki kodun püf noktası yeni kapsamda tanımlanan ```bar``` değişkeni kapsamdan çıktığı anda ```&mut foo``` referansını gerçek sahibine devretmesi ve daha sonra sahibin bu referansı tekrardan değiştirilebilir referans olarak kullanabilmesidir.


Aslında bu yazıyı yazmaya başladığımda bu kadar örnek ile gitmeyi düşünmüyordum. Çünkü bu yazıda sadece Rust dilinin bu iki paradigmasını anlatıp bitirecektim ancak biraz derine gitmeden bu konseptleri anlamanın biraz zor olabileceğini düşündüğüm için tahmin ettiğimden detaylı oldu. Muhtemelen sahiplik ve ödünç verme özellikleri ile ilgili farklı bir yazı yazacak olsam da bu yazıda anlattıklarımdan pek farklı olmayacaktır. Ben de bu dili yeni öğrenmeye başladığım için ileride daha detaylı ve anlaşılabilir şekilde yazabilirim. Söz vermiyorum.
